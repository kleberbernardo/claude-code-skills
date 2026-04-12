# Feature Spec — [Nome da Feature]
<!-- Gerado por /sdd-builder feature -->
<!-- Data: YYYY-MM-DD -->
<!-- Produto: [Nome do produto] -->
<!-- Feature #: NNN -->

---

## 1. Resumo da Feature

**Nome:** [Nome da feature]

**Descrição:** [O que faz em 1–2 frases]

**Produto alvo:** [Nome do produto + link para prd.md se existir]

**Usuário(s) afetado(s):** [Todos / Admin / Usuário comum / outro perfil]

**Motivação:** [Por que essa feature agora — problema ou oportunidade que originou o pedido]

---

## 2. Contexto do Produto

<!-- Resumo do produto existente para dar contexto à feature. Não repetir o prd.md inteiro. -->

**Produto:** [Nome] — [proposta de valor em 1 frase]

**Funcionalidades existentes relevantes:**
- [Feature existente 1 — mencionada porque a nova feature interage com ela]
- [Feature existente 2]

**Stack existente (se conhecido):**
- [Ex: Next.js 15, Postgres, Prisma]

---

## 3. Problema / Oportunidade

**Situação atual:** [O que o usuário não consegue fazer hoje, ou faz de forma inadequada]

**Impacto do problema:** [Consequência prática de não ter essa feature]

**Solução proposta:** [O que a feature resolve, em termos do usuário]

---

## 4. User Stories

<!-- User Stories específicas desta feature. Formato: Como [perfil], quero [ação] para [benefício]. -->

### US-[NNN]-01 — [Título curto]
> Como [tipo de usuário], quero [ação] para [benefício].

Critérios de aceite:
- Dado [contexto], quando [ação], então [resultado esperado]
- Dado [contexto inválido], quando [ação], então [mensagem/comportamento de erro]

---

### US-[NNN]-02 — [Título curto]
> Como [tipo de usuário], quero [ação] para [benefício].

Critérios de aceite:
- Dado [contexto], quando [ação], então [resultado esperado]

---

## 5. Fluxo Principal

**Ator:** [Tipo de usuário]
**Ponto de entrada:** [Onde no produto o usuário acessa essa feature]
**Pré-condição:** [O que deve ser verdade antes de iniciar]

**Steps:**
1. [Ação do usuário]
2. [Resposta do sistema]
3. [Próxima ação]
...

**Resultado esperado:** [O que acontece ao final com sucesso]

**Fluxo de erro principal:** [O que acontece na falha mais provável]

---

## 6. Pontos de Integração com o Produto Existente

<!-- O que esta feature toca, modifica ou depende no produto atual. -->

| Elemento existente | Tipo de impacto | Descrição |
|--------------------|----------------|-----------|
| [Tela / módulo X] | Modificado / Estendido / Apenas leitura | [O que muda] |
| [Entidade de dados Y] | Nova coluna / Nova tabela / Sem alteração | [O que muda] |
| [Endpoint / API Z] | Novo / Modificado / Reutilizado | [O que muda] |

---

## 7. Regras de Negócio

| ID | Regra | Condição | Resultado |
|----|-------|----------|-----------|
| RN-[NNN]-001 | [Nome] | [Quando X] | [Então Y] |
| RN-[NNN]-002 | [Nome] | [Quando X] | [Então Y] |

---

## 8. Requisitos Funcionais

- RF-[NNN]-001: O sistema deve [ação específica]
- RF-[NNN]-002: O sistema deve [ação específica]
- RF-[NNN]-003: O sistema deve [ação específica]

---

## 9. Edge Cases e Cenários de Falha

| # | Cenário | Condição | Comportamento esperado |
|---|---------|----------|----------------------|
| EC-[NNN]-001 | [Nome] | [O que acontece de incomum] | [Como o sistema deve reagir] |
| EC-[NNN]-002 | [Nome] | [O que acontece de incomum] | [Como o sistema deve reagir] |

---

## 10. Fora de Escopo desta Feature

<!-- O que explicitamente NÃO faz parte desta implementação. -->

- [Item 1 — motivo]
- [Item 2 — motivo]
- [Item 3 — motivo]

---

## 11. Premissas

- [ASSUMIDO: descrição da premissa 1]
- [ASSUMIDO: descrição da premissa 2]

---

## 12. Dúvidas em Aberto

| # | Questão | Impacto | Responsável |
|---|---------|---------|-------------|
| 1 | [Dúvida] | [O que bloqueia] | [Quem responde] |
