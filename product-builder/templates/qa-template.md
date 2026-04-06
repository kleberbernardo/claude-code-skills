---
feature_id: FEAT-001
date: [DATA]
status: passed | failed | partial
tasks_validated: [BE-TASK-001, FE-TASK-001]
---

# QA Report — FEAT-001: [Nome da Feature]

## Resultado Geral

**Status: [PASSOU ✅ / FALHOU ❌ / PARCIAL ⚠️]**

| AC ID | Descrição | Status |
|-------|-----------|--------|
| AC-001 | [Descrição] | ✅ / ❌ / ⚠️ / 🔲 |

## Gaps Encontrados

### 🔴 Bloqueantes (P0) — [N gaps]

#### GAP-001: [Título]
**AC:** AC-00X  
**Problema:** [Descrição clara do que está errado]  
**Evidência:** `[arquivo:linha]` ou [descrição do comportamento]  
**Correção sugerida:** [Como corrigir]

### 🟡 Importantes (P1) — [N gaps]

#### GAP-002: [Título]
[Mesmo formato]

### 🔵 Melhorias (P2) — [N gaps]

#### GAP-003: [Título]
[Mesmo formato]

## Resumo

| Métrica | Valor |
|---------|-------|
| ACs verificados | X |
| Passaram | X |
| Falharam | X |
| Parciais | X |
| Não verificáveis | X |

## Próximos Passos

**[Se FALHOU]:** Corrigir GAP-001 (bloqueante) e re-executar: `/product-builder qa FEAT-001`  
**[Se PASSOU]:** Feature concluída. Próxima: `/product-builder plan FEAT-002/BE-TASK-001`
