---
id: PRD-001
title: [Nome do Produto] — PRD
status: draft
created: [DATA]
---

# PRD — [Nome do Produto]

## Executive Summary

[3-4 frases: o que é o produto, para quem é, qual problema resolve, quais são as principais features do MVP]

## Features do MVP

| ID | Feature | Status | Prioridade | Deps |
|----|---------|--------|-----------|------|
| FEAT-001 | [Nome] | draft | P0 | - |
| FEAT-002 | [Nome] | draft | P0 | FEAT-001 |
| FEAT-003 | [Nome] | draft | P1 | - |

## Mapa de Dependências

```
FEAT-001 ──→ FEAT-002
FEAT-001 ──→ FEAT-003
FEAT-002 ──→ FEAT-004
```

## Ordem Recomendada de Implementação

1. [FEAT-001] — [motivo: base para outras]
2. [FEAT-002] — [motivo: depende de FEAT-001]
3. [FEAT-003] — [motivo: independente, alta prioridade]

## Glossário

| Termo | Definição |
|-------|-----------|
| [Termo] | [Definição no contexto deste produto] |

## Links para Features

- [FEAT-001 — Nome](features/FEAT-001-nome/feature.md)
- [FEAT-002 — Nome](features/FEAT-002-nome/feature.md)
