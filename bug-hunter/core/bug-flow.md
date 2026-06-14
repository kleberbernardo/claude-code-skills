# Fluxo Completo — bug-hunter

Este documento descreve o ciclo de vida de um bug e os gates entre os modos.

---

## Visão geral

```
                  ┌─────────────────────────────────────────┐
                  │  analyze                                 │
  problema  ───▶  │  1. entender (clarificar se ambíguo)     │
  (descrição/     │  2. reproduzir                           │
   stack trace/   │  3. analisar o código em profundidade    │
   comportamento) │  4. passo a passo da causa raiz          │
                  │  5. apresentar ao usuário ◀──┐           │
                  └──────────────┬───────────────┼───────────┘
                                 │               │ "não está correta,
                       usuário confirma?         │  considere X"
                                 │ sim           │ (novos insights)
                                 ▼               │
                       RootCause: confirmed ─────┘
                                 │
                  ┌──────────────▼───────────────┐
                  │  plan                         │
                  │  onde / o quê / por quê       │
                  │  impacto, riscos, testes      │
                  └──────────────┬────────────────┘
                                 │ usuário aprova
                                 ▼
                          Plan: done
                                 │
                  ┌──────────────▼───────────────┐
                  │  fix                          │
                  │  aplicar correção mínima      │
                  │  corrigir/criar testes        │
                  │  validar (falha antes/passa)  │
                  └──────────────┬────────────────┘
                                 ▼
                          Status: fixed
```

---

## Modo `analyze`

**Objetivo:** chegar à causa raiz confirmada. É o modo mais importante e o único iterativo.

Tem duas sub-fases sequenciais:

### Sub-fase A — Compreensão
- Ler o que o usuário relatou (descrição, stack trace, print, comportamento esperado vs observado).
- Avaliar se há informação suficiente para investigar. Se **não**, aplicar `core/clarification-rules.md`:
  pedir detalhes, a spec do comportamento correto, ou a **URL** da página/doc — e parar até receber.
- Não avançar para a análise com base em suposição sobre qual seria o comportamento correto.

### Sub-fase B — Análise da causa raiz
- Reproduzir o bug (rodar, ou descrever passo a passo o gatilho a partir do código).
- Inspecionar o código em profundidade seguindo `prompts/root-cause-rules.md`.
- Construir o passo a passo: do gatilho até o ponto exato onde o comportamento diverge do esperado.
- Explicar **por que** acontece, com evidência (`arquivo:linha`).
- Apresentar a análise ao usuário e **perguntar se está correta**.

### Gate de confirmação
- **Usuário confirma** → marcar `RootCause: confirmed`, `Status: analyzed`. Sugerir `/bug-hunter plan NNN`.
- **Usuário refuta** → tratar a refutação como novo insight, ajustar/aprofundar a análise, reapresentar.
  Repetir quantas vezes for necessário. `RootCause` continua `unknown`.

> A skill **nunca** marca a causa raiz como confirmada por conta própria.

---

## Modo `plan`

**Pré-requisito (gate):** `RootCause: confirmed`. Se não estiver, parar e orientar a rodar `analyze` primeiro.

**Objetivo:** descrever a correção com precisão suficiente para execução sem improviso.

- Para cada ponto de mudança: arquivo, trecho exato (antes), mudança proposta e o **porquê** (ligado à causa raiz).
- Mapear impacto e efeitos colaterais (quem mais usa o código tocado).
- Definir a estratégia de teste: qual teste falha hoje, qual teste protegerá o novo comportamento.
- Apresentar e aguardar aprovação do usuário antes de marcar `Plan: done`.

---

## Modo `fix`

**Pré-requisito (gate):** `Plan: done`. Se não estiver, parar e orientar a rodar `plan` primeiro.

**Objetivo:** aplicar a correção exatamente como planejada, com testes.

- Confirmar que o código ainda corresponde ao que o plano descreveu (estado pode ter mudado).
- Aplicar a mudança mínima.
- Implementar/ajustar testes: idealmente confirmar que o teste **falha antes** e **passa depois**.
- Rodar a suíte relevante.
- Atualizar o arquivo do bug (`Fix: done`, `Status: fixed`) com o registro do que foi alterado.

---

## Sessões interrompidas

Toda sessão começa lendo o arquivo do bug e seu cabeçalho de estado:

- `Status: investigating` → continuar/refinar a análise no modo `analyze`.
- `Status: analyzed` → próximo passo é `plan`.
- `Status: planned` → próximo passo é `fix`.
- `Status: fixed` → encerrado; só reabrir com confirmação explícita.
