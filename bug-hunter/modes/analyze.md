# Modo ANALYZE — bug-hunter

Entender o problema e chegar à **causa raiz confirmada**. É o coração do skill e o único modo iterativo.

---

## Objetivo

Produzir um documento que responde, com evidência: **"o que dispara o bug, onde o comportamento diverge do esperado, e por quê"** — e obter a confirmação do usuário de que a análise está correta.

Não planejar a correção. Não tocar no código. Apenas entender e provar.

---

## Resolução do ID

Ver `core/id-resolution.md`.
- Com ID → retomar/continuar a análise de um bug existente.
- Sem ID → criar um novo bug no próximo `NNN` livre, a partir de `templates/analysis-template.md`.

---

## Fluxo de Execução

```
SUB-FASE A — COMPREENSÃO
1. Ler o que o usuário relatou (descrição / stack trace / print / esperado vs observado)
2. Avaliar suficiência da informação (core/clarification-rules.md)
3. Se insuficiente → pedir detalhes / spec / URL e PARAR até receber
4. Se há URL de spec → WebFetch e registrar o comportamento correto como fonte da verdade

SUB-FASE B — ANÁLISE DA CAUSA RAIZ
5. Reproduzir o bug (rodar, ou reconstruir o gatilho a partir do código)
6. Inspecionar o código em profundidade (prompts/root-cause-rules.md)
7. Construir o passo a passo: gatilho → ponto de divergência
8. Explicar o porquê, com evidência arquivo:linha
9. Salvar/atualizar .ai/debug/NNN-*.md
10. Apresentar a análise e PERGUNTAR se está correta

GATE
11. Confirmado  → RootCause: confirmed, Status: analyzed → sugerir /bug-hunter plan NNN
    Refutado    → incorporar o novo insight, voltar ao passo 6, reapresentar
```

---

## Sub-fase A — Compreensão

Extrair do relato do usuário:
- **Comportamento observado** (o que acontece de errado)
- **Comportamento esperado** (o que deveria acontecer) — e a sua **fonte** (spec, doc, regra, intuição do usuário)
- **Gatilho/reprodução** (o que dispara)
- **Evidência** (stack trace, erro, log, print, valores)
- **Contexto** (branch, ambiente, quando começou)

Aplicar `core/clarification-rules.md`. Se o comportamento *correto* for ambíguo, **pedir a spec ou a URL** antes de prosseguir — e usar `WebFetch` na URL fornecida. Nunca presumir qual é o comportamento certo.

---

## Sub-fase B — Análise da causa raiz

Esta é a etapa mais crítica. Seguir `prompts/root-cause-rules.md`.

### Princípios
- **Sintoma ≠ causa.** Pergunte "por quê?" repetidamente até alcançar a origem.
- **Hipótese + evidência.** Toda afirmação aponta para `arquivo:linha`. Sem evidência, é especulação — marque como tal.
- **Reprodução primeiro.** Entenda o caminho real do dado/controle antes de afirmar onde quebra.

### Roteiro
1. Se há stack trace: começar pelo frame mais profundo dentro do projeto e subir a pilha.
2. Se não há: rastrear o fluxo a partir do ponto de entrada (rota, handler, função) com `Grep`/`Read`.
3. Localizar o **ponto exato de divergência**: a linha onde o estado/valor passa a estar errado.
4. Distinguir **causa raiz** de **causas contribuintes** e de **sintomas a jusante**.
5. Verificar `git log`/`git blame` do trecho quando "funcionava antes" — identificar a mudança que introduziu o defeito.
6. Considerar e descartar hipóteses alternativas explicitamente (por que NÃO é X).

### O que NÃO fazer
- Não propor a correção ainda (isso é o modo `plan`).
- Não corrigir "de passagem".
- Não ampliar para outros bugs encontrados — registrá-los como observação e seguir.

---

## Salvar

Salvar/atualizar `.ai/debug/NNN-slug.md` usando `templates/analysis-template.md`:
- Preencher Compreensão e Análise da Causa Raiz.
- Manter `RootCause: unknown` até a confirmação do usuário.
- `Status: investigating`.

---

## Apresentar e pedir confirmação

Apresentar ao usuário, de forma legível no chat:
- O passo a passo da causa raiz (numerado, do gatilho ao ponto de falha).
- O porquê, com as referências `arquivo:linha`.
- Hipóteses alternativas consideradas e descartadas.
- Lacunas/suposições, se houver.

Encerrar com a pergunta de confirmação:

```
Essa é a análise da causa raiz. Ela está correta?

- Se SIM, confirmo a causa raiz e seguimos para o plano de correção (/bug-hunter plan NNN).
- Se NÃO, me diga o que não bate ou o que considerar — eu aprofundo a análise.
```

---

## Gate de confirmação

### Usuário confirma
```
RootCause: confirmed
Status: analyzed
```
Responder:
```
✅ Causa raiz confirmada e registrada em .ai/debug/NNN-slug.md

Próximo passo: /bug-hunter plan NNN
```

### Usuário refuta
- Tratar a refutação como **novo insight**, não como fim.
- Reabrir a Sub-fase B com a nova informação: revisar hipóteses, inspecionar o que foi apontado, aprofundar.
- Atualizar o arquivo e reapresentar.
- `RootCause` permanece `unknown`. Repetir até a confirmação.

---

## Checklist de Qualidade da Análise

- [ ] O comportamento esperado tem fonte clara (spec/URL/regra), não suposição
- [ ] O gatilho de reprodução está descrito
- [ ] O passo a passo vai do gatilho até o ponto exato de divergência
- [ ] Cada afirmação aponta para `arquivo:linha`
- [ ] Causa raiz está distinguida de sintomas e causas contribuintes
- [ ] Pelo menos uma hipótese alternativa foi considerada e descartada com motivo
- [ ] Nenhuma correção foi proposta (isso é o modo plan)
- [ ] A pergunta de confirmação foi feita ao usuário
