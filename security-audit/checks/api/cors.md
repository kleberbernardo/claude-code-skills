# CORS — Cross-Origin Resource Sharing

Configuração incorreta de CORS é uma das vulnerabilidades mais comuns em APIs.

---

## Vulnerabilidades Críticas

### 1. Origin Refletido com Credentials
**Severidade: Critical**

Se o servidor reflete o Origin do request E permite credentials, qualquer site pode fazer requests autenticados.

```javascript
// CRÍTICO — reflete origin com credentials
app.use((req, res, next) => {
  res.setHeader('Access-Control-Allow-Origin', req.headers.origin)  // reflete!
  res.setHeader('Access-Control-Allow-Credentials', 'true')
  next()
})

// CRÍTICO — wildcard com credentials (inválido mas código que tenta)
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

**Buscar:**
```bash
grep -rn "Allow-Origin.*req\.\|Allow-Origin.*origin\b" --include="*.{js,ts,py}" -n
grep -rn "origins.*\*\|allowOrigin.*\*" --include="*.{js,ts,py}" -n | grep -i "credentials.*true"
```

---

### 2. Wildcard em API Privada
**Severidade: High**

```javascript
// API que requer autenticação mas tem CORS aberto
Access-Control-Allow-Origin: *
// Sem credentials=true é menos crítico, mas ainda problemático
// para APIs que usam cookies ou que não deveriam ser acessadas de outros domínios
```

---

### 3. Validação de Origin Fraca
**Severidade: High**

```javascript
// VULNERÁVEL — endsWith fraco
if (origin.endsWith('myapp.com')) {
  res.setHeader('Access-Control-Allow-Origin', origin)
}
// Bypass: evilmyapp.com

// VULNERÁVEL — includes
if (origin.includes('myapp.com')) {
  res.setHeader('Access-Control-Allow-Origin', origin)
}
// Bypass: myapp.com.evil.com

// SEGURO — lista exata
const ALLOWED_ORIGINS = ['https://myapp.com', 'https://app.myapp.com']
if (ALLOWED_ORIGINS.includes(origin)) {
  res.setHeader('Access-Control-Allow-Origin', origin)
}
```

---

## Configuração Correta

```javascript
// Express com cors package
import cors from 'cors'

const corsOptions = {
  origin: (origin, callback) => {
    const allowedOrigins = [
      'https://myapp.com',
      'https://app.myapp.com',
    ]
    if (!origin || allowedOrigins.includes(origin)) {
      callback(null, true)
    } else {
      callback(new Error('Not allowed by CORS'))
    }
  },
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'OPTIONS'],
  allowedHeaders: ['Content-Type', 'Authorization'],
}

app.use(cors(corsOptions))
```

---

## Checklist

- [ ] `Access-Control-Allow-Origin` não reflete o origin do request
- [ ] `Access-Control-Allow-Origin: *` não usado com `credentials: true`
- [ ] Lista de origins permitidos é explícita e usa comparação exata
- [ ] Métodos permitidos são apenas os necessários
- [ ] Headers expostos são apenas os necessários
- [ ] Preflight OPTIONS responde corretamente

---

## Red Flags

1. `res.setHeader('Access-Control-Allow-Origin', req.headers.origin)` + credentials=true
2. `origin: '*'` em API que usa cookies ou JWT em headers
3. Validação de origin por `includes()` ou `endsWith()`
4. CORS habilitado para todos os endpoints incluindo admin
