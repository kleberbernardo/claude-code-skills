# RPI Flow — rpi-builder

Fluxo completo de execução e decisões por modo.

---

## Visão do Fluxo

```
[Task SDD]
    │
    ├─ research ──→ [research.md] ──→ atualiza task: Research: done
    │
    ├─ plan ────→ [plan.md] ──────→ atualiza task: Plan: done
    │
    └─ implement ──→ [código] ────→ atualiza task: Implementation: done, Status: done
```

**Invariante:** A ordem nunca muda. Nenhuma etapa pode ser pulada.

---

## Pré-requisitos por Modo

### `research`
- Requer: task file em `.ai/product/tasks/`
- Requer: `.ai/product/prd.md` (aviso se ausente, não bloqueia)
- Requer: `.ai/product/design.md` (aviso se ausente, não bloqueia)
- Produz: `.ai/product/research/NNN-task-name.md`

### `plan`
- Requer: task file em `.ai/product/tasks/`
- Requer: research correspondente em `.ai/product/research/`
- Requer: `.ai/product/prd.md`
- Requer: `.ai/product/design.md`
- Produz: `.ai/product/plans/NNN-task-name.md`

### `implement`
- Requer: plan file em `.ai/product/plans/`
- Requer: task referenciada no plano
- Requer: research referenciado no plano
- Produz: código implementado + task atualizada

### `status`
- Requer: `.ai/product/tasks/` (pode estar vazia)
- Produz: tabela de progresso no chat

### `next`
- Requer: `.ai/product/tasks/`
- Lê: `.ai/product/research/` e `.ai/product/plans/`
- Produz: sugestão de próximo comando

---

## Lógica de Decisão do modo `next`

```
Para cada task (em ordem numérica):
    Se status == done → pular

    Se research não existe:
        → sugerir: rpi-builder research @.ai/product/tasks/NNN.md
        → parar

    Se plan não existe:
        → sugerir: rpi-builder plan @.ai/product/tasks/NNN.md
        → parar

    Se implementation != done:
        → sugerir: rpi-builder implement @.ai/product/plans/NNN.md
        → parar

Se todas as tasks estão done:
    → "All tasks are completed."
```

---

## Tratamento de Estados Inconsistentes

| Estado detectado | Ação |
|-----------------|------|
| Task sem campos de status | Assumir: pending / not-started em tudo |
| Research existe mas task diz not-started | Confiar no arquivo research; atualizar task |
| Plan existe mas task diz not-started | Confiar no arquivo plan; atualizar task |
| Plan referencia research inexistente | Avisar inconsistência; não implementar |
| Plan contradiz design.md | Parar; apontar a contradição específica |
| Sessão interrompida (task in-progress) | Ler o que foi feito; continuar da etapa correta |

---

## Sessões Interrompidas

Quando o modo `implement` é iniciado e encontra uma task `in-progress`:

1. Ler o plan completo
2. Inspecionar o que já foi implementado no código
3. Identificar o que falta
4. Continuar a partir do ponto correto
5. Nunca reimplementar o que já existe e funciona

Quando o modo `research` ou `plan` encontra arquivo parcial:
1. Ler o que existe
2. Identificar seções ausentes ou incompletas
3. Completar sem reescrever o que já está correto

---

## Rastreabilidade de IDs

O identificador base da task (ex: `001-setup-auth`) deve ser usado identicamente em:
- `tasks/001-setup-auth.md`
- `research/001-setup-auth.md`
- `plans/001-setup-auth.md`

A skill deve derivar o nome de research/plan a partir do nome do arquivo da task.

Exemplo:
- Task recebida: `@.ai/product/tasks/001-setup-auth.md`
- Research output: `.ai/product/research/001-setup-auth.md`
- Plan output: `.ai/product/plans/001-setup-auth.md`
