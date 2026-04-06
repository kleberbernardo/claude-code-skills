# Interview Rules — Product Builder

Regras obrigatórias para conduzir entrevistas de descoberta e aprofundamento.

---

## Princípio Central

**Uma pergunta por vez. Sempre.**

Nunca faça 3 perguntas de uma vez. O usuário responde uma, você processa, faz a próxima. Isso gera respostas mais ricas e mantém o usuário engajado.

---

## Quando Fazer Perguntas

### Modo MVP
- Quando a ideia for vaga demais para gerar output
- Quando o problema não estiver claro
- Quando o público-alvo for genérico ("todo mundo", "empresas")
- Quando as features listadas forem contraditórias
- Quando o MVP parecer grande demais para um MVP real

### Modo PRD
- Quando uma feature tiver regras de negócio implícitas
- Quando não souber quem pode fazer o quê (permissões)
- Quando os estados de erro não forem óbvios
- Quando houver dependências não declaradas

---

## Árvore de Decisão por Resposta

```
Resposta recebida
  ↓
É suficiente para avançar?
  ├── Sim → avançar para próxima etapa / próxima pergunta necessária
  └── Não → identificar o GAP mais importante
               ↓
             Formular 1 pergunta sobre esse GAP
               ↓
             Esperar resposta
```

---

## Tipos de Perguntas por Contexto

### Perguntas de Problema
- "Quem exatamente sofre com esse problema? Me descreva a pessoa."
- "O que eles fazem hoje para resolver isso? Por que isso não funciona?"
- "Você já viu alguém pagando por uma solução parecida?"

### Perguntas de Escopo
- "Se você tivesse que lançar em 2 semanas, o que cortaria?"
- "Essa feature é essencial para o primeiro usuário pagar, ou é nice-to-have?"
- "O que acontece se essa feature não existir no lançamento?"

### Perguntas de Fluxo
- "Me conta o passo a passo de como o usuário faria isso hoje."
- "O que acontece quando dá errado? Como o usuário sabe?"
- "Quem precisa aprovar essa ação? Existe hierarquia?"

### Perguntas de Dados
- "Quais informações você precisa guardar sobre isso?"
- "Esse dado muda depois de criado? Quem pode mudar?"
- "Quanto tempo você guarda esse dado? Existe expiração?"

### Perguntas de Integração
- "Isso precisa conectar com algum sistema que já existe?"
- "Existe autenticação? Quem tem acesso a quê?"
- "Isso precisa funcionar offline?"

---

## Sinais de que Precisa Aprofundar

| Sinal | Ação |
|-------|------|
| "É simples" | Nunca é simples. Pedir para detalhar o fluxo. |
| "Igual ao X" | Perguntar o que é diferente do X. |
| "Qualquer usuário" | Perguntar se existe hierarquia de acesso. |
| "Tudo em uma tela" | Listar o que está nessa tela e validar. |
| "Será que precisa?" | Assumir que sim e perguntar o contexto. |
| Resposta < 2 linhas | Aprofundar com "me conta mais sobre isso". |

---

## Sinais de que Pode Avançar

- Problema: articulado com clareza em 1-2 frases
- Público: pessoa específica, não categoria
- Features: lista de 5-8 com motivação clara
- Jornadas: pelo menos 2 fluxos principais descritos
- Constraints: tecnologia, prazo, equipe mencionados (ou explicitamente "sem constraints")

---

## Tom das Perguntas

- Usar segunda pessoa: "você", "seu usuário", "sua ideia"
- Nunca julgativo: não "isso não vai funcionar", mas "me ajuda a entender..."
- Curiosidade genuína: tratar como se fosse o primeiro PM entrevistando o founder
- Validar o que ouviu antes de perguntar: "Entendido — então o problema principal é X. Me conta: [pergunta]"

---

## Limite de Perguntas

- MVP: máximo 8-10 rodadas de pergunta antes de gerar draft
- PRD por feature: máximo 5 rodadas por feature
- Se chegar no limite → gerar melhor versão possível com o que tem e sinalizar as incertezas com `[ASSUMIDO: ...]`
