# Anti-Patterns de Segurança

Padrões de código que parecem razoáveis mas são inseguros.

---

## Anti-Pattern 1: "Só para Desenvolvimento"

```javascript
// "Vou remover depois"
if (process.env.NODE_ENV !== 'production') {
  app.get('/admin/all-users', (req, res) => {
    return res.json(await User.findAll())
  })
}

// Problemas:
// 1. NODE_ENV não setado em staging = endpoint exposto
// 2. "depois" raramente acontece
// 3. Pode ser descoberto em code review ou vazamento de código
```

---

## Anti-Pattern 2: Validação Apenas no Frontend

```typescript
// Frontend valida, backend confia
// React component:
if (!/^[a-zA-Z0-9]+$/.test(username)) {
  setError('Username inválido')
  return
}
// Backend:
const user = await db.query(`SELECT * FROM users WHERE username = '${body.username}'`)
// Atacante chama o backend diretamente → SQL injection
```

---

## Anti-Pattern 3: Security by Obscurity

```javascript
// "Ninguém sabe que esse endpoint existe"
app.get('/internal-admin-abcd1234', adminHandler)

// "A rota não está no menu, então está protegida"
// Facilmente descoberto via fuzzing, code review, source maps
```

---

## Anti-Pattern 4: Whitelist de IPs como Única Proteção

```javascript
// "Só nosso IP acessa isso"
if (req.ip !== '203.0.113.5') return res.status(403).end()

// Problemas:
// - X-Forwarded-For pode ser forjado
// - IP muda em cloud/VPN
// - SSRF pode fazer o servidor agir como cliente interno
// - Não substitui autenticação real
```

---

## Anti-Pattern 5: "Já Está Autenticado, Não Preciso Verificar Autorização"

```typescript
// "Se passou pelo middleware de auth, pode tudo"
app.put('/api/users/:id', authenticate, async (req, res) => {
  const user = await User.findById(req.params.id)
  await user.update(req.body)  // sem verificar se req.user.id === req.params.id
})
```

---

## Anti-Pattern 6: Confundir HTTPS com Segurança Completa

"Está em HTTPS, então está seguro."

HTTPS protege dados em trânsito. Não previne:
- IDOR
- SQL injection
- XSS
- CSRF
- Secrets no código

---

## Anti-Pattern 7: MD5 como "Fingerprint Segura"

```javascript
// "Não é para senha, é só um hash de identificação"
const userId = md5(email)  // previsível, colisões, reversível via rainbow table

// Para IDs públicos sem segurança crítica: OK
// Para verificação de integridade: use SHA-256 com HMAC
// Para "segredo": use crypto.randomBytes(32)
```

---

## Anti-Pattern 8: eval() para Parsing de JSON

```javascript
// "É mais fácil"
const data = eval(`(${jsonString})`)

// Use JSON.parse — é seguro e rápido
const data = JSON.parse(jsonString)
```

---

## Anti-Pattern 9: Desabilitar SSL em "Ambiente de Dev"

```javascript
// "É só para não dar erro local"
process.env.NODE_TLS_REJECT_UNAUTHORIZED = '0'

// Isso frequentemente fica em produção quando o arquivo é copiado
// Permite MITM em QUALQUER conexão SSL feita pela aplicação
```

---

## Anti-Pattern 10: Logging Excessivo por Paranoia

```javascript
// "Logar tudo para poder debugar depois"
logger.info('Login attempt', { email, password, ip })
logger.debug('Request body', req.body)  // pode conter cartão, CPF

// Logs vão para Datadog, Splunk, CloudWatch — múltiplos engenheiros têm acesso
// Logs são frequentemente copiados, exportados, menos protegidos que o banco
```

---

## Anti-Pattern 11: Tokens Sem Expiração

```javascript
// "O usuário não vai querer fazer login toda vez"
jwt.sign({ userId }, secret)  // sem expiresIn
// Token válido para sempre — se comprometido, não tem como revogar
```

---

## Anti-Pattern 12: Depender de Client-Side para Controle de Preço

```javascript
// Frontend calcula o preço com base em "dados públicos"
const total = cart.items.reduce((sum, item) => sum + item.price * item.qty, 0)
// item.price vem do cliente → pode ser manipulado
```
