# OAuth 2.0 e OIDC

Auditoria de implementações OAuth e OpenID Connect.

---

## Vulnerabilidades Principais

### 1. CSRF em OAuth — State Parameter Ausente
**Severidade: High**

Sem `state`, um atacante pode fazer o usuário vincular sua conta com a conta do atacante.

```javascript
// VULNERÁVEL — sem state
const authUrl = `https://provider.com/oauth/authorize?client_id=${CLIENT_ID}&redirect_uri=${REDIRECT}&response_type=code`

// SEGURO — com state
const state = crypto.randomBytes(16).toString('hex')
session.oauthState = state
const authUrl = `...&state=${state}`

// Verificar no callback
if (req.query.state !== session.oauthState) {
  throw new Error('Invalid state — possible CSRF attack')
}
```

**Buscar:**
```bash
grep -rn "oauth\|authorize\|authorization_url" --include="*.{js,ts,py}" -n | grep -v "state"
```

---

### 2. Redirect URI Validation Fraca
**Severidade: Critical**

```javascript
// VULNERÁVEL — aceita qualquer subdomínio
const ALLOWED_REDIRECTS = ['https://*.myapp.com']  // wildcard perigoso

// VULNERÁVEL — validação por contains
if (redirect_uri.includes('myapp.com')) { ... }  // bypass: evil.com/?myapp.com

// SEGURO — lista exata
const ALLOWED_REDIRECTS = [
  'https://myapp.com/auth/callback',
  'https://app.myapp.com/auth/callback',
]
if (!ALLOWED_REDIRECTS.includes(redirect_uri)) {
  throw new Error('Invalid redirect_uri')
}
```

---

### 3. PKCE Ausente (para SPAs e Mobile)
**Severidade: High**

Sem PKCE, authorization code pode ser interceptado.

```javascript
// SPA sem PKCE — INSEGURO
window.location = `https://provider.com/oauth/authorize?...&response_type=code`

// SPA com PKCE — SEGURO
const codeVerifier = generateCodeVerifier()
const codeChallenge = await generateCodeChallenge(codeVerifier)
sessionStorage.setItem('code_verifier', codeVerifier)
window.location = `...&code_challenge=${codeChallenge}&code_challenge_method=S256`
```

---

### 4. Access Token em URL
**Severidade: High**

Tokens em URLs vazam via logs de servidor, Referer header, histórico do browser.

```javascript
// VULNERÁVEL — token em URL (implicit flow)
response_type=token  // evitar em apps novas

// SEGURO — authorization code flow
response_type=code
```

---

### 5. Sem Verificação de ID Token
**Severidade: High**

```javascript
// VULNERÁVEL — usar access token como identidade
const user = await getUserInfo(accessToken)  // pode ser manipulado

// SEGURO — verificar ID token com chave pública do provider
const idToken = jwt.verify(rawIdToken, providerPublicKey, {
  audience: CLIENT_ID,
  issuer: PROVIDER_ISSUER,
  algorithms: ['RS256'],
})
```

---

## Checklist

- [ ] State parameter em todos os authorization requests
- [ ] State verificado no callback
- [ ] redirect_uri validado por lista exata (não wildcard)
- [ ] PKCE implementado para SPAs e apps mobile
- [ ] Não usar Implicit Flow (response_type=token) em apps novas
- [ ] ID Token verificado criptograficamente
- [ ] nonce verificado (anti-replay)
- [ ] Token armazenado de forma segura (não em localStorage)
- [ ] Scopes mínimos solicitados

---

## Red Flags

1. State parameter ausente no OAuth flow
2. `redirect_uri` aceita wildcard ou validação por substring
3. Access tokens em URLs (implicit flow)
4. `jwt.decode()` em vez de `jwt.verify()` para ID tokens
