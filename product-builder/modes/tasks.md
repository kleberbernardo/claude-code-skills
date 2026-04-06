# Modo Tasks — Product Builder

Fluxo de quebra de features em tasks executáveis de backend, frontend e QA.

---

## Objetivo

Transformar o PRD em tasks pequenas, independentes e executáveis — separadas por camada (BE/FE/QA).

---

## Pré-requisito

Verificar se `.ai/product/02-prd/prd.md` existe com features documentadas.

Se não existir:
```
Tasks requerem PRD completo. Execute primeiro: /product-builder prd
```

---

## Fluxo de Execução

```
1. Ler PRD e listar features
2. Para cada feature:
   a. Quebrar em tasks de backend
   b. Quebrar em tasks de frontend
   c. Criar task de QA
3. Gerar tasks-overview.md
4. Salvar todos os arquivos
5. Apresentar sumário
```

---

## Passo 1: Ler PRD

Ler `.ai/product/02-prd/prd.md` e `.ai/product/02-prd/features/*/feature.md`.

Extrair por feature:
- Endpoints necessários (backend)
- Telas necessárias (frontend)
- Critérios de aceite (para QA)

---

## Passo 2: Quebra de Backend Tasks

### Quando criar tasks separadas de backend:
- Cada endpoint diferente → 1 task
- Migrations de banco → task própria
- Autenticação/autorização → task própria
- Integrações externas → task própria
- Webhooks → task própria

### Estrutura de uma Backend Task
```
# [BE-TASK-001] [Título descritivo]

## Contexto
[Qual feature pertence, por que essa task existe]

## Escopo
[O que exatamente deve ser implementado]

## Endpoints
- METHOD /path → [descrição]
  - Request: [schema]
  - Response: [schema]
  - Erros: [lista de erros possíveis]

## Banco de Dados
- Tabelas/coleções afetadas
- Migrations necessárias
- Índices necessários

## Regras de Negócio
[Lógicas específicas a implementar]

## Validações
[Por campo: regra + código de erro]

## Dependências
- Requer: [lista de tasks que precisam estar prontas]
- Bloqueia: [lista de tasks que dependem desta]

## Critérios de Aceite
[ACs técnicos — formato Given/When/Then]

## Estimativa
[XS | S | M | L | XL]
```

---

## Passo 3: Quebra de Frontend Tasks

### Quando criar tasks separadas de frontend:
- Cada tela nova → 1 task (mínimo)
- Componentes complexos e reutilizáveis → task própria
- Formulários com lógica → task própria
- Integração com nova API → task por contexto
- Fluxos de navegação → task própria se complexo

### Estrutura de uma Frontend Task
```
# [FE-TASK-001] [Título descritivo]

## Contexto
[Qual feature pertence, qual tela/componente]

## Escopo
[O que deve ser implementado — telas, componentes, integrações]

## Telas / Componentes
- [Nome da tela/componente]
  - Layout: [descrição]
  - Dados exibidos: [lista]
  - Ações disponíveis: [lista]

## Estados a implementar
- [ ] Loading (skeleton ou spinner)
- [ ] Success (dados carregados)
- [ ] Error (mensagem + retry)
- [ ] Empty (quando não há dados)
- [ ] Disabled (quando ação não disponível)

## Integrações de API
- Endpoint: [METHOD /path]
- Quando chamar: [trigger]
- Como tratar erros: [comportamento]

## Gerenciamento de Estado
- Estado local vs global
- Dados em cache (se aplicável)

## Navegação
- De onde vem o usuário
- Para onde vai após ação

## Dependências
- Requer: [tasks backend que precisam estar prontas]
- Requer: [tasks frontend com componentes base]

## Critérios de Aceite
[ACs de UI — incluindo responsividade e acessibilidade]

## Estimativa
[XS | S | M | L | XL]
```

---

## Passo 4: Task de QA

Uma task de QA por feature (não por task individual).

### Estrutura de uma QA Task
```
# [QA-TASK-001] QA — [Nome da Feature]

## Escopo
[Quais tasks de BE e FE serão validadas]

## Checklist de Testes

### Funcional
- [ ] Happy path completo
- [ ] Validações de input
- [ ] Permissões e autorização
- [ ] Edge cases listados no PRD

### UI/UX
- [ ] Estados de loading
- [ ] Estados de erro
- [ ] Empty states
- [ ] Responsividade (mobile, tablet, desktop)

### Integração
- [ ] Frontend chama os endpoints corretos
- [ ] Dados persistem corretamente
- [ ] Erros do backend tratados no frontend

### Regressão
- [ ] Features anteriores não foram quebradas

## Critérios de Aceite
[ACs do PRD numerados — todos devem passar]
```

---

## Regras de Quebra de Tasks

### Tasks Devem Ser:
- **Independentes** — executáveis sem depender de outra em andamento
- **Pequenas** — máximo 1-2 dias de trabalho
- **Testáveis** — tem critérios de aceite claros
- **Completas** — entrega valor por si só (não "metade de um formulário")

### Tasks NÃO Devem:
- Misturar backend e frontend na mesma task
- Ser genéricas ("implementar autenticação" → dividir em tasks específicas)
- Ser tão pequenas que geram overhead (1 linha de CSS não é uma task)
- Ter dependências circulares

### Estimativas
| Tamanho | Horas | Exemplo |
|---------|-------|---------|
| XS | < 2h | Adicionar campo a formulário existente |
| S | 2-4h | CRUD simples com validação |
| M | 4-8h | Feature completa com estados |
| L | 1-2d | Feature complexa com integrações |
| XL | > 2d | Dividir em tasks menores |

---

## Passo 5: Gerar tasks-overview.md

```markdown
# Tasks Overview

## Status
- Total: XX tasks
- Backend: XX
- Frontend: XX  
- QA: XX

## Features e Tasks

### FEAT-001 — [Nome]
| ID | Tipo | Título | Deps | Est. | Status |
|----|------|--------|------|------|--------|
| BE-TASK-001 | BE | [título] | - | S | 🔲 |
| FE-TASK-001 | FE | [título] | BE-TASK-001 | M | 🔲 |
| QA-TASK-001 | QA | QA FEAT-001 | FE-TASK-001 | S | 🔲 |

## Ordem Recomendada de Implementação
1. [FEAT-001] BE-TASK-001 — [título]
2. [FEAT-001] FE-TASK-001 — [título]
...
```

---

## Saída no Chat

```markdown
## Tasks Geradas — [Nome do Produto]

**FEAT-001 — Autenticação:** 2 BE + 2 FE + 1 QA = 5 tasks
**FEAT-002 — Dashboard:** 1 BE + 3 FE + 1 QA = 5 tasks
**FEAT-003 — Perfil:** 2 BE + 2 FE + 1 QA = 5 tasks

**Total: 15 tasks salvas em `.ai/product/03-tasks/`**

Para começar: `/product-builder plan FEAT-001/BE-TASK-001`
```
