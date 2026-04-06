# Riscos em Vibe Coding e Low-Code

Padrões de risco específicos de projetos desenvolvidos com velocidade alta, pouca revisão e foco em resultado visual.

---

## O que é Vibe Coding

Desenvolvimento rápido guiado por:
- Prompts de AI (Cursor, GitHub Copilot, Claude Code)
- Templates e boilerplates copiados de tutoriais
- Low-code/no-code com integrações rápidas
- Foco em entregar fast, segurança como afterthought

**Resultado:** Funciona, mas frequentemente tem vulnerabilidades básicas que um desenvolvedor de segurança detectaria imediatamente.

---

## Riscos mais Comuns em Vibe Coding

### 1. Boilerplate de Autenticação Sem Hardening

Projetos que usam `create-next-app`, templates Supabase, etc. frequentemente têm:
- Auth básica funcionando
- MFA não configurado
- Rate limiting não adicionado
- Sem proteção de brute force

```bash
# Verificar configurações padrão
grep -rn "nextauth\|supabase.*auth\|clerk\|auth0" --include="*.{js,ts}" -l
# Depois verificar se há rate limiting, MFA, session hardening
```

### 2. Variáveis de Ambiente Mixadas

```bash
# NEXT_PUBLIC_ misturado com secrets privados
grep -rn "NEXT_PUBLIC_STRIPE_SECRET\|NEXT_PUBLIC_DB_\|NEXT_PUBLIC_JWT" --include="*.{env*,ts,tsx}" -n
```

Tutorial mostrou `NEXT_PUBLIC_` e dev adicionou em todos os env vars sem entender o que a prefix significa.

### 3. Prisma com Dados Abertos

```typescript
// Vibe coding frequentemente retorna o model inteiro
export async function GET(req) {
  const users = await prisma.user.findMany()  // todos os campos, todos os usuários
  return Response.json(users)  // inclui senhas hashadas, dados internos
}
```

### 4. Supabase sem RLS (Row Level Security)

Tutoriais do Supabase ensinam a fazer funcionar, mas RLS é configuração separada frequentemente esquecida.

```bash
# Verificar se usa Supabase
grep -rn "createClient\|supabase" --include="*.{js,ts}" -l
# Se sim, perguntar/verificar se RLS está habilitado
```

### 5. API Routes Next.js sem Autenticação

```typescript
// Vibe: "deixa eu criar uma rota rápida para testar"
// app/api/debug/route.ts — esquece de remover ou proteger
export async function GET() {
  const users = await prisma.user.findMany()
  return Response.json(users)  // sem autenticação
}
```

```bash
find . -path "*/app/api/*" -name "route.ts" -o -name "route.js" | xargs grep -L "auth\|session\|token\|middleware" 2>/dev/null
```

### 6. .env Commitado por Descuido

```bash
git ls-files | grep "\.env"
git log --all --full-history --oneline -- "*.env" 2>/dev/null
```

### 7. Dependências Excessivas

```bash
# Vibe coding tende a adicionar muitas dependências
cat package.json | python3 -c "
import json,sys
p=json.load(sys.stdin)
deps = {**p.get('dependencies',{}), **p.get('devDependencies',{})}
print(f'Total dependencies: {len(deps)}')
"
# Mais de 100 dependências diretas = alto risco de supply chain
```

### 8. CORS Aberto "Para Funcionar"

```javascript
// Developer adicionou porque o browser estava bloqueando
app.use(cors())  // abre para todos
// ou
Access-Control-Allow-Origin: *
```

### 9. Debug Console.logs com Dados Sensíveis

```bash
grep -rn "console\.log\b" --include="*.{ts,js}" -n | grep -i "user\|password\|token\|email\|cpf\|carta" | head -20
```

Vibe coding frequentemente tem `console.log(user)`, `console.log(token)` deixados no código.

### 10. Sem Environment Separation

```bash
grep -rn "NODE_ENV\|VERCEL_ENV\|APP_ENV" --include="*.{ts,js,json}" | grep "production\|staging\|development"
```

Um único `.env` para tudo, sem separação dev/staging/prod.

---

## Checklist Específico para Vibe Coding

Além do checklist padrão, verificar adicionalmente:

- [ ] Remover todas as rotas de debug criadas durante desenvolvimento
- [ ] Verificar se NEXT_PUBLIC_ prefix não está em secrets
- [ ] Confirmar que RLS está habilitado se usa Supabase
- [ ] Verificar console.logs com dados sensíveis
- [ ] Confirmar que `.env` está no `.gitignore`
- [ ] Verificar se CORS foi aberto "temporariamente" e ficou
- [ ] Confirmar que todos os endpoints de API têm autenticação
- [ ] Verificar se boilerplate de auth foi customizado para produção

---

## Frase para o Relatório

> "Este projeto apresenta padrões típicos de desenvolvimento ágil assistido por AI (vibe coding), onde a velocidade de entrega priorizou funcionalidade sobre segurança. As vulnerabilidades encontradas são comuns neste tipo de projeto e corrigíveis, mas requerem atenção antes do lançamento em produção com dados reais de usuários."
