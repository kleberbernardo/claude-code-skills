# Workflow — Product Builder

Fluxo completo de construção de produto do zero até QA.

---

## Sequência Obrigatória

```
MVP → PRD → TASKS → PLAN → EXECUTE → QA
```

Cada etapa depende da anterior. Não pule. Se artefato anterior não existir, instrua o usuário a executar a etapa faltante.

---

## Estado do Produto

Antes de qualquer modo, verificar qual etapa está completa:

```bash
# Verificar estado atual
ls .ai/product/01-discovery/mvp.md       # MVP completo?
ls .ai/product/02-prd/prd.md             # PRD completo?
ls .ai/product/03-tasks/tasks-overview.md # Tasks geradas?
ls .ai/product/04-plans/                  # Plans existem?
ls .ai/product/05-execution/              # Execução iniciada?
ls .ai/product/06-qa/                     # QA realizado?
```

---

## Etapa 1: MVP

**Input:** Ideia verbal do usuário  
**Output:** `.ai/product/01-discovery/mvp.md` + `user-journeys.md` + `screen-map.md`  
**Gatilho para próxima etapa:** MVP aprovado pelo usuário

### O que define um MVP aprovado
- Problema claramente articulado
- Público-alvo definido
- Features essenciais listadas (máx 8)
- Jornadas principais mapeadas
- Mapa de telas em alto nível
- Usuário confirmou o documento

---

## Etapa 2: PRD

**Input:** `.ai/product/01-discovery/mvp.md`  
**Output:** `.ai/product/02-prd/prd.md` + features individuais em `02-prd/features/FEAT-XXX/`  
**Gatilho para próxima etapa:** PRD com todas as features detalhadas

### O que define um PRD completo
- Cada feature do MVP documentada
- Regras de negócio explícitas
- Fluxos de usuário por feature
- Estados de UI mapeados (loading, error, empty, success)
- Validações e mensagens de erro definidas
- Critérios de aceite escritos
- Dependências entre features mapeadas

---

## Etapa 3: Tasks

**Input:** `.ai/product/02-prd/prd.md` + features  
**Output:** `.ai/product/03-tasks/tasks-overview.md` + tasks individuais por feature

### O que define tasks completas
- Cada feature tem tasks de backend E frontend E QA
- IDs únicos e sequenciais (BE-TASK-001, FE-TASK-001, QA-TASK-001)
- Cada task tem: escopo, dependências, critérios de aceite
- Tasks são independentes e executáveis por uma pessoa
- Tasks-overview tem lista completa com status

---

## Etapa 4: Plan

**Input:** Task específica + codebase atual  
**Output:** `.ai/product/04-plans/features/FEAT-XXX/[task-id]-plan.md`

### O que define um plano completo
- Análise do projeto existente feita
- Arquivos a criar/modificar listados
- Decisões técnicas justificadas
- Passos de implementação numerados
- Critérios de aceite técnicos definidos
- Edge cases mapeados

---

## Etapa 5: Execute

**Input:** Plano aprovado + task-id  
**Output:** Código implementado + `.ai/product/05-execution/logs/[task-id].log.md`

### O que define execução bem-sucedida
- Apenas o escopo da task foi implementado
- Padrões do projeto respeitados
- Sem mudanças fora do plano
- Log de mudanças atualizado

---

## Etapa 6: QA

**Input:** Critérios de aceite + implementação  
**Output:** `.ai/product/06-qa/reports/[feat-id]-qa-report.md`

### O que define QA completo
- Todos os critérios de aceite verificados
- Status: passou / falhou / parcial
- Gaps identificados
- Bloqueadores listados
- Sugestões de melhoria (não bloqueantes)

---

## Navegação entre Modos

| De | Para | Condição |
|----|------|----------|
| mvp | prd | mvp.md aprovado |
| prd | tasks | prd.md com todas features |
| tasks | plan | task selecionada |
| plan | execute | plano revisado |
| execute | qa | task implementada |
| qa | plan | se QA falhou, re-planejar |
| qa | tasks | se nova feature necessária |

---

## Sinais de Alerta

- Usuário pede execute sem plan → recusar, instruir a rodar `plan` primeiro
- Usuário pede tasks sem prd → recusar, instruir a rodar `prd` primeiro
- PRD vago demais → aprofundar com perguntas antes de gerar tasks
- Plano contradiz codebase existente → alertar e reconciliar
