# IDOR — Insecure Direct Object Reference

Uma das vulnerabilidades mais prevalentes e mais encontradas em vibe coding / AI-generated code.

---

## O que é IDOR

IDOR ocorre quando um endpoint usa um identificador fornecido pelo usuário (ID, UUID, email) para acessar um recurso sem verificar se o usuário tem permissão para acessar ESSE recurso específico.

**Exemplo clássico:**
```
GET /api/orders/1234
```
Usuário A consegue acessar pedido do Usuário B apenas mudando o número.

---

## Por que é comum em vibe coding

AI geralmente gera o código mais simples possível:
```typescript
// AI gera isso — VULNERÁVEL
app.get('/api/orders/:id', async (req, res) => {
  const order = await db.orders.findById(req.params.id)
  return res.json(order)
})

// Correto seria:
app.get('/api/orders/:id', authenticate, async (req, res) => {
  const order = await db.orders.findOne({
    where: { id: req.params.id, userId: req.user.id }  // verifica ownership
  })
  if (!order) return res.status(404).json({ error: 'Not found' })
  return res.json(order)
})
```

---

## Padrões de Busca

```bash
# Endpoints que usam parâmetros de ID sem verificação óbvia
grep -rn "findById\|findOne.*params\|findOne.*req\.params" --include="*.{js,ts}" -n
grep -rn "\.find_by_id\|\.get(.*params\[" --include="*.py" -n
grep -rn "WHERE id = \$1\|WHERE id = ?" --include="*.{js,ts,go,py}" -n

# Prisma/TypeORM sem userId
grep -rn "\.findUnique\|\.findFirst\|\.findOne" --include="*.{js,ts}" -A 3 | grep -v "userId\|user_id\|ownerId\|createdBy"

# Sequelize sem userId
grep -rn "where.*id.*req\.params\|where.*id.*req\.body" --include="*.{js,ts}" | grep -v "userId\|user_id\|createdBy"
```

---

## Tipos de IDOR

### IDOR Horizontal
Usuário A acessa dados do Usuário B (mesmo nível de permissão).

```typescript
// VULNERÁVEL
const profile = await User.findById(req.params.userId)
// Qualquer usuário autenticado pode ver qualquer perfil

// SEGURO
const profile = await User.findById(req.user.id)  // sempre usa o usuário autenticado
```

### IDOR Vertical  
Usuário comum acessa recurso de admin.

```typescript
// VULNERÁVEL
const settings = await SystemSettings.findById(req.params.id)
// Sem verificação de role

// SEGURO
if (req.user.role !== 'admin') return res.status(403).json({ error: 'Forbidden' })
const settings = await SystemSettings.findById(req.params.id)
```

### IDOR via Referência Indireta
ID exposto em um endpoint permite acessar outro.

```typescript
// Documento expõe userId
GET /api/documents/123 → { id: 123, userId: 456, content: "..." }
// Atacante usa userId para acessar:
GET /api/users/456 → dados do usuário
```

---

## Checklist

Para CADA endpoint que aceita um ID de recurso:

- [ ] Verifica se o recurso pertence ao usuário autenticado
- [ ] OU verifica se o usuário tem permissão explícita para acessar o recurso
- [ ] OU o recurso é genuinamente público
- [ ] Retorna 404 (não 403) quando recurso não pertence ao usuário (anti-enumeração)
- [ ] IDs não são sequenciais (use UUIDs) para dificultar enumeração

---

## IDs Sequenciais vs UUIDs

```bash
# Verificar se IDs são sequenciais ou UUID
grep -rn "autoIncrement\|SERIAL\|AUTO_INCREMENT\|INTEGER.*PRIMARY\|Int.*@id" --include="*.{js,ts,prisma,sql}" -n
```

IDs sequenciais + IDOR = enumeração trivial de todos os recursos.
UUIDs tornam a enumeração impraticável mas não substituem verificação de autorização.

---

## Casos Reais

**Instagram 2019:** IDOR em endpoint de stories permitia ver stories privados de qualquer usuário.

**Padrão comum em SaaS brasileiro:** GET `/api/notas-fiscais/{id}` sem verificar se a nota fiscal pertence ao CNPJ do usuário autenticado.
