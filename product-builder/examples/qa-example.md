# QA Example — FEAT-001: Criar Tarefa

Exemplo de relatório de QA gerado.

---

```markdown
# QA Report — FEAT-001: Criar Tarefa

## Resultado Geral
**Status: ⚠️ PARCIAL**

| AC ID | Descrição | Status |
|-------|-----------|--------|
| AC-001 | Criar tarefa com dados válidos | ✅ Passou |
| AC-002 | Botão disabled com campos vazios | ✅ Passou |
| AC-003 | Mobile 375px | ⚠️ Parcial |

## Gaps Encontrados

### 🟡 Importantes (P1)

#### GAP-001: Modal extrapolando tela em mobile 375px

**AC:** AC-003  
**Problema:** O modal tem `max-w-md` mas sem padding lateral em telas < 400px, fazendo o botão "Adicionar Tarefa" ficar parcialmente fora da área de toque.

**Evidência:** `app/components/ModalNovaTarefa.tsx:26`
```tsx
<div className="fixed inset-0 z-50 flex items-center justify-center p-4">
  <div className="relative bg-white rounded-xl ... w-full max-w-md p-6">
  // p-4 no container + p-6 interno = 40px de padding lateral
  // em 375px: 375 - 40 = 335px disponíveis, max-w-md = 448px
  // resultado: overflow lateral
```

**Correção sugerida:** 
```tsx
// Trocar p-6 interno por p-4 em mobile
<div className="relative bg-white rounded-xl ... w-full max-w-md p-4 sm:p-6">
```

## Resumo
| Métrica | Valor |
|---------|-------|
| ACs verificados | 3 |
| Passaram | 2 |
| Parciais | 1 |
| Falharam | 0 |

## Próximos Passos
Corrigir GAP-001 e re-executar: `/product-builder qa FEAT-001`
```
