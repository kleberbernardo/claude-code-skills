# Multi-Tenant Security

Isolamento entre tenants em aplicações SaaS. Falha aqui expõe dados de todos os clientes.

---

## Modelos de Multi-Tenancy e Riscos

### 1. Banco de dados compartilhado, schema compartilhado
**Maior risco** — uma query sem filtro expõe dados de todos os tenants.

```sql
-- VULNERÁVEL — sem filtro de tenant
SELECT * FROM orders WHERE status = 'pending'

-- SEGURO — sempre filtrar por tenant_id
SELECT * FROM orders WHERE tenant_id = $1 AND status = 'pending'
```

### 2. Banco compartilhado, schemas separados
**Risco médio** — erro de conexão pode cruzar schemas.

### 3. Bancos separados por tenant
**Menor risco** — isolamento real, mas complexidade operacional maior.

---

## Padrões de Busca

```bash
# Queries sem tenant_id
grep -rn "SELECT\|findMany\|findAll\|\.find(" --include="*.{js,ts,py,go}" | grep -v "tenant_id\|tenantId\|organization_id\|orgId\|workspace_id"

# Prisma sem tenant filter
grep -rn "prisma\.\w\+\.findMany\|prisma\.\w\+\.findFirst" --include="*.{js,ts}" -A 5 | grep -v "tenantId\|organizationId"

# Middleware de tenant
grep -rn "tenantId\|tenant_id\|x-tenant-id" --include="*.{js,ts,py}" | grep "middleware\|inject\|set\|extract"
```

---

## RLS — Row Level Security (PostgreSQL)

A implementação mais robusta de multi-tenancy: o próprio banco garante isolamento.

```sql
-- Verificar se RLS está habilitado
SELECT tablename, rowsecurity FROM pg_tables WHERE schemaname = 'public';

-- Verificar policies
SELECT * FROM pg_policies WHERE tablename = 'orders';
```

**Buscar no código:**
```bash
grep -rn "ROW LEVEL SECURITY\|CREATE POLICY\|ENABLE ROW LEVEL\|set_config.*tenant\|app\.tenant" --include="*.{sql,prisma,ts,js}" -i
```

Se o projeto usa Supabase/Postgres e NÃO tem RLS em tabelas com dados de tenant → **High/Critical**.

---

## Vulnerabilidades Comuns

### 1. Tenant ID via Header sem Validação
```javascript
// VULNERÁVEL — trust header sem verificar
const tenantId = req.headers['x-tenant-id']
const data = await db.findMany({ where: { tenantId } })
// Atacante muda o header para acessar outro tenant

// SEGURO — tenant_id vem do token autenticado
const tenantId = req.user.tenantId  // do JWT ou session
```

### 2. Admin Cross-Tenant
```javascript
// VULNERÁVEL — admin de tenant A pode acessar tenant B
if (user.role === 'admin') {
  return await db.findById(resourceId)  // sem verificar tenant
}

// SEGURO — admin de tenant A só acessa tenant A
if (user.role === 'admin' && resource.tenantId === user.tenantId) { ... }
```

### 3. Compartilhamento de Cache
```javascript
// VULNERÁVEL — cache key sem tenant
cache.set(`user:${userId}`, userData)

// SEGURO — cache key inclui tenant
cache.set(`tenant:${tenantId}:user:${userId}`, userData)
```

---

## Checklist

- [ ] Toda query filtra por tenant_id (ou equivalente)
- [ ] Tenant ID vem do token autenticado, nunca do request
- [ ] RLS habilitado no banco (se PostgreSQL)
- [ ] Admin de um tenant não acessa dados de outro tenant
- [ ] Cache keys incluem tenant_id
- [ ] Uploads/arquivos isolados por tenant (path ou bucket separado)
- [ ] Logs e audit trail filtráveis por tenant
- [ ] Endpoints de export não expõem dados cross-tenant

---

## Red Flags

1. Queries sem `tenant_id` em tabelas de dados de clientes
2. `x-tenant-id` header confiado sem validação pelo token
3. PostgreSQL sem RLS em tabelas multi-tenant
4. Supabase sem Row Level Security habilitado
