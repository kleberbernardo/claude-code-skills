# RPI Builder

Skill de execução disciplinada de tarefas via Research → Plan → Implement para o Claude Code.

Opera sobre artefatos gerados pelo `sdd-builder` ou qualquer task bem definida.

---

## Uso

```bash
# Pesquisar o codebase antes de planejar
/rpi-builder research @.ai/product/tasks/001-task-name.md

# Gerar plano técnico executável
/rpi-builder plan @.ai/product/tasks/001-task-name.md

# Executar a implementação
/rpi-builder implement @.ai/product/plans/001-task-name.md

# Ver estado atual de todas as tasks
/rpi-builder status

# Descobrir próximo passo
/rpi-builder next
```

---

## Fluxo obrigatório

```
Task definida
    ↓
/rpi-builder research @task   →  .ai/product/research/NNN.md
    ↓
/rpi-builder plan @task       →  .ai/product/plans/NNN.md
    ↓
/rpi-builder implement @plan  →  código + task atualizada
```

Nenhuma etapa pode ser pulada.

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
