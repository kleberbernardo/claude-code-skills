# Rate Limiting e Proteção contra Abuso

Ausência de rate limiting permite brute force, scraping, DDoS e abuso de API.

---

## Endpoints Críticos que DEVEM ter Rate Limiting

| Endpoint | Risco sem rate limit | Limite recomendado |
|----------|---------------------|-------------------|
| POST /auth/login | Brute force de senhas | 5-10 tentativas/15min por IP |
| POST /auth/register | Criação de contas em massa | 3-5 registros/hora por IP |
| POST /auth/forgot-password | Spam de email | 3-5 requests/hora por email |
| POST /auth/verify-otp | Brute force OTP | 5 tentativas/15min |
| GET /api/* (público) | Scraping, DDoS | 60-100 req/min por IP |
| POST /api/* (ações) | Abuso de funcionalidade | 30-60 req/min por usuário |
| Endpoints de envio (email, SMS) | Spam e custo | Muito restrito |
| Endpoints de IA/LLM | Custo, abuso | Por token/crédito |

---

## Busca

```bash
# Verificar se há rate limiting configurado
grep -rn "rateLimit\|rate-limit\|express-rate-limit\|@nestjs/throttler\|slowDown\|bottleneck\|limiter" --include="*.{js,ts,py,go}" -l

# Verificar se está aplicado em endpoints de auth
grep -rn "login\|signin\|register\|forgot" --include="*.{js,ts}" -l | xargs grep -l "rateLimit\|limiter\|throttle"

# FastAPI/Python
grep -rn "slowapi\|limits\|flask_limiter\|@limiter" --include="*.py" -l
```

---

## Verificação de Cobertura

```bash
# Listar todos os endpoints
grep -rn "app\.\(get\|post\|put\|patch\|delete\)\|router\.\(get\|post\|put\|patch\|delete\)" --include="*.{js,ts}" -n

# Verificar se cada endpoint tem rate limit ou está coberto por middleware global
grep -B 20 "\.post\s*(\s*['\"].*login\|\.post\s*(\s*['\"].*signin" --include="*.{js,ts}" | grep "rateLimit\|limiter"
```

---

## Implementação Correta

```javascript
// Express — rate limiting por endpoint
import rateLimit from 'express-rate-limit'

const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,  // 15 minutos
  max: 10,                    // máximo 10 tentativas
  message: { error: 'Too many login attempts, try again in 15 minutes' },
  standardHeaders: true,
  legacyHeaders: false,
  // Usar IP + email para maior efetividade
  keyGenerator: (req) => `${req.ip}:${req.body?.email || 'unknown'}`,
})

app.post('/api/auth/login', loginLimiter, loginHandler)

// Rate limit global para API
const globalLimiter = rateLimit({
  windowMs: 60 * 1000,  // 1 minuto
  max: 100,              // 100 requests por minuto
})
app.use('/api/', globalLimiter)
```

---

## Checklist

- [ ] Rate limit em endpoint de login (por IP e por email/usuário)
- [ ] Rate limit em endpoint de registro
- [ ] Rate limit em forgot-password e verify-otp
- [ ] Rate limit global na API pública
- [ ] Headers de rate limit retornados (X-RateLimit-*)
- [ ] Resposta 429 com mensagem adequada e Retry-After
- [ ] Rate limit não bypassável via headers (X-Forwarded-For)

---

## Red Flags

1. Sem nenhum rate limiting em endpoint de login
2. Rate limiting apenas no frontend (JavaScript)
3. Rate limiting bypassável via `X-Forwarded-For: 127.0.0.1`
4. Sem proteção em endpoint de forgot-password (spam de emails)
