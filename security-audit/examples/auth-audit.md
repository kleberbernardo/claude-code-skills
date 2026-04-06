# Exemplo: Auth Audit

Foco em autenticação e autorização.

---

## Invocação

```
/security-audit auth
```

## Checklist Executado

1. Detectar biblioteca de auth usada (NextAuth, Passport, Supabase Auth, etc.)
2. Verificar hashing de senhas
3. Verificar JWT (algoritmo, secret, expiração)
4. Verificar rate limiting em login/register/reset
5. Verificar session fixation
6. Verificar OAuth (state, PKCE, redirect_uri)
7. Verificar MFA disponibilidade
8. Verificar IDOR em todos os endpoints com IDs

## Exemplo de Saída

```markdown
# AUTH SECURITY AUDIT — NextAuthApp

## Stack Detectada
- Framework: Next.js 14 App Router
- Auth: NextAuth.js v4 (next-auth)
- Banco: PostgreSQL via Prisma

## 🔴 CRITICAL

### [AUTH-001] JWT Secret Fraco
- Arquivo: `.env:3` — NEXTAUTH_SECRET="mysecret"
- "mysecret" tem apenas 8 chars → brute force offline viável
- Fix: gerar com `openssl rand -base64 32`

## 🟠 HIGH

### [AUTH-002] Sem Rate Limiting em /api/auth/signin
- NextAuth expõe POST /api/auth/signin sem rate limiting
- Permite brute force ilimitado
- Fix: middleware de rate limiting ou edge rate limiting

### [AUTH-003] IDOR em /api/users/[id]/profile
- Sem verificação de ownership: qualquer usuário vê qualquer perfil
- Fix: verificar session.user.id === params.id

## 🟡 MEDIUM

### [AUTH-004] Cookies sem SameSite: Strict
- NextAuth usa SameSite: Lax por padrão
- Recomendado: Strict para aplicações sem OAuth cross-origin

## ✅ OK
- Bcrypt com saltRounds=10 (poderia ser 12)
- HttpOnly e Secure nos cookies (via NextAuth default)
- Argon2id disponível como upgrade
```
