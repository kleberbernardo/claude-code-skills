# Resolução de ID e Auto-descoberta — bug-hunter

Todos os bugs vivem em `.ai/debug/NNN-slug.md`, onde `NNN` é um número sequencial de 3 dígitos.

---

## Auto-descoberta

Antes de qualquer ação, cada modo executa:

```
Glob .ai/debug/*.md
```

E lê o cabeçalho de estado dos arquivos relevantes.

Se a pasta `.ai/debug/` não existir, criar no modo `analyze` (primeiro bug).

---

## Modo `analyze` — criar ou continuar

```
1. O usuário passou um bug-id?
   - Sim → Glob .ai/debug/NNN-*.md
       - Match → continuar a análise daquele bug (sessão retomada)
       - Sem match → informar e perguntar se deve criar novo
   - Não → criar um bug novo:
       a. Determinar o próximo NNN livre (maior existente + 1, começando em 001)
       b. Gerar um slug curto a partir do problema relatado (ex: login-loop-infinito)
       c. Criar .ai/debug/NNN-slug.md a partir de templates/analysis-template.md
```

---

## Modos `plan` e `fix` — exigem ID

```
1. Glob .ai/debug/NNN-*.md
2. Match único → usar e confirmar o nome encontrado
3. Sem match → listar bugs disponíveis e parar:

   Bug 005 não encontrado.

   Bugs disponíveis:
   - 001 — login-loop-infinito (analyzed)
   - 002 — saldo-negativo-checkout (investigating)

   Use /bug-hunter status para ver o estado completo.

4. Nenhum ID passado → orientar:
   "Informe o ID do bug. Use /bug-hunter status para ver os bugs abertos."
```

---

## Verificação de gates por modo

| Modo | Gate exigido | Se falhar |
|------|--------------|-----------|
| `plan` | `RootCause: confirmed` | "A causa raiz do bug NNN ainda não foi confirmada. Rode /bug-hunter analyze NNN e confirme a análise antes de planejar." |
| `fix` | `Plan: done` | "O bug NNN ainda não tem plano aprovado. Rode /bug-hunter plan NNN antes de corrigir." |

---

## Convenção de slug

- minúsculas, separadas por hífen
- curto e descritivo do sintoma (3–5 palavras)
- estável: uma vez criado, o slug não muda mesmo que a causa raiz se revele diferente do sintoma

Exemplos: `001-token-expira-cedo`, `002-upload-falha-arquivo-grande`, `003-total-pedido-arredonda-errado`.
