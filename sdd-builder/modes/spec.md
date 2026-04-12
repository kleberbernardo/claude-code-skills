# Modo SPEC — SDD Builder

Conduzir entrevista interativa e gerar PRD completo.

---

## Objetivo

Transformar uma ideia (vaga ou detalhada) em um PRD preciso, sem ambiguidade, pronto para virar design técnico.

---

## Fluxo de Execução

```
1. Receber a ideia inicial (ou pedir que descreva)
2. Avaliar o nível de completude do contexto
3. Conduzir entrevista (seguir core/interview-rules.md)
4. Cobrir todos os tópicos obrigatórios
5. Anunciar geração do PRD
6. Gerar o PRD usando templates/prd-template.md
7. Salvar em .ai/product/prd.md
8. Exibir confirmação e próximo passo
```

---

## Passo 1: Recepção da Ideia

**Se o usuário não descreveu nada além do comando:**
```
Vamos começar. Me conta a ideia — pode ser uma frase curta ou um parágrafo.
```

**Se o usuário descreveu a ideia junto ao comando:**
Pular para o Passo 2 diretamente.

---

## Passo 2: Avaliação de Completude

Antes de perguntar, avaliar o que já está presente na ideia inicial:

| Elemento | Presente? | Ação se ausente |
|----------|-----------|-----------------|
| O que é o produto | Sim/Não | Primeira pergunta |
| Problema que resolve | Sim/Não | Segunda pergunta prioritária |
| Público-alvo | Sim/Não | Terceira prioridade |
| Fluxos principais | Sim/Não | Cobrir na entrevista |
| Funcionalidades | Sim/Não | Explorar e priorizar |
| Restrições técnicas | Sim/Não | Perguntar no final |

---

## Passo 3: Entrevista

Seguir `core/interview-rules.md` rigorosamente.

### Tópicos obrigatórios a cobrir (em ordem de prioridade):

**Bloco 1 — Problema e Público**
- Qual problema exato está sendo resolvido?
- Quem exatamente sofre com esse problema? (específico)
- Como essa pessoa resolve hoje? Por que é insatisfatório?
- O que muda na vida dessa pessoa com o produto?

**Bloco 2 — Produto e Escopo**
- O que o produto faz na sua forma mais essencial?
- Quais são as 3–5 funcionalidades sem as quais o produto não existe?
- O que é importante mas pode ficar para depois?
- O que definitivamente não faz parte deste produto?

**Bloco 3 — Fluxos**
- Me conta o passo a passo do fluxo principal (como o usuário usaria da primeira vez)
- Existe mais de um tipo de usuário com permissões diferentes?
- O que acontece quando o usuário comete um erro nesse fluxo?

**Bloco 4 — Regras e Restrições**
- Existe alguma regra de negócio específica que o produto deve obedecer?
- Há integrações com sistemas externos?
- Existe stack tecnológica definida ou restrição de plataforma?

**Bloco 5 — Sucesso**
- Como você saberá que o produto funcionou? Qual métrica muda?
- Qual é o critério mínimo de aceite para considerar a primeira versão pronta?

---

## Passo 4: Transição para Geração

Quando todos os tópicos obrigatórios tiverem sido cobertos (ou após 12 rodadas):

```
Entendido. Tenho o que preciso para gerar o PRD.
Vou estruturar agora com base em tudo que você me contou.
```

Não perguntar se pode gerar. Apenas gerar.

---

## Passo 5: Geração do PRD

Usar `templates/prd-template.md` como estrutura base.

**Regras de geração:**
- Ser específico: usar linguagem do usuário, não genérica
- Não inventar funcionalidades não confirmadas
- Marcar premissas com `[ASSUMIDO: ...]`
- Registrar incertezas em "Dúvidas em Aberto"
- Critérios de aceite devem ser verificáveis (sim/não testável)
- Edge cases devem ser realistas, não hipotéticos abstratos

---

## Passo 6: Salvar e Confirmar

1. Criar pasta `.ai/product/` se não existir
2. Salvar o PRD em `.ai/product/prd.md`
3. Exibir confirmação:

```
✅ PRD salvo em .ai/product/prd.md

Próximo passo: /sdd-builder design @.ai/product/prd.md
```

---

## Checklist de Qualidade do PRD

Antes de salvar, verificar:

- [ ] Seção 1: produto descrito em 1–3 frases claras
- [ ] Seção 2: problema articulado sem ambiguidade
- [ ] Seção 3: público específico (não "empresas" ou "qualquer pessoa")
- [ ] Seção 5: escopo funcional lista apenas o confirmado
- [ ] Seção 5: "Fora de Escopo" com pelo menos 3 itens
- [ ] Seção 6: pelo menos 2 fluxos principais descritos em steps
- [ ] Seção 7: regras de negócio são verificáveis (condição → resultado)
- [ ] Seção 10: critérios de aceite são testáveis
- [ ] Seção 11: edge cases incluem falhas reais, não apenas "happy path"
- [ ] Seção 14: premissas registradas explicitamente
- [ ] Seção 16: dúvidas em aberto registradas
