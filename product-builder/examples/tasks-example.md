# Tasks Example — FreelaTask

Exemplo de tasks geradas para a feature "Criar Tarefa" do FreelaTask.

---

## FEAT-001 — Criar Tarefa

### BE-TASK-001: Endpoint POST /api/tarefas

```markdown
## Escopo
Criar endpoint que recebe dados de uma nova tarefa e retorna a tarefa criada com ID.

## Endpoint
POST /api/tarefas
Request: { titulo, cliente, valor, status?, prioridade? }
Response 201: { id, titulo, cliente, valor, status, prioridade, criadoEm }
Response 400: { error: string, field?: string }

## Validações
- titulo: obrigatório, min 3, max 100 chars
- cliente: obrigatório, min 2, max 100 chars
- valor: obrigatório, number, > 0

## Dependências
Nenhuma (primeira task)

## Critérios de Aceite
AC-T001: POST com dados válidos → 201 com objeto completo
AC-T002: Campo obrigatório vazio → 400 com field
AC-T003: Valor negativo → 400 com field: "valor"
```

### FE-TASK-001: Modal de Nova Tarefa

```markdown
## Escopo
Implementar modal com formulário para criar nova tarefa.
Integrar com BE-TASK-001.

## Tela
Modal com campos: Título, Cliente, Valor, Status (select), Prioridade (select)

## Estados
- Idle: formulário vazio, botão "Adicionar" disabled
- Preenchendo: botão habilita quando campos obrigatórios preenchidos
- Submitting: botão "Adicionando..." + disabled
- Success: fechar modal + toast "Tarefa criada!"
- Error: manter modal aberto + mensagem de erro

## Critérios de Aceite
AC-001: Campos obrigatórios vazios → botão "Adicionar" disabled
AC-002: Submit com dados válidos → modal fecha + tarefa aparece na lista
AC-003: Erro de conexão → mensagem "Não foi possível criar. Tente novamente."
AC-004: Em mobile 375px → modal ocupa 90% da tela, todos campos acessíveis
```

### QA-TASK-001: QA — FEAT-001

```markdown
## Checklist
- [ ] Happy path: criar tarefa → aparece na lista com valor em BRL
- [ ] Validação: campos obrigatórios bloqueam submit
- [ ] Loading: botão muda durante submit
- [ ] Mobile: modal usável em 375px
- [ ] Painel financeiro atualiza após criar tarefa
```
