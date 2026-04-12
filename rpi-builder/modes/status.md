# Modo STATUS — rpi-builder

Exibir o estado atual de todas as tasks para acompanhamento e retomada de trabalho.

---

## Objetivo

Responder: **"onde estamos agora?"** — de forma clara, rápida e objetiva.

---

## Fluxo de Execução

```
1. Listar todos os arquivos em .ai/product/tasks/
2. Para cada task: ler e extrair campos de status
3. Cruzar com existência de arquivos em research/ e plans/
4. Montar tabela de progresso
5. Exibir resumo geral
```

---

## Passo 1: Listagem de Tasks

Usar Glob para listar: `.ai/product/tasks/*.md`

Ordenar por prefixo numérico (001, 002, 003...).

Se a pasta não existir: informar que nenhuma task foi encontrada.

---

## Passo 2: Extração de Status por Task

Para cada arquivo de task:

1. Ler os campos de status no topo do arquivo
2. Se não existirem: assumir `pending` / `not-started`
3. Cruzar com existência física dos arquivos:
   - `.ai/product/research/NNN-task-name.md` → se existe: Research = done
   - `.ai/product/plans/NNN-task-name.md` → se existe: Plan = done

Usar o estado mais atualizado (arquivo físico tem precedência sobre metadado da task).

---

## Passo 3: Extração do ID e Nome

Do nome do arquivo, extrair:
- ID numérico (ex: `001`)
- Nome legível (ex: `setup-auth` → `Setup Auth`)

Para o nome legível: converter kebab-case para Title Case.

---

## Formato de Saída

```
Progress: N/T tasks completed

ID    Task                        Research   Plan   Implement   Status
----------------------------------------------------------------------
001   Setup Auth                  ✅         ✅     ✅          Done
002   Login Endpoint              ✅         ✅     ❌          In Progress
003   Dashboard Base              ✅         ❌     ❌          In Progress
004   Profile Page                ❌         ❌     ❌          Pending
005   Billing Integration         ❌         ❌     ❌          Pending

Summary
-------
Done:         1
In Progress:  2
Pending:      2
Total:        5
```

### Ícones de Status

| Estado | Ícone |
|--------|-------|
| done / ✅ | `✅` |
| not-started / ❌ | `❌` |
| in-progress | `🔄` (apenas no campo Status) |
| pending | sem ícone no campo Status |

### Campo Status (coluna final)

| Valor real | Exibição |
|------------|----------|
| `done` | `Done` |
| `in-progress` | `In Progress` |
| `pending` | `Pending` |

---

## Casos Especiais

**Nenhuma task encontrada:**
```
No tasks found in .ai/product/tasks/

Run /sdd-builder tasks to generate tasks first.
```

**Task sem nome legível (só número):**
- Usar o nome do arquivo como está

**Inconsistência de status:**
- Se arquivo de research existe mas task diz `not-started`: mostrar `✅` (confiar no arquivo)
- Não reportar inconsistências no modo status — apenas mostrar o estado real

---

## Alinhamento de Colunas

Usar espaços para alinhar colunas.
Calcular a largura máxima do campo "Task" e preencher com espaços para alinhamento.
