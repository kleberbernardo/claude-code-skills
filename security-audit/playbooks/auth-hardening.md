# Playbook: Hardening de Autenticação

Guia para fortalecer autenticação em sistemas existentes.

---

## Prioridade 1: Hashing de Senhas

```bash
# 1. Verificar algoritmo atual
grep -rn "bcrypt\|argon2\|md5\|sha1\|sha256" --include="*.{js,ts,py}" -n

# 2. Se MD5/SHA1 → migração necessária
```

**Plano de migração de MD5/SHA1 para bcrypt:**
```javascript
// Na verificação de login, detectar e migrar automaticamente
async function verifyAndMigratePassword(inputPassword, storedHash) {
  // Detectar se é hash antigo (MD5 = 32 chars, SHA1 = 40 chars)
  const isLegacyMd5 = /^[a-f0-9]{32}$/.test(storedHash)
  const isLegacySha1 = /^[a-f0-9]{40}$/.test(storedHash)
  
  if (isLegacyMd5) {
    const md5Hash = crypto.createHash('md5').update(inputPassword).digest('hex')
    if (md5Hash !== storedHash) return false
    // Migrar para bcrypt
    const bcryptHash = await bcrypt.hash(inputPassword, 12)
    await User.updateHash(userId, bcryptHash)
    return true
  }
  
  return bcrypt.compare(inputPassword, storedHash)
}
```

---

## Prioridade 2: Rate Limiting

```typescript
// Implementação progressiva de rate limiting
import rateLimit from 'express-rate-limit'
import slowDown from 'express-slow-down'

// Rate limit estrito para login
const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,  // 15 minutos
  max: 10,
  keyGenerator: (req) => `login:${req.ip}:${req.body?.email || ''}`,
  message: { error: 'Muitas tentativas de login. Tente novamente em 15 minutos.' },
})

// Slow down progressivo (opcional, para melhor UX)
const loginSlowDown = slowDown({
  windowMs: 15 * 60 * 1000,
  delayAfter: 3,
  delayMs: (attempts) => attempts * 500,  // 500ms por tentativa extra
})

app.post('/api/auth/login', loginSlowDown, loginLimiter, loginHandler)
```

---

## Prioridade 3: JWT Hardening

```typescript
// 1. Adicionar algoritmo explícito
// Antes:
jwt.verify(token, secret)
// Depois:
jwt.verify(token, secret, { algorithms: ['HS256'] })

// 2. Adicionar expiração se ausente
// Antes:
jwt.sign({ userId }, secret)
// Depois:
jwt.sign({ userId }, secret, { algorithm: 'HS256', expiresIn: '15m' })

// 3. Verificar força do secret (mínimo 32 bytes)
console.log(Buffer.byteLength(process.env.JWT_SECRET, 'utf8'))
// Se < 32, gerar novo: node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

---

## Prioridade 4: Cookies Seguros

```typescript
// Se usando JWT em cookie (recomendado):
res.cookie('accessToken', token, {
  httpOnly: true,
  secure: process.env.NODE_ENV === 'production',
  sameSite: 'strict',
  maxAge: 15 * 60 * 1000,
})

// Se usando localStorage → migrar para cookie HttpOnly
// (mudança requer atualizar frontend também)
```

---

## Prioridade 5: Session Fixation Prevention

```javascript
// Após login bem-sucedido, regenerar session ID
req.session.regenerate((err) => {
  if (err) return next(err)
  req.session.userId = user.id
  next()
})
```

---

## Checklist de Hardening

- [ ] Bcrypt/argon2 para novas senhas
- [ ] Plano de migração para senhas com hash fraco existentes
- [ ] Rate limiting em login, registro e reset de senha
- [ ] JWT com algoritmo explícito e expiração curta
- [ ] Cookies com HttpOnly, Secure e SameSite
- [ ] Session fixation prevenida
- [ ] Password reset com token de alta entropia e expiração
- [ ] MFA disponível para usuários (obrigatório para admins)
