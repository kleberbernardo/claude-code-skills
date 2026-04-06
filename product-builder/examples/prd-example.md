# PRD Example — Feature: Criar Tarefa

Exemplo de feature.md gerado para FEAT-001.

---

```markdown
# FEAT-001 — Criar Tarefa

## Descrição
Permite ao freelancer cadastrar uma nova tarefa com título, cliente, valor, status e prioridade. A tarefa aparece imediatamente na lista e o painel financeiro é atualizado.

## Regras de Negócio
- RN-001: Título é obrigatório, mín 3 caracteres
- RN-002: Cliente é obrigatório, mín 2 caracteres
- RN-003: Valor é obrigatório, deve ser > R$ 0,00
- RN-004: Status padrão: "pendente"
- RN-005: Prioridade padrão: "média"

## Fluxo do Usuário
1. Clica "Nova Tarefa" no header
2. Modal abre com formulário vazio
3. Preenche campos (Título, Cliente, Valor, Status, Prioridade)
4. Clica "Adicionar Tarefa"
5. Modal fecha
6. Tarefa aparece no topo da lista
7. Painel financeiro atualiza (valor em "A receber")

## Estados de UI

### Loading
"Adicionando..." no botão, campos desabilitados

### Success
Modal fecha, tarefa visível na lista, painel atualizado

### Error: conexão
Modal permanece aberto, mensagem "Não foi possível criar. Tente novamente."

### Error: validação
Mensagem abaixo do campo com erro específico

## Validações
| Campo | Regra | Mensagem |
|-------|-------|---------|
| Título | min 3 chars | "Título muito curto (mín. 3 caracteres)" |
| Cliente | obrigatório | "Informe o nome do cliente" |
| Valor | > 0 | "Informe um valor válido" |

## Critérios de Aceite

[P0] AC-001:
Given formulário preenchido com dados válidos
When usuário clica "Adicionar Tarefa"
Then modal fecha e tarefa aparece na lista com todos os campos corretos

[P0] AC-002:
Given campo "Título" vazio
When usuário clica "Adicionar Tarefa"
Then botão está desabilitado (não executa ação)

[P1] AC-003:
Given usuário em mobile (375px)
When abre modal
Then todos os campos são acessíveis e botão não fica cortado
```
