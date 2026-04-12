# SDD Builder

Skill de Spec-Driven Development para o Claude Code.

Transforma uma ideia em documentação estruturada, completa e executável via três modos sequenciais.

---

## Uso

```bash
# Modo 1: Entrevista interativa → PRD
/sdd-builder spec

# Modo 2: PRD → Design Técnico
/sdd-builder design @.ai/product/prd.md

# Modo 3: PRD + Design → Tasks executáveis
/sdd-builder tasks @.ai/product/prd.md @.ai/product/design.md
```

---

## O que cada modo faz

| Modo | Entrada | Saída | Propósito |
|------|---------|-------|-----------|
| `spec` | Conversa interativa | `.ai/product/prd.md` | Extrair contexto e definir requisitos |
| `design` | `prd.md` | `.ai/product/design.md` | Traduzir requisitos em arquitetura técnica |
| `tasks` | `prd.md` + `design.md` | `.ai/product/tasks/*.md` | Quebrar em tasks executáveis por agentes RPI |

---

## Fluxo completo

```
Ideia vaga
    ↓
/sdd-builder spec
    ↓
.ai/product/prd.md
    ↓
/sdd-builder design @.ai/product/prd.md
    ↓
.ai/product/design.md
    ↓
/sdd-builder tasks @.ai/product/prd.md @.ai/product/design.md
    ↓
.ai/product/tasks/
    ↓
Execução com RPI
```

---

## Estrutura de output

```
.ai/product/
  prd.md
  design.md
  tasks/
    001-setup-projeto.md
    002-schema-dados.md
    003-autenticacao.md
    ...
```

---

## Estrutura da skill

```
sdd-builder/
  SKILL.md                    ← ponto de entrada da skill
  README.md                   ← este arquivo
  core/
    interview-rules.md        ← protocolo: uma pergunta por vez
    scope-rules.md            ← como definir e proteger escopo
    output-conventions.md     ← convenções de nomes e paths
  modes/
    spec.md                   ← fluxo do modo SPEC
    design.md                 ← fluxo do modo DESIGN
    tasks.md                  ← fluxo do modo TASKS
  templates/
    prd-template.md           ← estrutura do PRD
    design-template.md        ← estrutura do Design Técnico
    task-template.md          ← estrutura de cada Task
  prompts/
    discovery-questions.md    ← banco de perguntas por categoria
    design-rules.md           ← regras para o design técnico
    task-rules.md             ← regras para criação de tasks
  examples/
    prd-example.md            ← PRD de referência completo
    tasks-example.md          ← Task de referência detalhada
  knowledge/
    sdd-principles.md         ← fundamentos do Spec-Driven Development
    rpi-compatibility.md      ← como preparar specs para agentes RPI
```

---

## Princípios

- Uma pergunta por vez na entrevista — nunca questionários
- Nunca inventar escopo não confirmado pelo usuário
- Premissas sempre explicitamente marcadas
- Tasks auto-suficientes para execução por agentes RPI
- Clareza sobre completude — documento curto e claro > longo e confuso
