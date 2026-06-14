# Regras de Esclarecimento — bug-hunter

Quando o problema relatado **não permite investigação confiável**, pare e peça o que falta. Adivinhar o comportamento correto é a forma mais comum de "corrigir" o bug errado.

---

## Quando pedir esclarecimento

Peça mais informação se **qualquer** destes for verdadeiro:

1. **Comportamento esperado ambíguo** — você não tem certeza de qual seria o resultado *correto*.
2. **Sem gatilho de reprodução** — não está claro o que o usuário faz para o bug aparecer.
3. **Sem evidência concreta** — nenhum stack trace, mensagem de erro, print ou valor observado.
4. **Conflito com a spec** — o código parece intencional e você não sabe se é bug ou regra de negócio.
5. **Escopo indefinido** — "está tudo quebrado" sem um caso específico.

> Se há informação suficiente para formular e testar uma hipótese, **não** trave o usuário com perguntas — investigue.

---

## O que pedir (em ordem de prioridade)

### 1. O comportamento correto, com fonte
Quando o ponto de dúvida é "o que *deveria* acontecer":
- Peça a descrição do resultado esperado em um caso concreto.
- Peça a **spec** ou documentação do comportamento correto.
- Se for uma página/feature, peça a **URL** da página ou da doc — para ler a fonte da verdade em vez de presumir.
  Use `WebFetch` na URL fornecida antes de analisar.

### 2. A reprodução
- Passos exatos para disparar o bug.
- Entrada usada (dados, usuário, ambiente).
- Frequência: sempre, intermitente, só em produção?

### 3. A evidência
- Stack trace completo (não truncado).
- Mensagem de erro literal.
- Logs próximos ao momento da falha.
- Print/screenshot quando for visual.
- Valor observado **vs** valor esperado.

### 4. O ambiente (quando relevante)
- Versão/branch onde ocorre.
- Funcionava antes? O que mudou? (commit, deploy, dependência)

---

## Como pedir

- **Uma rodada objetiva**, em lista curta — não um interrogatório longo.
- Diga **por que** precisa de cada item ("preciso da URL da spec para não corrigir contra o comportamento errado").
- Ofereça o caminho mais barato: "cole o stack trace completo" é melhor que "descreva o erro".
- Se o usuário não tiver um item, registre como lacuna e siga com o que dá — declarando a suposição como `[ASSUMIDO: ...]`.

---

## Stack trace como entrada de primeira classe

Quando o usuário cola um stack trace, trate-o como o melhor ponto de partida:

1. Identifique o **frame mais profundo no código do projeto** (ignore frames de libs até cruzar a fronteira do projeto).
2. Vá direto a `arquivo:linha` daquele frame com `Read`.
3. Suba a pilha reconstruindo como os argumentos chegaram ali.
4. Só peça mais contexto se o trace não revelar o gatilho (ex: erro assíncrono sem origem clara).

---

## Antiexemplos

- ❌ Assumir que "data errada" significa fuso horário sem confirmar qual seria a data correta.
- ❌ Começar a corrigir um cálculo sem saber a fórmula esperada — peça a spec/URL.
- ❌ Disparar 12 perguntas quando o stack trace já aponta o arquivo e a linha.
