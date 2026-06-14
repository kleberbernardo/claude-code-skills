# Bug 001 — token-expira-cedo
<!-- Exemplo de referência de uma análise de causa raiz completa (modo analyze) -->
<!-- Data: 2026-06-14 -->

Status: analyzed
RootCause: confirmed
Plan: not-started
Fix: not-started

---

## 1. Compreensão

### Comportamento observado
Usuários são deslogados cerca de 15 minutos após o login, mesmo com a sessão configurada para durar 24 horas. O token JWT é rejeitado com `TokenExpiredError` antes do tempo esperado.

### Comportamento esperado
O token de acesso deve ser válido por 24 horas após a emissão, conforme a política de sessão.

**Fonte do comportamento correto:** doc interna de autenticação fornecida pelo usuário — `https://docs.interno/app/auth#sessao` (lida via WebFetch). Define `accessToken TTL = 24h`.

### Reprodução
1. Fazer login em `/api/auth/login`.
2. Aguardar ~15 min.
3. Fazer qualquer requisição autenticada.

**Entrada usada:** usuário comum, ambiente de staging.
**Frequência:** sempre.

### Evidência
```
TokenExpiredError: jwt expired
    at exports.verify (node_modules/jsonwebtoken/verify.js:152:21)
    at authMiddleware (src/auth/middleware.ts:34:25)
    at Layer.handle (node_modules/express/lib/router/layer.js:95:5)
```

### Contexto
- **Branch/versão:** main, deploy de 2026-06-12.
- **Funcionava antes?** Sim. Usuários relataram o problema após o deploy de 12/06.

---

## 2. Análise da Causa Raiz

### Reprodução no código
O login emite o token em `src/auth/token.ts`; o middleware o verifica em `src/auth/middleware.ts`. O TTL vem de `config/auth.ts`.

### Passo a passo (gatilho → divergência)

1. `src/auth/login.ts:22` — login chama `signAccessToken(user)`.
2. `src/auth/token.ts:14` — `signAccessToken` lê `authConfig.accessTokenTtl` e passa para `jwt.sign(payload, secret, { expiresIn: ttl })`.
3. `config/auth.ts:9` — **ponto de divergência**: `accessTokenTtl: process.env.ACCESS_TOKEN_TTL ?? 900`. O valor `900` (segundos = 15 min) é o fallback, e `expiresIn` numérico em `jsonwebtoken` é interpretado como **segundos**.
4. A doc pede 24h = 86400s, mas o fallback hardcoded é 900s.
5. `src/auth/middleware.ts:34` — após 900s, `jwt.verify` lança `TokenExpiredError` — o sintoma observado.

### Causa raiz
O fallback de `accessTokenTtl` em `config/auth.ts:9` é `900` (15 min), e a variável de ambiente `ACCESS_TOKEN_TTL` não está definida no ambiente de staging — então o fallback errado prevalece.

**Localização:** `config/auth.ts:9`

### Por que acontece
O commit `a1b2c3d` (deploy de 12/06) renomeou a env var de `JWT_TTL` para `ACCESS_TOKEN_TTL`, mas o `.env` de staging continuou definindo `JWT_TTL`. Sem `ACCESS_TOKEN_TTL`, o código cai no fallback `900`. Antes do commit, a var lida era `JWT_TTL=86400`, que existia — por isso funcionava.

### Causas contribuintes
- O fallback hardcoded é um valor de produção plausível porém curto, mascarando a ausência da env var em vez de falhar alto.

### Hipóteses descartadas
- ❌ Relógio do servidor adiantado (clock skew) — descartada: `iat` e `exp` no token decodificado estão a exatos 900s de distância, não há desvio de relógio (`src/auth/token.ts:14`).
- ❌ Refresh token quebrado — descartada: o erro é no access token no `authMiddleware`, antes de qualquer fluxo de refresh.

### Origem
`git blame config/auth.ts` → linha introduzida no commit `a1b2c3d` "rename JWT_TTL to ACCESS_TOKEN_TTL".

---

## 3. Lacunas e Suposições
- [ASSUMIDO: o `.env` de produção também usa `JWT_TTL` antigo] — confirmar se produção está igualmente afetada.

## 4. Observações fora de escopo
- Vários TTLs são lidos com `?? <número>` espalhados pelo config; vale padronizar e falhar alto quando ausentes — fora do escopo deste bug.
