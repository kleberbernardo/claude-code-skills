# JWT — JSON Web Tokens

Auditoria completa de implementações JWT. Uma das áreas com mais vulnerabilidades em projetos modernos.

---

## Vulnerabilidades Críticas

### 1. Algoritmo `none` (CVE-2015-9235)
**Severidade: Critical | Confiança: Alta se encontrado**

O JWT com `alg: none` não tem assinatura. Qualquer um pode criar um token válido.

```javascript
// VULNERÁVEL — aceitar alg: none
jwt.verify(token, secret)  // sem especificar algorithms
jwt.verify(token, '', { algorithms: ['none'] })

// SEGURO
jwt.verify(token, secret, { algorithms: ['HS256'] })
jwt.verify(token, publicKey, { algorithms: ['RS256'] })
```

**Buscar:**
```bash
grep -rn "jwt\.verify\|jwt\.decode" --include="*.{js,ts}" | grep -v "algorithms:"
grep -rn "algorithms.*none\|alg.*none" --include="*.{js,ts,py,go}"
```

---

### 2. Confusão de Algoritmo RS256 → HS256
**Severidade: Critical**

Se o servidor usa RS256 (chave pública/privada) mas aceita HS256, um atacante pode:
1. Pegar a chave pública (geralmente exposta)
2. Criar um token HS256 assinado com a chave pública como secret

```javascript
// VULNERÁVEL — aceita múltiplos algoritmos sem restrição
jwt.verify(token, key)  // key pode ser pública ou HMAC secret

// SEGURO — algoritmo fixo
jwt.verify(token, publicKey, { algorithms: ['RS256'] })
```

---

### 3. Secret Fraco
**Severidade: Critical**

Secrets curtos ou previsíveis podem ser quebrados por brute force offline.

```bash
# Buscar secrets hardcoded ou fracos
grep -rn "JWT_SECRET\s*=\s*['\"]" --include="*.{js,ts,py,env}"
grep -rn "jwt.*secret.*[=:]\s*['\"][a-zA-Z0-9]{1,20}['\"]" --include="*.{js,ts,py}"
```

**Red flags:**
```javascript
const JWT_SECRET = 'secret'       // CRITICAL
const JWT_SECRET = '123456'       // CRITICAL
const JWT_SECRET = 'mysecretkey'  // CRITICAL
const JWT_SECRET = 'your-secret-key-here'  // placeholder não substituído
```

**Mínimo seguro:** 32 bytes aleatórios de alta entropia
```bash
# Gerar secret seguro
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

---

### 4. Sem Verificação de Expiração
**Severidade: High**

```javascript
// VULNERÁVEL — apenas decodifica sem verificar
const payload = jwt.decode(token)  // NÃO verifica assinatura

// VULNERÁVEL — ignora expiração
jwt.verify(token, secret, { ignoreExpiration: true })

// SEGURO
jwt.verify(token, secret, { algorithms: ['HS256'] })
// expiração verificada automaticamente
```

---

### 5. Sem Validação de Claims
**Severidade: High**

```javascript
// VULNERÁVEL — não valida audience e issuer
const payload = jwt.verify(token, secret)

// SEGURO
const payload = jwt.verify(token, secret, {
  algorithms: ['HS256'],
  audience: 'https://myapp.com',
  issuer: 'https://auth.myapp.com',
})
```

---

### 6. Token em localStorage
**Severidade: High**

Tokens em localStorage são acessíveis via XSS. Um script injetado pode roubar o token.

```javascript
// VULNERÁVEL
localStorage.setItem('token', jwt)

// MELHOR — HttpOnly cookie (inacessível via JS)
// Set-Cookie: token=...; HttpOnly; Secure; SameSite=Strict
```

---

### 7. Ausência de Revogação
**Severidade: Medium**

JWTs são stateless — se comprometido, continua válido até expirar.

**Verificar:**
- Existe mecanismo de blacklist/denylist de tokens?
- Logout invalida o token de alguma forma?
- Expiração é curta (≤ 1 hora para access token)?

---

## Checklist Completo

- [ ] Algoritmo especificado explicitamente na verificação
- [ ] Algoritmo `none` não aceito
- [ ] Secret com 32+ bytes de entropia alta
- [ ] Secret não hardcoded (vem de variável de ambiente)
- [ ] Expiração definida (`exp` claim)
- [ ] Access token com vida curta (15-60 min)
- [ ] Refresh token com vida mais longa e rotação
- [ ] Issuer (`iss`) validado
- [ ] Audience (`aud`) validada
- [ ] Tokens de autenticação em HttpOnly cookies, não localStorage
- [ ] Logout invalida o token (blacklist ou short-lived)
- [ ] Nenhum dado sensível no payload (senha, cartão, SSN)

---

## Boas Práticas

```javascript
// Node.js — implementação segura
import jwt from 'jsonwebtoken'

const JWT_SECRET = process.env.JWT_SECRET  // 32+ bytes de env var
const JWT_ISSUER = 'https://api.myapp.com'
const JWT_AUDIENCE = 'https://myapp.com'

// Gerar token
function generateToken(userId: string): string {
  return jwt.sign(
    { sub: userId },
    JWT_SECRET,
    {
      algorithm: 'HS256',
      expiresIn: '15m',
      issuer: JWT_ISSUER,
      audience: JWT_AUDIENCE,
    }
  )
}

// Verificar token
function verifyToken(token: string): JwtPayload {
  return jwt.verify(token, JWT_SECRET, {
    algorithms: ['HS256'],
    issuer: JWT_ISSUER,
    audience: JWT_AUDIENCE,
  }) as JwtPayload
}
```

---

## Casos Reais de Falha

**Auth0 2019:** Vulnerabilidade de confusion attack entre RS256/HS256 afetou múltiplas aplicações que não fixavam o algoritmo.

**Caso comum em vibe coding:** AI gera `jwt.verify(token, secret)` sem especificar `algorithms`, deixando a aplicação vulnerável ao ataque de algorithm confusion.
