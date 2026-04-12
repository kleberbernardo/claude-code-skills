# Modo NEXT — rpi-builder

Identificar e sugerir o próximo passo no fluxo RPI sem executar nada.

---

## Objetivo

Responder: **"qual é o próximo comando que devo executar?"** — com o comando exato pronto para copiar.

---

## Fluxo de Execução

```
1. Listar e ordenar todas as tasks
2. Para cada task (em ordem):
   a. Verificar se está done → pular
   b. Verificar se research existe → se não: sugerir research
   c. Verificar se plan existe → se não: sugerir plan
   d. Verificar se implementation está done → se não: sugerir implement
3. Primeira sugestão encontrada → exibir e parar
4. Se todas done → informar conclusão
```

---

## Algoritmo de Decisão

```
tasks = listar .ai/product/tasks/ ordenadas por número

Para task em tasks:

    Se status == done:
        continuar para próxima

    research_existe = arquivo .ai/product/research/NNN-nome.md existe
    plan_existe = arquivo .ai/product/plans/NNN-nome.md existe
    impl_done = campo Implementation == done no arquivo da task

    Se não research_existe:
        exibir: [caso 1 — sugerir research]
        parar

    Se não plan_existe:
        exibir: [caso 2 — sugerir plan]
        parar

    Se não impl_done:
        exibir: [caso 3 — sugerir implement]
        parar

Se nenhuma task pendente:
    exibir: [caso 4 — tudo concluído]
```

---

## Formatos de Saída

### Caso 1 — Research pendente

```
Next: Task 001 - Setup Auth
Phase: Research

The task has no research file yet. Research the codebase before planning.

Command:
/rpi-builder research @.ai/product/tasks/001-setup-auth.md
```

### Caso 2 — Plan pendente

```
Next: Task 001 - Setup Auth
Phase: Plan

Research is done. Generate the technical plan before implementing.

Command:
/rpi-builder plan @.ai/product/tasks/001-setup-auth.md
```

### Caso 3 — Implement pendente

```
Next: Task 001 - Setup Auth
Phase: Implement

Plan is ready. Execute the implementation.

Command:
/rpi-builder implement @.ai/product/plans/001-setup-auth.md
```

### Caso 4 — Task in-progress (sessão retomada)

Quando uma task está `in-progress` e já tem research e plan mas implementation ainda não está done:

```
Next: Task 002 - Login Endpoint
Phase: Implement
Note: This task was in progress. The plan exists — continue from where it stopped.

Command:
/rpi-builder implement @.ai/product/plans/002-login-endpoint.md
```

### Caso 5 — Tudo concluído

```
All tasks are completed. ✅

Total: N tasks done.

Next: consider running /sdd-builder tasks if new features were added to the PRD.
```

---

## Regras do modo NEXT

- Nunca executar nenhuma fase automaticamente
- Exibir apenas UMA sugestão por vez
- Sempre incluir o comando exato pronto para copiar
- Explicar brevemente o motivo da sugestão (1 linha)
- Não listar todas as tasks — apenas a próxima
- Se quiser ver o panorama geral: usar `/rpi-builder status`
