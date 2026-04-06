# File Conventions — Product Builder

Convenções de nomes, paths e estrutura de todos os artefatos gerados.

---

## Diretório Raiz

Todos os artefatos vivem em `.ai/product/` na raiz do projeto.

```
.ai/
  product/
    01-discovery/
    02-prd/
    03-tasks/
    04-plans/
    05-execution/
    06-qa/
```

> **Por que `.ai/`?** Separa claramente os artefatos de produto do código. Fácil de ignorar no .gitignore se necessário, mas recomendado versioná-los.

---

## Nomenclatura de Features

Formato: `FEAT-NNN-nome-kebab-case`

Exemplos:
- `FEAT-001-autenticacao`
- `FEAT-002-dashboard-tarefas`
- `FEAT-003-notificacoes-email`

Regras:
- Numeração sequencial, 3 dígitos com zero à esquerda
- Nome em kebab-case, sem acentos
- Máximo 30 caracteres no nome
- Derivado do título no PRD

---

## Nomenclatura de Tasks

| Tipo | Formato | Exemplo |
|------|---------|---------|
| Backend | `BE-TASK-NNN` | `BE-TASK-001` |
| Frontend | `FE-TASK-NNN` | `FE-TASK-001` |
| QA | `QA-TASK-NNN` | `QA-TASK-001` |

Numeração reinicia por feature:
```
FEAT-001/
  BE-TASK-001  ← primeiro backend da FEAT-001
  BE-TASK-002
  FE-TASK-001  ← primeiro frontend da FEAT-001
  QA-TASK-001
FEAT-002/
  BE-TASK-001  ← reinicia para FEAT-002
  FE-TASK-001
```

---

## Estrutura de Pastas Completa

```
.ai/product/

01-discovery/
  mvp.md                    ← documento principal do MVP
  user-journeys.md          ← jornadas mapeadas
  screen-map.md             ← mapa de telas em texto

02-prd/
  prd.md                    ← PRD consolidado
  features/
    FEAT-001-autenticacao/
      feature.md            ← PRD da feature

03-tasks/
  tasks-overview.md         ← lista completa, status, dependências
  features/
    FEAT-001/
      backend/
        BE-TASK-001.md
        BE-TASK-002.md
      frontend/
        FE-TASK-001.md
      qa/
        QA-TASK-001.md

04-plans/
  features/
    FEAT-001/
      BE-TASK-001-plan.md
      FE-TASK-001-plan.md

05-execution/
  logs/
    FEAT-001-BE-TASK-001.log.md
    FEAT-001-FE-TASK-001.log.md

06-qa/
  reports/
    FEAT-001-qa-report.md
```

---

## Formato de Nomes de Arquivo

| Artefato | Nome do arquivo |
|----------|----------------|
| MVP | `mvp.md` |
| User Journeys | `user-journeys.md` |
| Screen Map | `screen-map.md` |
| PRD consolidado | `prd.md` |
| PRD de feature | `feature.md` (dentro da pasta da feature) |
| Tasks overview | `tasks-overview.md` |
| Task individual | `[TASK-ID].md` (ex: `BE-TASK-001.md`) |
| Plano de task | `[TASK-ID]-plan.md` (ex: `BE-TASK-001-plan.md`) |
| Log de execução | `[FEAT-ID]-[TASK-ID].log.md` |
| Relatório QA | `[FEAT-ID]-qa-report.md` |

---

## Frontmatter Obrigatório

Todo arquivo `.md` de artefato deve ter frontmatter YAML:

```yaml
---
id: FEAT-001
title: Autenticação de Usuários
status: draft | in-review | approved | in-progress | done
created: 2026-04-06
updated: 2026-04-06
depends_on: []
---
```

Status válidos:
- `draft` — gerado, não revisado
- `in-review` — aguardando aprovação do usuário
- `approved` — aprovado, pronto para próxima etapa
- `in-progress` — em execução
- `done` — concluído e verificado

---

## .gitignore Recomendado

Se o usuário quiser ignorar os artefatos de produto:
```
# Ignorar apenas outputs de execução
.ai/product/05-execution/
```

Recomendado versionar o restante (discovery, prd, tasks, plans, qa).

---

## Atualização de Status

Ao iniciar uma nova etapa, atualizar o frontmatter dos artefatos relacionados:
- Iniciar plan → task: `in-progress`
- Concluir execução → task: `done`, plan: `done`
- QA aprovado → feature: `done`
- QA falhou → task relevante volta para `in-progress`
