# RBAC — Role-Based Access Control

Auditoria de controle de acesso baseado em roles.

---

## Verificações Principais

### Definição de Roles
```bash
# Localizar definição de roles/permissões
grep -rn "role\|permission\|privilege\|capability" --include="*.{js,ts,py,go}" -l
grep -rn "enum.*Role\|ROLES\s*=\|permissions\s*=" --include="*.{js,ts,py}" -n
```

### Middleware de Autorização
```bash
# Verificar se há middleware de autorização
grep -rn "requireRole\|checkRole\|hasRole\|canAccess\|authorize\|isAdmin\|isOwner" --include="*.{js,ts,py}" -l

# Verificar se middleware está aplicado em todos os endpoints sensíveis
grep -rn "router\.\(get\|post\|put\|patch\|delete\)" --include="*.{js,ts}" -n | grep -v "requireAuth\|authenticate\|protect\|guard"
```

---

## Vulnerabilidades Comuns

### 1. Verificação de Role no Frontend Apenas
```javascript
// VULNERÁVEL — verificação apenas no UI
{user.isAdmin && <AdminButton onClick={deleteUser} />}

// A rota /api/admin/users/delete não verifica role no backend
// Atacante chama a rota diretamente

// SEGURO — verificação no backend
app.delete('/api/admin/users/:id', authenticate, requireRole('admin'), handler)
```

### 2. Role Escalation via Mass Assignment
```javascript
// VULNERÁVEL — aceita role do body
const user = await User.create(req.body)  // { email, password, role: 'admin' }

// SEGURO — role definido pelo sistema
const user = await User.create({
  email: req.body.email,
  password: req.body.password,
  role: 'user',  // sempre default
})
```

```bash
grep -rn "User\.create\|user\.save\|createUser" --include="*.{js,ts,py}" -A 3 | grep -v "role.*=.*['\"]user\|role.*default\|role.*member"
```

### 3. Verificação de Role Condicional Fraca
```javascript
// VULNERÁVEL — verificação invertida ou comparação fraca
if (user.role !== 'user') { ... }  // qualquer role que não seja 'user' tem acesso

// SEGURO — lista de permissões explícita
if (!['admin', 'superadmin'].includes(user.role)) {
  return res.status(403).json({ error: 'Forbidden' })
}
```

---

## Checklist

- [ ] Roles definidos explicitamente e documentados
- [ ] Verificação de role no backend em TODOS os endpoints sensíveis
- [ ] Role não pode ser modificado pelo próprio usuário
- [ ] Middleware de autenticação + autorização em endpoints protegidos
- [ ] Verificação de role usa lista de permissões explícita
- [ ] Usuário padrão criado com role mínimo

---

## Red Flags

1. Endpoints `/admin/*` sem verificação de role no backend
2. Role definido por `req.body.role` em criação de usuário
3. Verificação de autorização apenas no frontend
4. `if (role !== 'user')` em vez de `if (role === 'admin')`
