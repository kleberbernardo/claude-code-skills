# Status Management — rpi-builder

Como ler, interpretar e atualizar o status das tasks.

---

## Formato de Status no Arquivo de Task

O bloco de status deve aparecer logo abaixo do título principal, antes de qualquer outra seção:

```markdown
# Task 001 - Nome da tarefa

Status: pending
Research: not-started
Plan: not-started
Implementation: not-started

---

## 1. Objetivo
...
```

---

## Valores Válidos

### Campo `Status`
| Valor | Significado |
|-------|-------------|
| `pending` | Task ainda não iniciada |
| `in-progress` | Task em execução (research, plan ou implement em andamento) |
| `done` | Task 100% concluída (research + plan + implement todos done) |

### Campo `Research`
| Valor | Significado |
|-------|-------------|
| `not-started` | Research não realizado |
| `done` | Research completo e salvo |

### Campo `Plan`
| Valor | Significado |
|-------|-------------|
| `not-started` | Plan não gerado |
| `done` | Plan completo e salvo |

### Campo `Implementation`
| Valor | Significado |
|-------|-------------|
| `not-started` | Implementação não iniciada |
| `done` | Implementação completa e validada |

---

## Regras de Atualização

### Após `research` concluído
```
Research: done
Status: in-progress  (se era pending)
```

### Após `plan` concluído
```
Plan: done
Status: in-progress  (se ainda não estava)
```

### Após `implement` concluído com sucesso
```
Implementation: done
Status: done
```

### Após `implement` incompleto (sessão interrompida ou erro)
```
Implementation: not-started  (não alterar — não existe "parcial")
Status: in-progress
```

---

## Como Ler o Status

A skill deve ler os campos de status usando busca por padrão no topo do arquivo.

Se os campos não existirem no arquivo:
- Assumir `Status: pending`
- Assumir `Research: not-started`
- Assumir `Plan: not-started`
- Assumir `Implementation: not-started`

Se o campo existir com valor diferente dos válidos:
- Tratar como o valor mais conservador (ex: valor desconhecido em Research → `not-started`)

---

## Como Atualizar o Status

Usar o tool Edit para modificar o bloco de status no topo do arquivo da task.

Localizar o bloco:
```
Status: [valor]
Research: [valor]
Plan: [valor]
Implementation: [valor]
```

Substituir os valores correspondentes.

**Nunca modificar conteúdo fora do bloco de status ao atualizar o status.**

---

## Inserção de Bloco de Status em Task sem Status

Se a task não tiver o bloco de status, inserir logo após o título principal:

```markdown
# Task 001 - Nome

Status: pending
Research: not-started
Plan: not-started
Implementation: not-started

---
```

Inserir antes da primeira seção `## 1.`.

---

## Consistência com Arquivos de Research e Plan

Ao ler o status de uma task, cruzar com os arquivos físicos:

| Situação | Ação |
|----------|------|
| Task diz `Research: not-started` mas `research/001.md` existe | Atualizar task para `Research: done` |
| Task diz `Plan: not-started` mas `plans/001.md` existe | Atualizar task para `Plan: done` |
| Task diz `Status: pending` mas research e plan existem | Atualizar para `in-progress` |
| Task diz `Status: done` mas implementation não está done | Corrigir: `Status: in-progress` |

Sempre confiar nos arquivos físicos sobre os metadados da task.
