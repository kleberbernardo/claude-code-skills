# Riscos em Código Gerado por IA

Padrões específicos de vulnerabilidades que aparecem repetidamente em código gerado por LLMs.

---

## Por que Código AI tem Riscos Específicos

1. **Otimiza para funcionalidade, não segurança** — o código mais simples que funciona geralmente omite verificações de segurança
2. **Treinado em código inseguro** — grande parte do código de treinamento tem vulnerabilidades
3. **Sem contexto de produção** — não sabe se é para ambiente interno ou público
4. **Tutoriais são mais comuns que produção** — aprende padrões de blog posts simplificados
5. **Não faz threat modeling** — não pensa como um atacante

---

## Top 10 Vulnerabilidades Mais Geradas por AI

### 1. IDOR — Falta de Ownership Check

```typescript
// AI GERA (INSEGURO):
app.get('/api/documents/:id', authenticate, async (req, res) => {
  const doc = await prisma.document.findUnique({ where: { id: req.params.id } })
  return res.json(doc)  // sem verificar se pertence ao usuário
})

// CORRETO:
app.get('/api/documents/:id', authenticate, async (req, res) => {
  const doc = await prisma.document.findFirst({
    where: { id: req.params.id, userId: req.user.id }
  })
  if (!doc) return res.status(404).end()
  return res.json(doc)
})
```

### 2. JWT sem Algoritmo Especificado

```javascript
// AI GERA (INSEGURO):
const payload = jwt.verify(token, process.env.JWT_SECRET)

// CORRETO:
const payload = jwt.verify(token, process.env.JWT_SECRET, { algorithms: ['HS256'] })
```

### 3. Mass Assignment

```typescript
// AI GERA (INSEGURO):
app.put('/api/users/:id', authenticate, async (req, res) => {
  const user = await prisma.user.update({
    where: { id: req.params.id },
    data: req.body,  // qualquer campo pode ser atualizado
  })
  return res.json(user)
})

// CORRETO:
app.put('/api/users/:id', authenticate, async (req, res) => {
  const { name, email, bio } = req.body  // apenas campos permitidos
  const user = await prisma.user.update({
    where: { id: req.params.id, userId: req.user.id },
    data: { name, email, bio },
  })
  return res.json(user)
})
```

### 4. SQL Injection via Template Literal

```typescript
// AI GERA (INSEGURO, especialmente para ORDER BY ou filtros dinâmicos):
const results = await db.query(`SELECT * FROM users WHERE role = '${role}' ORDER BY ${sortBy}`)

// CORRETO:
const ALLOWED_SORT = ['name', 'createdAt', 'email']
const safeSort = ALLOWED_SORT.includes(sortBy) ? sortBy : 'createdAt'
const results = await db.query('SELECT * FROM users WHERE role = $1 ORDER BY ' + safeSort, [role])
```

### 5. Sem Rate Limiting

AI raramente adiciona rate limiting por padrão — foca no happy path.

```typescript
// AI GERA sem rate limit:
app.post('/api/auth/login', loginHandler)

// CORRETO:
app.post('/api/auth/login', loginRateLimiter, loginHandler)
```

### 6. Token em localStorage

AI frequentemente segue tutoriais que usam localStorage:

```javascript
// AI GERA (INSEGURO):
localStorage.setItem('authToken', token)

// MELHOR:
// Usar HttpOnly cookie no servidor
```

### 7. Resposta Diferenciada em Auth (Enumeração)

```javascript
// AI GERA (INSEGURO):
if (!user) return res.status(401).json({ error: 'User not found' })
if (!validPassword) return res.status(401).json({ error: 'Wrong password' })

// CORRETO:
if (!user || !validPassword) return res.status(401).json({ error: 'Invalid credentials' })
```

### 8. Error Handler Sem Tratamento de Produção

```javascript
// AI GERA (INSEGURO em produção):
app.use((err, req, res, next) => {
  res.status(500).json({ error: err.message, stack: err.stack })
})

// CORRETO:
app.use((err, req, res, next) => {
  logger.error(err)
  res.status(500).json({ error: 'Internal server error' })
})
```

### 9. Sem Validação de Input

```typescript
// AI GERA sem validação:
app.post('/api/users', async (req, res) => {
  const { email, name, age } = req.body
  const user = await prisma.user.create({ data: { email, name, age } })
  return res.json(user)
})

// CORRETO — com Zod:
const schema = z.object({
  email: z.string().email().max(255),
  name: z.string().min(2).max(100),
  age: z.number().int().min(0).max(150).optional(),
})
```

### 10. Exposição de Dados no Response

```typescript
// AI GERA (retorna tudo incluindo campos sensíveis):
const user = await prisma.user.findUnique({ where: { id } })
return res.json(user)  // inclui passwordHash, internalNotes, etc.

// CORRETO:
const user = await prisma.user.findUnique({
  where: { id },
  select: { id: true, name: true, email: true, createdAt: true }
})
return res.json(user)
```

---

## Como Auditar Código AI-Generated

1. **Assumir que ownership checks estão ausentes** — verificar todos os endpoints com parâmetros de ID
2. **Assumir que rate limiting está ausente** — verificar todos os endpoints de auth
3. **Verificar se há validação de schema** — AI raramente inclui
4. **Verificar select fields em queries** — AI geralmente retorna objeto completo
5. **Verificar error handlers** — AI coloca `err.message` e `err.stack` na resposta
6. **Verificar algoritmos de JWT** — AI raramente especifica
7. **Verificar se existe middleware de autenticação** — pode estar faltando em alguma rota

---

## Prompt Injection em Aplicações com IA

Para apps que usam LLMs internamente:

```bash
grep -rn "openai\|anthropic\|langchain\|llm\|gpt\|claude" --include="*.{js,ts,py}" -l
```

**Verificar:**
- Dados do usuário são sanitizados antes de entrar no prompt?
- Prompt injection pode vazar dados de outros usuários?
- Há system prompt com secrets que podem ser extraídos?
- Rate limiting e cost limiting nas chamadas de LLM?
