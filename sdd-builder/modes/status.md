# Modo STATUS — SDD Builder

Exibir o estado atual de todos os artefatos do projeto.

---

## Objetivo

Dar ao usuário uma visão rápida e completa de:
- Quais artefatos já foram gerados
- Quais features existem e em que estágio estão
- Qual é o próximo passo recomendado

---

## Fluxo de Execução

```
1. Glob todos os artefatos em .ai/product/
2. Para cada feature: verificar se design e tasks foram gerados
3. Calcular o próximo passo recomendado
4. Exibir o relatório de status
```

Não perguntar nada. Não modificar nada. Apenas ler e exibir.

---

## Passo 1: Descoberta de Artefatos

Executar os seguintes Globs:

```
Glob .ai/product/prd.md
Glob .ai/product/ux.md
Glob .ai/product/design.md
Glob .ai/product/features/*.md
Glob .ai/product/tasks/*.md
```

---

## Passo 2: Rastreamento de Features

Para cada arquivo em `features/*.md`:

**Verificar se o design foi gerado para a feature:**
- Grep em `design.md` por `[NOVO - feature NNN]` onde NNN é o número da feature
- Se encontrar: design gerado ✅
- Se não encontrar (ou design.md não existir): design pendente ⏳

**Verificar se as tasks foram geradas para a feature:**
- Grep em `tasks/*.md` por `<!-- Feature: NNN` onde NNN é o número da feature
- Se encontrar ao menos 1 arquivo: tasks geradas ✅ (contar quantas)
- Se não encontrar: tasks pendentes ⏳

---

## Passo 3: Cálculo do Próximo Passo

Lógica de prioridade (primeira condição verdadeira vence):

```
prd.md não existe?
  → "Execute /sdd-builder spec para começar"

prd.md existe, design.md não existe?
  → "Execute /sdd-builder design"

design.md existe, tasks/ vazia ou não existe?
  → "Execute /sdd-builder tasks"

Existe alguma feature sem design?
  → "Execute /sdd-builder design [NNN] para [nome da feature]"

Existe alguma feature com design mas sem tasks?
  → "Execute /sdd-builder tasks [NNN] para [nome da feature]"

Tudo completo?
  → "Tudo pronto. Execute /rpi-builder para iniciar a implementação."
```

---

## Passo 4: Exibir Relatório

Formato do relatório:

```
─────────────────────────────────────────────
  SDD Builder — Status do Projeto
─────────────────────────────────────────────

Artefatos principais:
  [ícone] PRD          prd.md              [data ou "não gerado"]
  [ícone] UX Visual    ux.md               [data ou "não gerado (opcional)"]
  [ícone] Design       design.md           [data ou "não gerado"]
  [ícone] Tasks        tasks/ — N tasks    [ou "nenhuma task gerada"]

Features:
  [ícone] NNN — [nome]   design [ícone]   tasks [ícone] (N tasks)
  [ícone] NNN — [nome]   design [ícone]   tasks [ícone]

─────────────────────────────────────────────
Próximo passo:
  /sdd-builder [comando]
─────────────────────────────────────────────
```

**Ícones:**
- ✅ existe / gerado
- ⏳ pendente / não gerado ainda
- ➖ não se aplica (ex: ux.md quando não foi respondida Fase 5)

---

## Exemplos de Output

### Projeto do zero, nada gerado:
```
─────────────────────────────────────────────
  SDD Builder — Status do Projeto
─────────────────────────────────────────────

Artefatos principais:
  ⏳ PRD          prd.md              não gerado
  ➖ UX Visual    ux.md               não gerado (opcional)
  ⏳ Design       design.md           não gerado
  ⏳ Tasks        tasks/              nenhuma task

Features:
  nenhuma feature especificada

─────────────────────────────────────────────
Próximo passo:
  /sdd-builder spec
─────────────────────────────────────────────
```

### PRD gerado, falta design:
```
─────────────────────────────────────────────
  SDD Builder — Status do Projeto
─────────────────────────────────────────────

Artefatos principais:
  ✅ PRD          prd.md              2026-04-12
  ✅ UX Visual    ux.md               2026-04-12
  ⏳ Design       design.md           não gerado
  ⏳ Tasks        tasks/              nenhuma task

Features:
  nenhuma feature especificada

─────────────────────────────────────────────
Próximo passo:
  /sdd-builder design
─────────────────────────────────────────────
```

### Produto completo + feature pendente:
```
─────────────────────────────────────────────
  SDD Builder — Status do Projeto
─────────────────────────────────────────────

Artefatos principais:
  ✅ PRD          prd.md              2026-04-10
  ✅ UX Visual    ux.md               2026-04-10
  ✅ Design       design.md           2026-04-11
  ✅ Tasks        tasks/              8 tasks (001–008)

Features:
  ✅ 001 — exportacao-relatorios    design ✅   tasks ✅ (3 tasks: 006–008)
  ⏳ 002 — notificacoes-email       design ⏳   tasks ⏳

─────────────────────────────────────────────
Próximo passo:
  /sdd-builder design 002
─────────────────────────────────────────────
```

### Tudo completo:
```
─────────────────────────────────────────────
  SDD Builder — Status do Projeto
─────────────────────────────────────────────

Artefatos principais:
  ✅ PRD          prd.md              2026-04-10
  ✅ UX Visual    ux.md               2026-04-10
  ✅ Design       design.md           2026-04-12
  ✅ Tasks        tasks/              11 tasks (001–011)

Features:
  ✅ 001 — exportacao-relatorios    design ✅   tasks ✅ (3 tasks: 006–008)
  ✅ 002 — notificacoes-email       design ✅   tasks ✅ (3 tasks: 009–011)

─────────────────────────────────────────────
Próximo passo:
  Tudo pronto. Execute /rpi-builder para iniciar a implementação.
─────────────────────────────────────────────
```
