# Modo MVP — Product Builder

Fluxo completo de descoberta e definição do MVP.

---

## Objetivo

Transformar uma ideia vaga em um documento de MVP claro, aprovado e pronto para virar PRD.

---

## Fluxo de Execução

```
1. Receber ideia inicial
2. Avaliar completude
3. Conduzir entrevista (uma pergunta por vez)
4. Gerar draft do MVP
5. Apresentar ao usuário para aprovação
6. Ajustar com base no feedback
7. Salvar artefatos finais
```

---

## Passo 1: Receber e Avaliar a Ideia

Quando o usuário invocar `/product-builder mvp`, ele pode:
- Descrever a ideia na mesma mensagem
- Não descrever nada (só o comando)

Se não descreveu nada:
```
Olá! Vou te ajudar a definir o MVP do seu produto.

Me conta: qual é a ideia? Pode ser uma frase ou um parágrafo — começa por aí.
```

Se descreveu a ideia, pular para avaliação.

---

## Passo 2: Avaliação da Completude

Verificar se a ideia contém:

| Elemento | Presente? | Ação se ausente |
|----------|-----------|-----------------|
| Problema | Sim/Não | Perguntar qual problema resolve |
| Público | Sim/Não | Perguntar quem é o usuário principal |
| Solução | Sim/Não | Geralmente implícito na ideia |
| Features | Sim/Não | Perguntar quais são as funcionalidades essenciais |
| Context técnico | Sim/Não | Perguntar stack ou constraints se relevante |

---

## Passo 3: Entrevista de Descoberta

Seguir `core/interview-rules.md` rigorosamente.

**Sequência recomendada de perguntas (se necessário):**

1. **Problema** — "Qual é o problema exato que você está resolvendo? Quem sofre com isso hoje?"
2. **Público** — "Me descreve o usuário ideal: quem é, o que faz, por que precisaria do seu produto?"
3. **Dor atual** — "Como esse usuário resolve esse problema hoje? O que é frustrante nessa solução atual?"
4. **Proposta de valor** — "Por que alguém trocaria o que usa hoje pelo seu produto?"
5. **Features essenciais** — "Se você tivesse que lançar em 2 semanas, quais são as 3 funcionalidades sem as quais o produto não existe?"
6. **Features nice-to-have** — "O que é importante mas pode esperar para v2?"
7. **Jornada principal** — "Me conta o passo a passo de como o usuário usaria o produto pela primeira vez."
8. **Constraints** — "Tem alguma limitação técnica, de prazo ou de equipe que eu deva saber?"

---

## Passo 4: Gerar Draft do MVP

Quando tiver contexto suficiente, gerar o documento usando `templates/mvp-template.md`.

**Antes de gerar:**
- Resumir o que entendeu: "Entendi que você quer [X] para [Y], que resolve [Z]. Vou gerar o MVP agora."
- Sinalizar incertezas com `[ASSUMIDO: ...]`

**Apresentar no chat:**
- Mostrar resumo do MVP (não o documento completo)
- Perguntar: "Ficou próximo da sua visão? O que ajustar?"

---

## Passo 5: Ciclo de Aprovação

```
Apresentar draft → Usuário dá feedback → Ajustar → Confirmar
```

Se o usuário aprovar: salvar arquivos e informar próxima etapa.
Se o usuário pedir ajustes: aplicar e repetir.

**Limite:** Máximo 3 ciclos de revisão. Após isso, salvar com status `draft` e instruir o usuário a revisar manualmente.

---

## Passo 6: Salvar Artefatos

Criar os seguintes arquivos:

1. `.ai/product/01-discovery/mvp.md` — documento principal
2. `.ai/product/01-discovery/user-journeys.md` — jornadas detalhadas
3. `.ai/product/01-discovery/screen-map.md` — mapa de telas

---

## Passo 7: Informar Próxima Etapa

```
✅ MVP salvo em .ai/product/01-discovery/mvp.md

Próximo passo: gerar o PRD com `/product-builder prd`
```

---

## Saída Esperada no Chat

Após salvar, mostrar resumo em formato de card:

```markdown
## MVP Definido — [Nome do Produto]

**Problema:** [1 frase]
**Público:** [descrição do usuário]
**Proposta de valor:** [1 frase]

**Features do MVP:**
1. [Feature 1]
2. [Feature 2]
3. [Feature 3]
...

**Fora do MVP:**
- [Item 1]
- [Item 2]

**Próximo passo:** `/product-builder prd`
```

---

## Checklist de Qualidade do MVP

Antes de finalizar, verificar:

- [ ] Problema está articulado em 1-2 frases
- [ ] Público é específico (não "qualquer empresa")
- [ ] Features são 5-8, não 15
- [ ] Cada feature tem motivação clara (por que é essencial?)
- [ ] Jornadas principais estão documentadas
- [ ] Mapa de telas faz sentido para as features
- [ ] "Fora do MVP" lista pelo menos 3 itens
- [ ] Não há contradições entre features e jornadas
