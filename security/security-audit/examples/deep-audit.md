# Exemplo: Deep Audit

Demonstração de formato de relatório completo para auditoria deep.

---

## Invocação

```
/security-audit deep
```

---

## Exemplo de Executive Summary

```
Este sistema Next.js 14 + Prisma + PostgreSQL apresenta postura de segurança PRECÁRIA.
Foram identificados 2 issues críticos, 4 altos e 6 médios.

Os riscos mais severos envolvem:
1. Ausência total de verificação de ownership em endpoints de recursos (IDOR)
   — qualquer usuário pode acessar dados de qualquer outro usuário
2. JWT sem algoritmo especificado — vulnerável a algorithm confusion attack
3. Falta de rate limiting nos endpoints de autenticação

O sistema não está pronto para produção com dados reais de usuários.
Recomendação: resolver Critical e High antes do lançamento.
```

---

## Exemplo de Finding Completo

```markdown
### [CRIT-001] IDOR — Ausência de Verificação de Ownership em Recursos

| Campo | Valor |
|-------|-------|
| Severidade | Critical |
| Confiança | High |
| CVSS | 8.6 |
| CWE | CWE-862 |
| OWASP | A01:2021 |
| Arquivos Afetados | `src/app/api/documents/[id]/route.ts:15`, `src/app/api/orders/[id]/route.ts:22` |

**Descrição**
Os endpoints de acesso a documentos e pedidos aceitam um ID como parâmetro de rota 
mas não verificam se o recurso solicitado pertence ao usuário autenticado. 
Qualquer usuário autenticado pode acessar recursos de qualquer outro usuário 
simplesmente alterando o ID na URL.

**Evidência**
```
Arquivo: src/app/api/documents/[id]/route.ts:15
Código: const doc = await prisma.document.findUnique({ where: { id: params.id } })
// Sem verificação de userId
```

**Cenário de Exploração**
1. Usuário A faz login e obtém token de autenticação
2. Usuário A navega para /documents/1234 (seu documento)
3. Usuário A troca o ID para /documents/1235 (documento do Usuário B)
4. Servidor retorna o documento do Usuário B sem verificação
5. Em IDs sequenciais, é possível enumerar todos os documentos do sistema

**Impacto de Negócio**
Vazamento de todos os dados de todos os usuários para qualquer usuário autenticado.
Em sistema com dados empresariais/financeiros: violação de confidencialidade, 
possível multa LGPD, perda de clientes.

**Correção**
```typescript
// Antes (vulnerável)
const doc = await prisma.document.findUnique({ where: { id: params.id } })

// Depois (seguro)
const session = await getServerSession()
const doc = await prisma.document.findFirst({
  where: { id: params.id, userId: session.user.id }
})
if (!doc) return Response.json({ error: 'Not found' }, { status: 404 })
```

**Prioridade:** CRÍTICA — corrigir antes de qualquer lançamento público
```
