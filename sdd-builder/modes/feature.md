# Modo FEATURE — SDD Builder

Especificar uma nova funcionalidade para um produto existente, sem reescrever o PRD inteiro.

---

## Objetivo

Gerar um Feature Spec focado — documento completo de uma funcionalidade incremental — para um produto que já existe (com ou sem prd.md prévio). O Feature Spec é autossuficiente para execução por design e tasks.

---

## Quando usar

- Produto já existe (MVP lançado, em desenvolvimento, ou já maduro)
- Uma funcionalidade nova precisa ser especificada
- Não faz sentido refazer o PRD inteiro

---

## Fluxo de Execução

```
1. Verificar existência do prd.md
   ├── Existe → ler e extrair contexto do produto
   └── Não existe → conduzir mini-entrevista de contexto (3–5 trocas)
2. Perguntar qual feature o usuário quer adicionar
3. Conduzir entrevista focada na feature (7 blocos)
4. Gerar o Feature Spec usando templates/feature-template.md
5. Salvar em .ai/product/features/NNN-nome-da-feature.md
6. Se prd.md existir: atualizar seção de Escopo e User Stories
7. Confirmar e indicar próximo passo
```

---

## Passo 1: Contexto do Produto Existente

### Se `.ai/product/prd.md` existe (ou foi passado como argumento):

Ler o prd.md completo. Extrair:
- Nome e proposta de valor do produto
- Tipos de usuário existentes
- Funcionalidades já especificadas
- Stack técnica definida (se presente no design.md, ler também)

Anunciar ao usuário:
```
Li o PRD existente. O [Nome do Produto] é [proposta de valor em 1 frase].
Já tem as seguintes funcionalidades especificadas: [lista resumida].

Que funcionalidade nova você quer adicionar?
```

### Se `prd.md` não existe:

Fazer mini-entrevista de contexto (máximo 5 trocas, sem aprofundamento):

```
Você tem um produto existente mas sem PRD documentado.
Me conta em uma frase o que o produto faz e quem usa.
```

Cobrir em no máximo 5 trocas:
1. O que é o produto e para quem
2. Quais funcionalidades principais já existem
3. Qual é o stack técnico (se relevante para a feature)

Depois de cobrir o contexto:
```
Entendido. Agora me conta: que funcionalidade nova você quer adicionar?
```

> **Nota:** Não criar um prd.md completo do zero neste modo. Se o usuário quiser documentar o produto todo, orientar: `Para documentar o produto completo, use /sdd-builder spec`.

---

## Passo 2: Entrevista da Feature

Seguir `core/interview-rules.md` (uma pergunta por vez, aprofundar até estar claro).

### Bloco 1 — O que é a feature
- Em uma frase: o que essa funcionalidade faz?
- Para qual usuário ela é destinada — todos ou um tipo específico?
- Qual problema ou necessidade ela resolve?

### Bloco 2 — Motivação e contexto
- Por que essa feature agora? O que motivou o pedido?
- Ela foi solicitada por usuários, por uma necessidade interna, ou por requisito de negócio?
- Existe algum prazo ou dependência que impacta o escopo?

### Bloco 3 — Fluxo principal
- Me conta o passo a passo de como o usuário usaria essa feature
- Onde no produto ela aparece — em qual tela, menu ou contexto?
- Como o usuário chega até ela — é iniciada pelo usuário ou disparada pelo sistema?

### Bloco 4 — Integração com o produto existente
- Quais funcionalidades ou telas atuais essa feature afeta ou toca?
- Ela adiciona dados novos, modifica dados existentes, ou apenas lê?
- Tem alguma regra existente do produto que precisa ser respeitada ou estendida?

### Bloco 5 — Regras de negócio
- Tem alguma regra específica que essa feature deve obedecer?
- Quem pode usar essa feature — todos os usuários ou só alguns perfis?
- Tem algum limite, validação ou condição de erro específica?

### Bloco 6 — Critérios de aceite e edge cases
- Como você saberá que essa feature está funcionando corretamente?
- O que acontece quando o usuário comete um erro no fluxo?
- Tem alguma situação de borda que precisa de comportamento específico?

### Bloco 7 — Fora de escopo
- O que você explicitamente NÃO quer nessa implementação?
- Tem alguma extensão óbvia que deve ficar para depois?

---

## Passo 3: Transição para Geração

Quando os 7 blocos estiverem cobertos (ou o usuário pedir encerramento):

```
Entendido. Tenho o que preciso para gerar o Feature Spec.
Vou estruturar agora.
```

---

## Passo 4: Geração do Feature Spec

Usar `templates/feature-template.md` como estrutura.

**Regras de geração:**
- Ser específico: usar linguagem do usuário
- Não inventar funcionalidades não confirmadas
- Registrar premissas com `[ASSUMIDO: ...]`
- User Stories no formato: Como [perfil], quero [ação] para [benefício]
- Critérios de aceite no formato Dado/Quando/Então
- Edge cases derivados da entrevista, não hipotéticos

---

## Passo 5: Salvar o Feature Spec

1. Verificar arquivos existentes em `.ai/product/features/` para definir o número sequencial
2. Salvar como `.ai/product/features/NNN-nome-da-feature.md`
   - Exemplo: `001-exportacao-relatorios.md`, `002-notificacoes-email.md`

---

## Passo 6: Atualizar prd.md (se existir)

Se um `prd.md` existente foi lido no Passo 1:

1. **Adicionar à seção "Escopo Funcional"** (Seção 5 do PRD):
   ```
   | N | [Nome da Feature] | Must-have | [Motivação resumida] |
   ```
   Com nota: `→ Spec completo em features/NNN-nome-da-feature.md`

2. **Adicionar User Stories** à Seção 6 do PRD, no épico correspondente

3. **Não modificar** nenhuma outra seção do prd.md

> Se o prd.md não existir: não criar — apenas gerar o feature spec.

---

## Passo 7: Confirmar e Indicar Próximo Passo

```
✅ Feature Spec salvo em .ai/product/features/NNN-nome-da-feature.md
✅ prd.md atualizado (escopo + user stories)   [se aplicável]

Próximo passo:
/sdd-builder design @.ai/product/prd.md @.ai/product/features/NNN-nome-da-feature.md
```

Se não há prd.md:
```
✅ Feature Spec salvo em .ai/product/features/NNN-nome-da-feature.md

Próximo passo:
/sdd-builder design @.ai/product/features/NNN-nome-da-feature.md
```

---

## Checklist de Qualidade do Feature Spec

Antes de salvar, verificar:

- [ ] Feature descrita em 1–2 frases sem ambiguidade
- [ ] Usuário-alvo identificado (todos ou perfil específico)
- [ ] Motivação clara — por que agora
- [ ] Fluxo principal em steps com pré-condição e resultado esperado
- [ ] Pontos de integração com produto existente listados
- [ ] Regras de negócio verificáveis (condição → resultado)
- [ ] User Stories com critérios de aceite Dado/Quando/Então
- [ ] Edge cases derivados dos fluxos (não inventados)
- [ ] Fora de escopo com pelo menos 2 itens explícitos
- [ ] Premissas registradas
