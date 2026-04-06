# OWASP API Security Top 10 — 2023

Específico para APIs REST, GraphQL, gRPC e similares.

---

## API1:2023 — Broken Object Level Authorization (BOLA)
**Equivalente ao IDOR**

Todo endpoint que acessa um objeto por ID deve verificar autorização.

**Buscar:** `checks/authorization/idor.md`

---

## API2:2023 — Broken Authentication

JWTs sem verificação, tokens sem expiração, brute force em tokens.

**Buscar:** `checks/authentication/jwt.md`, `checks/authentication/passwords.md`

---

## API3:2023 — Broken Object Property Level Authorization (BOPLA)
**Combina Mass Assignment + Excessive Data Exposure**

**Mass Assignment:**
```javascript
// VULNERÁVEL — campos sensíveis podem ser modificados
await User.update({ where: { id } }, req.body)
// Payload: { "role": "admin", "isAdmin": true }

// SEGURO — campos explícitos
await User.update({ where: { id } }, {
  name: req.body.name,
  email: req.body.email,
  // role e isAdmin não são atualizáveis pelo usuário
})
```

**Excessive Data Exposure:**
```javascript
// VULNERÁVEL — retorna tudo
const user = await User.findById(id)
return res.json(user)  // inclui password_hash, internal_id, etc.

// SEGURO — selecionar apenas campos necessários
const user = await User.findById(id, { select: { name, email, createdAt } })
return res.json(user)
```

**Buscar:**
```bash
grep -rn "\.update\s*(\s*{.*req\.body\|\.create\s*(\s*req\.body\b" --include="*.{js,ts}" -n
grep -rn "res\.json\s*(\s*\w\+\s*)" --include="*.{js,ts}" | grep -v "select\|pick\|omit\|fields"
```

---

## API4:2023 — Unrestricted Resource Consumption

Rate limiting ausente em APIs que consomem recursos.

**Verificar:**
- Sem limite de tamanho de request
- Sem rate limiting
- Queries sem LIMIT
- Operações de alto custo sem throttling

**Buscar:** `checks/api/rate-limit.md`

---

## API5:2023 — Broken Function Level Authorization

Endpoints admin acessíveis por usuários comuns.

```bash
grep -rn "\"admin\"\|'/admin'\|/manage/\|/internal/" --include="*.{js,ts,py}" | grep "route\|path\|app\.\|router\." | grep -v "requireAdmin\|isAdmin\|adminOnly\|checkRole"
```

---

## API6:2023 — Unrestricted Access to Sensitive Business Flows

Automação de fluxos que deveriam ser humanos:
- Comprar todos os ingressos de show em bot
- Criar contas spam em escala
- Abusar de sistema de referral

---

## API7:2023 — Server Side Request Forgery (SSRF)

Ver OWASP A10:2021 acima.

---

## API8:2023 — Security Misconfiguration

APIs com:
- CORS aberto
- Stack traces em erro
- Métodos HTTP não necessários habilitados
- Debug endpoints ativos

---

## API9:2023 — Improper Inventory Management

APIs não documentadas ou esquecidas:
- Versões antigas da API ainda ativas (v1, v2, v3...)
- Endpoints de debug não removidos
- APIs de terceiros integradas sem segurança

```bash
grep -rn "v1\|v2\|v3\|/old\|/legacy\|/deprecated" --include="*.{js,ts,py}" | grep "route\|path\|endpoint" -i
```

---

## API10:2023 — Unsafe Consumption of APIs

Quando sua API consome APIs de terceiros:
- Sem validação do response
- SSL não verificado
- Dados do terceiro usados diretamente sem sanitização

```bash
grep -rn "rejectUnauthorized.*false\|verify.*false\|ssl.*false" --include="*.{js,ts,py}" -n
grep -rn "response\.data\b\|res\.body\b" --include="*.{js,ts}" | grep -v "validate\|schema\|parse" | head -20
```
