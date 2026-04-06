# ABAC — Attribute-Based Access Control

Controle de acesso baseado em atributos — mais granular que RBAC.

---

## O que é ABAC

Em vez de apenas verificar o role, ABAC considera atributos do usuário, do recurso e do contexto:
- **Usuário:** role, departamento, localização, horário, device
- **Recurso:** classificação, dono, estado, sensibilidade
- **Contexto:** horário, IP, dispositivo, ambiente

---

## Verificações Principais

```bash
# Verificar se há lógica de autorização granular
grep -rn "canEdit\|canDelete\|canView\|canAccess\|isOwner\|belongsTo" --include="*.{js,ts,py}" -n

# Verificar policies/abilities
grep -rn "ability\|policy\|can\(\|cannot\(\|defineRules\|defineAbilities" --include="*.{js,ts,py}" -l
```

---

## Padrões Comuns

### 1. Verificação de Ownership
```typescript
// Verificar se recurso pertence ao usuário
async function canEditPost(userId: string, postId: string): Promise<boolean> {
  const post = await db.posts.findUnique({ where: { id: postId } })
  return post?.authorId === userId
}

// Uso em endpoint
app.put('/api/posts/:id', authenticate, async (req, res) => {
  if (!await canEditPost(req.user.id, req.params.id)) {
    return res.status(403).json({ error: 'Forbidden' })
  }
  // ...
})
```

### 2. Verificação de Estado do Recurso
```typescript
// Recurso em determinado estado não pode ser editado por user comum
if (order.status === 'shipped' && req.user.role !== 'admin') {
  return res.status(403).json({ error: 'Cannot edit shipped order' })
}
```

---

## Checklist

- [ ] Lógica de autorização centralizada (não espalhada no código)
- [ ] Ownership verificado antes de qualquer modificação
- [ ] Estado do recurso considerado nas permissões
- [ ] Regras de negócio de autorização documentadas e testadas

---

## Red Flags

1. Lógica de autorização duplicada em múltiplos endpoints (inconsistência)
2. Autorização inline sem abstração (difícil de manter e auditar)
3. Sem testes para cenários de autorização negada
