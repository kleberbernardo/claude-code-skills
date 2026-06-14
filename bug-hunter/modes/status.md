# Modo STATUS — bug-hunter

Relatório visual do estado de todos os bugs rastreados em `.ai/debug/`.

---

## Objetivo

Dar uma visão rápida de quais bugs existem, em que fase cada um está e qual o próximo passo recomendado.

---

## Fluxo de Execução

```
1. Glob .ai/debug/*.md
2. Para cada arquivo, ler o cabeçalho de estado
3. Montar a tabela de progresso
4. Sugerir o próximo passo para o(s) bug(s) em aberto
```

Se `.ai/debug/` não existir ou estiver vazia:
```
Nenhum bug rastreado ainda.

Comece com: /bug-hunter analyze  (e descreva o problema ou cole o stack trace)
```

---

## Formato do Relatório

```
🐛 Bugs rastreados — .ai/debug/

| ID  | Bug                        | Status        | Causa raiz | Plano | Fix  |
|-----|----------------------------|---------------|------------|-------|------|
| 001 | login-loop-infinito        | fixed         | confirmed  | done  | done |
| 002 | saldo-negativo-checkout    | analyzed      | confirmed  | —     | —    |
| 003 | upload-falha-arquivo-grande| investigating | unknown    | —     | —    |

Próximos passos:
- 002 → /bug-hunter plan 002  (causa raiz confirmada, falta planejar)
- 003 → /bug-hunter analyze 003  (análise em andamento — confirme a causa raiz)
```

---

## Mapeamento Status → próximo passo

| Status | Próximo passo sugerido |
|--------|------------------------|
| `investigating` | `/bug-hunter analyze NNN` — confirmar a causa raiz |
| `analyzed` | `/bug-hunter plan NNN` — planejar a correção |
| `planned` | `/bug-hunter fix NNN` — aplicar a correção |
| `fixed` | nenhum — encerrado |

---

## Regras

- Apenas leitura — `status` nunca altera arquivos.
- Ordenar por ID.
- Se um arquivo não tiver cabeçalho de estado válido, exibir `?` nas colunas e sinalizar para revisão.
