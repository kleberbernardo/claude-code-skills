# Padrões de Vulnerabilidades do Mundo Real

Casos reais e padrões que aparecem repetidamente em auditorias.

---

## Padrão 1: .env em Repositório Público

**Frequência:** Extremamente comum
**Impacto:** Critical

**Como acontece:**
1. Dev cria `.env` localmente
2. Esquece de adicionar ao `.gitignore`
3. Faz `git add .` e commita
4. Repositório é tornando público (ou era público desde o início)

**Caso real:** Em 2023, pesquisadores encontraram mais de 100.000 repositórios GitHub com arquivos `.env` contendo secrets ativos.

**Detecção:**
```bash
find . -name ".env" -not -path "*/node_modules/*"
git ls-files | grep "\.env"
git log --all --full-history -- "*.env" 2>/dev/null
```

---

## Padrão 2: JWT sem Verificação de Algoritmo

**Frequência:** Muito comum em projetos com AI/vibe coding
**Impacto:** Critical

**Como acontece:**
AI gera `jwt.verify(token, secret)` sem especificar `algorithms`, deixando vulnerável a algorithm confusion attack.

**Detecção:**
```bash
grep -rn "jwt\.verify\b" --include="*.{js,ts}" | grep -v "algorithms:"
```

**Exploração:**
1. Pegar a chave pública (geralmente exposta em JWKS endpoint)
2. Criar JWT forjado assinado com HS256 usando a chave pública como HMAC secret
3. Se servidor aceitar, autenticação bypassada

---

## Padrão 3: IDOR em SaaS Brasileiro

**Frequência:** Muito comum
**Impacto:** High a Critical

**Como acontece:**
```typescript
// AI gerou essa rota sem ownership check
app.get('/api/faturas/:id', authenticate, async (req, res) => {
  const fatura = await prisma.fatura.findUnique({ where: { id: req.params.id } })
  return res.json(fatura)
})
// Qualquer usuário autenticado pode ver qualquer fatura
```

**Detecção:**
```bash
grep -rn "findUnique\|findById\|findOne" --include="*.{js,ts}" -A 5 | grep -v "userId\|user_id\|ownerId"
```

---

## Padrão 4: Supabase sem RLS

**Frequência:** Muito comum em projetos Next.js + Supabase
**Impacto:** Critical

**Como acontece:**
1. Dev usa Supabase com anon key no frontend
2. Consulta direto ao banco via Supabase client
3. Sem RLS, qualquer usuário autenticado lê todas as tabelas

**Detecção:**
```bash
grep -rn "supabase\.from\|createClient" --include="*.{js,ts,jsx,tsx}" -l
# Verificar policies no banco via Supabase dashboard
```

---

## Padrão 5: Rate Limit Ausente no Login

**Frequência:** Extremamente comum
**Impacto:** High

**Como acontece:**
```typescript
// Endpoint de login sem rate limit
app.post('/api/auth/login', async (req, res) => {
  const user = await User.findOne({ email: req.body.email })
  if (!user || !bcrypt.compareSync(req.body.password, user.password)) {
    return res.status(401).json({ error: 'Invalid credentials' })
  }
  // ...
})
// Sem rate limit → brute force ilimitado
```

---

## Padrão 6: Preço do Frontend em Checkout

**Frequência:** Comum em e-commerce gerado por AI
**Impacto:** Critical (perda financeira direta)

```javascript
// VULNERÁVEL — price vem do cliente
const { productId, price } = req.body  // atacante manda price: 0.01
await stripe.paymentIntents.create({ amount: price * 100 })
```

---

## Padrão 7: Stack Trace em Produção

**Frequência:** Muito comum
**Impacto:** Medium (facilita outros ataques)

```javascript
// Express default error handler em NODE_ENV=production sem override
app.get('/users/:id', async (req, res) => {
  const user = await db.query(`SELECT * FROM users WHERE id = ${req.params.id}`)
  // Query falha → stack trace com query SQL exposta na resposta
})
```

---

## Padrão 8: GitHub Actions com pull_request_target

**Frequência:** Crescendo (mais GitHub Actions em projetos)
**Impacto:** Critical (RCE em CI/CD + acesso a secrets)

Ver `checks/ci-cd/github-actions.md`.

---

## Padrão 9: Secrets em docker-compose.yml

**Frequência:** Comum
**Impacto:** Critical se repositório for comprometido

```yaml
environment:
  POSTGRES_PASSWORD: minha_senha_123  # hardcoded
  STRIPE_SECRET_KEY: sk_live_abc123   # CRÍTICO
```

---

## Padrão 10: SQL Injection via ORDER BY

**Frequência:** Subestimado
**Impacto:** High

```javascript
// Parâmetro de ordenação vai direto para SQL
const { sortBy } = req.query
const results = await db.query(`SELECT * FROM products ORDER BY ${sortBy}`)
// Payload: "price; DROP TABLE products--"
// Ou: "(SELECT CASE WHEN (1=1) THEN price ELSE name END)"  — blind SQLi
```
