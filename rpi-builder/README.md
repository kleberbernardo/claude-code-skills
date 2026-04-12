# RPI Builder

Skill de execução disciplinada de tarefas via Research → Plan → Implement para o Claude Code.

Opera sobre artefatos gerados pelo `sdd-builder` ou qualquer task bem definida.

---

## Uso

O argumento é sempre o **ID numérico da task** (ex: `001`). Cada modo sabe qual pasta usar internamente.

```bash
# Descobrir próximo passo (começo recomendado)
/rpi-builder next

# Pesquisar o codebase antes de planejar
/rpi-builder research 001

# Gerar plano técnico executável
/rpi-builder plan 001

# Executar a implementação
/rpi-builder implement 001

# Ver estado atual de todas as tasks
/rpi-builder status
```

> **`research 001` e `plan 001`** resolvem em `tasks/001-*.md`  
> **`implement 001`** resolve em `plans/001-*.md` — é o plano que guia a implementação, não a task diretamente

---

## Fluxo obrigatório

```
Task definida
    ↓
/rpi-builder research 001   →  .ai/product/research/001-task.md
    ↓
/rpi-builder plan 001       →  .ai/product/plans/001-task.md
    ↓
/rpi-builder implement 001  →  código + task atualizada
```

Nenhuma etapa pode ser pulada.

---

## Resolução de ID

Se o ID passado não existir:

```
Task 005 não encontrada.

Tasks disponíveis:
- 001 — setup-projeto
- 002 — schema-usuario
- 003 — auth-login

Use /rpi-builder status para ver o estado completo.
```

Se a task já estiver concluída (`Status: done`):

```
⚠️ Task 001 já está concluída (Status: done).
Se quiser reexecutar esta fase, confirme explicitamente.
Para ver o próximo passo: /rpi-builder next
```

---

## Estrutura de artefatos

```
.ai/product/
  prd.md              ← contexto de negócio (gerado pelo sdd-builder)
  design.md           ← arquitetura técnica (gerado pelo sdd-builder)
  tasks/
    001-task-name.md  ← task com campos de status no topo
    002-task-name.md
  research/           ← criado automaticamente pelo rpi-builder
    001-task-name.md
    002-task-name.md
  plans/              ← criado automaticamente pelo rpi-builder
    001-task-name.md
    002-task-name.md
```

---

## Status por task

Cada task tem campos de status atualizados automaticamente:

```
Status: pending | in-progress | done
Research: not-started | done
Plan: not-started | done
Implementation: not-started | done
```

---

## Integração com SDD Builder

```
/sdd-builder spec    →  prd.md
/sdd-builder design  →  design.md
/sdd-builder tasks   →  tasks/*.md
         ↓
/rpi-builder next    → (começa o ciclo de execução)
```

---

## Estrutura da skill

```
rpi-builder/
  SKILL.md                        ← ponto de entrada
  README.md                       ← este arquivo
  core/
    rpi-flow.md                   ← fluxo completo e lógica de decisão
    status-management.md          ← como ler e atualizar status
    rastreability-rules.md        ← convenções de IDs e nomes
  modes/
    research.md                   ← fluxo do modo RESEARCH
    plan.md                       ← fluxo do modo PLAN
    implement.md                  ← fluxo do modo IMPLEMENT
    status.md                     ← fluxo do modo STATUS
    next.md                       ← fluxo do modo NEXT
  templates/
    research-template.md          ← estrutura do research.md (14 seções)
    plan-template.md              ← estrutura do plan.md (16 seções)
  prompts/
    research-rules.md             ← como fazer pesquisa eficiente
    plan-rules.md                 ← como escrever planos executáveis
    implement-rules.md            ← como implementar com disciplina
  examples/
    research-example.md           ← research completo de referência
    plan-example.md               ← plan completo de referência
  knowledge/
    rpi-principles.md             ← fundamentos do método RPI
    disciplined-execution.md      ← execução previsível em projetos reais
```

---

## Princípios

- Nunca implementar sem plan — sem plano, sem código
- Nunca planejar sem research — sem pesquisa, sem plano
- Nunca expandir escopo — apenas o que a task define
- Mudanças mínimas necessárias — não refatorar o que não foi pedido
- Rastreabilidade total — task → research → plan → código
- Funcionar bem em sessões interrompidas — estado sempre persistido
