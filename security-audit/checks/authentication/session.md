# Gerenciamento de Sessão

Auditoria de cookies de sessão, geração de IDs, fixação e proteção.

---

## Vulnerabilidades Principais

### 1. Session Fixation
**Severidade: High**

Atacante define um session ID antes do login. Após o usuário logar, o atacante usa o mesmo ID.

```python
# VULNERÁVEL — não regenera session após login
def login(request):
    user = authenticate(request.POST['username'], request.POST['password'])
    request.session['user_id'] = user.id  # usa session existente

# SEGURO — regenerar session ID após autenticação
def login(request):
    user = authenticate(request.POST['username'], request.POST['password'])
    request.session.cycle_key()  # Django
    # ou
    session.regenerate()  # outros frameworks
    session['user_id'] = user.id
```

**Buscar:**
```bash
grep -rn "session\[.*\]\s*=\s*user\|session\.set\|session\.put" --include="*.{py,rb,php,js,ts}" | grep -v "regenerate\|cycle\|new_session"
```

---

### 2. Cookies sem HttpOnly
**Severidade: High**

Sem `HttpOnly`, JavaScript pode acessar o cookie → XSS rouba sessão.

```javascript
// VULNERÁVEL
res.cookie('sessionId', token)
res.cookie('sessionId', token, { httpOnly: false })

// SEGURO
res.cookie('sessionId', token, {
  httpOnly: true,
  secure: true,
  sameSite: 'strict',
  maxAge: 3600000,
})
```

**Buscar:**
```bash
grep -rn "res\.cookie\|set-cookie\|setCookie" --include="*.{js,ts}" | grep -v "httpOnly.*true\|HttpOnly"
grep -rn "SESSION_COOKIE_HTTPONLY\s*=\s*False" --include="*.py"  # Django
```

---

### 3. Cookies sem Secure Flag
**Severidade: High**

Sem `Secure`, cookie é enviado em HTTP não criptografado → MITM captura.

```bash
grep -rn "res\.cookie\|set-cookie" --include="*.{js,ts}" | grep -v "secure.*true\|Secure"
grep -rn "SESSION_COOKIE_SECURE\s*=\s*False" --include="*.py"  # Django
```

---

### 4. SameSite Ausente ou Permissivo
**Severidade: Medium**

`SameSite=None` sem `Secure` ou ausência de SameSite permite CSRF.

```javascript
// VULNERÁVEL
res.cookie('session', token, { sameSite: 'none' })

// SEGURO para a maioria das aplicações
res.cookie('session', token, { sameSite: 'strict' })
// ou para apps com OAuth/cross-origin
res.cookie('session', token, { sameSite: 'lax' })
```

---

### 5. Session ID Previsível
**Severidade: Critical**

Session IDs devem ser gerados por CSPRNG (Cryptographically Secure Pseudo-Random Number Generator).

```python
# VULNERÁVEL
import random
session_id = str(random.randint(1000000, 9999999))  # previsível

# SEGURO
import secrets
session_id = secrets.token_urlsafe(32)  # 32 bytes de entropia
```

```javascript
// VULNERÁVEL
const sessionId = Math.random().toString(36)  # Math.random não é CSPRNG

// SEGURO
import crypto from 'crypto'
const sessionId = crypto.randomBytes(32).toString('hex')
```

---

### 6. Expiração de Sessão
**Severidade: Medium**

Sessões que não expiram permitem que tokens roubados ou esquecidos sejam usados indefinidamente.

**Verificar:**
- Sessão expira após inatividade
- Sessão tem expiração absoluta
- Logout invalida a sessão no servidor

---

### 7. Session ID em URL
**Severidade: High**

Session ID na URL aparece em logs, histórico do browser, Referer header.

```bash
grep -rn "JSESSIONID\|session.*url\|sessionid.*url\|phpsessid" --include="*.{js,ts,py,java,php}" -i
```

---

## Checklist

- [ ] Session ID regenerado após login (session fixation prevention)
- [ ] Cookie com `HttpOnly: true`
- [ ] Cookie com `Secure: true`
- [ ] Cookie com `SameSite: Strict` ou `Lax`
- [ ] Session ID com entropia adequada (≥ 128 bits)
- [ ] Session expira por inatividade (ex: 30 min)
- [ ] Session expira absolutamente (ex: 24h máximo)
- [ ] Logout destrói a sessão no servidor
- [ ] Session ID nunca aparece em URLs
- [ ] Múltiplas sessões concorrentes controladas (opcional mas bom)

---

## Verificação de Frameworks

### Express.js (express-session)
```javascript
app.use(session({
  secret: process.env.SESSION_SECRET,  // verificar força do secret
  resave: false,
  saveUninitialized: false,
  cookie: {
    httpOnly: true,
    secure: process.env.NODE_ENV === 'production',
    sameSite: 'strict',
    maxAge: 30 * 60 * 1000,  // 30 min
  },
}))
```

### Django
```python
# settings.py
SESSION_COOKIE_HTTPONLY = True      # default é True
SESSION_COOKIE_SECURE = True        # verificar!
SESSION_COOKIE_SAMESITE = 'Strict'  # verificar!
SESSION_COOKIE_AGE = 1800           # 30 min
```

### Next.js (iron-session / NextAuth)
```javascript
// Verificar configuração de cookies em next-auth
cookies: {
  sessionToken: {
    options: {
      httpOnly: true,
      sameSite: 'strict',
      path: '/',
      secure: true,
    }
  }
}
```
