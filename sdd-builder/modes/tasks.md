# Modo TASKS — SDD Builder

Quebrar o sistema em tasks executáveis e detalhadas.

---

## Objetivo

Transformar o PRD + Design em um conjunto de tasks pequenas, sequenciais, independentes e executáveis por agentes RPI sem dúvidas.

---

## Pré-requisitos

- `.ai/product/prd.md` deve existir
- `.ai/product/design.md` deve existir
- Se qualquer um faltar: interromper e orientar com os passos anteriores

---

## Fluxo de Execução

```
1. Ler .ai/product/prd.md e .ai/product/design.md completamente
2. Identificar todos os componentes a implementar
3. Planejar a sequência de tasks (vertical, ponta a ponta)
4. Gerar cada task usando templates/task-template.md
5. Salvar em .ai/product/tasks/
6. Confirmar e exibir lista de tasks geradas
```

---

## Passo 1: Leitura e Inventário

Ler ambos os documentos completamente.

Extrair:
- Todos os módulos do Design (seção 5)
- Todos os endpoints/actions (seção 8)
- Todos os fluxos técnicos (seção 9)
- Modelo de dados completo (seção 6)
- Estratégia de autenticação (seção 10)
- Integrações externas (seção 12)
- Estratégia de testes (seção 15)

---

## Passo 2: Planejamento da Sequência

### Princípio de Ordenação

Priorizar **planos verticais**: cada feature entregue ponta a ponta antes da próxima.

```
NÃO FAZER:
  task 001: todos os schemas
  task 002: todos os services
  task 003: todos os endpoints
  task 004: toda a UI

FAZER:
  task 001: setup e fundação
  task 002: schema + service + endpoint + UI da feature A
  task 003: schema + service + endpoint + UI da feature B
  ...
```

### Sequência Padrão de Tasks

1. **Setup e configuração** — ambiente, dependências, estrutura de pastas
2. **Fundação de dados** — schemas base, migrations
3. **Autenticação** — se aplicável: registro, login, sessão
4. **Feature principal 1** — ponta a ponta (dados → domínio → API → UI)
5. **Feature principal 2** — ponta a ponta
6. **Features secundárias** — ponta a ponta por feature
7. **Integrações externas** — cada integração em sua task
8. **Observabilidade** — logging, métricas, alertas
9. **Testes de integração / E2E** — se não cobertos nas tasks anteriores

### Tamanho de Task

- Cada task deve ser executável em uma sessão (1–4 horas estimadas)
- Se uma task parecer muito grande: quebrar em sub-tasks
- Se uma task parecer trivial: agrupar com a anterior se relacionada
- Critério: uma task = um conjunto coeso e testável de mudanças

---

## Passo 3: Geração das Tasks

Para cada task:
- Usar `templates/task-template.md` como estrutura
- Ser específico sobre arquivos, funções e componentes
- Referenciar seções do Design explicitamente
- Incluir critérios de conclusão verificáveis
- Incluir testes obrigatórios específicos (não genéricos)
- Listar edge cases reais derivados do PRD

### Regras de Conteúdo

**Seção 6 — Arquivos envolvidos:**
- Listar paths completos a partir da raiz do projeto
- Se arquivo não existe ainda: marcar como `[novo]`
- Se arquivo existe e será modificado: marcar como `[modificar]`

**Seção 8 — Contratos e regras relevantes:**
- Copiar os tipos e contratos relevantes do Design
- Não resumir — copiar literal para evitar ambiguidade

**Seção 9 — Passos de implementação sugeridos:**
- Sequência concreta, não abstrata
- "Criar arquivo X com função Y que recebe Z e retorna W"
- Não "implementar a lógica de negócio"

**Seção 10 — Critérios de conclusão:**
- Verificáveis: cada item é sim/não
- Cobrem todos os aspectos: funcionamento, testes, edge cases

**Seção 11 — Testes obrigatórios:**
- Unitários: funções/classes específicas com casos específicos
- Integração: fluxos específicos com dados específicos
- Manual: passos exatos de validação visual/interativa

---

## Passo 4: Criar Pasta e Salvar

1. Criar pasta `.ai/product/tasks/` se não existir
2. Salvar cada task com nomenclatura `NNN-nome.md`
3. Seguir `core/output-conventions.md` para numeração

---

## Passo 5: Confirmar com Lista

Após salvar todas as tasks:

```
✅ Tasks geradas em .ai/product/tasks/

## Tasks criadas

| # | Arquivo | Descrição |
|---|---------|-----------|
| 001 | 001-setup-projeto.md | Configuração inicial do projeto |
| 002 | 002-schema-usuario.md | Schema e migration da entidade User |
| 003 | ... | ... |

Total: N tasks

Pronto para execução com RPI.
```

---

## Checklist de Qualidade das Tasks

Para cada task, verificar:

- [ ] Objetivo em 1–2 frases claras
- [ ] Contexto explica POR QUÊ (não apenas o quê)
- [ ] Dependências listam tasks anteriores obrigatórias
- [ ] Escopo é específico (não "implementar o módulo X")
- [ ] Fora de escopo lista o que NÃO fazer
- [ ] Arquivos listados com paths completos e status [novo/modificar]
- [ ] Contratos copiados literalmente do Design
- [ ] Passos de implementação são acionáveis (verbo + substantivo + detalhes)
- [ ] Critérios de conclusão são verificáveis
- [ ] Testes são específicos (não "testar a funcionalidade")
- [ ] Edge cases derivados do PRD, não inventados
