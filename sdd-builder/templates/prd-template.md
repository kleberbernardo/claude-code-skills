# Product Requirements Document
<!-- Gerado por /sdd-builder spec -->
<!-- Data: YYYY-MM-DD -->

---

## 1. Visão Geral do Produto

<!-- O que é o produto em 2–4 frases. Inclui: nome, proposta de valor central, contexto de mercado/uso. Não lista features ainda. -->

**Nome:** [Nome do produto]

**Proposta de valor:** [O que faz, para quem, e qual diferencial]

**Contexto:** [Onde será usado, em que plataforma, por quem]

---

## 2. Problema a Ser Resolvido

<!-- O problema exato que o produto resolve. Ser específico: quem sofre, quando, com que frequência, qual o impacto. Evitar generalidades como "as empresas precisam de eficiência". -->

**Problema principal:** [Articulado em 1–3 frases]

**Causa raiz:** [Por que o problema existe]

**Como é resolvido hoje:** [Solução atual e suas limitações]

**Impacto do problema não resolvido:** [Consequências para o usuário]

---

## 3. Público-Alvo e Usuários

<!-- Descrição específica de quem usa. Se houver mais de um tipo de usuário, listar cada um. -->

### Usuário Principal

- **Perfil:** [Quem é, o que faz, contexto]
- **Necessidade:** [O que precisa do produto]
- **Frequência de uso:** [Diário, semanal, ocasional]
- **Nível técnico:** [Iniciante, intermediário, avançado]

### Outros Tipos de Usuário (se houver)

| Tipo | Descrição | Permissões | Necessidade |
|------|-----------|------------|-------------|
| [Tipo 1] | [Descrição] | [O que pode fazer] | [Por que usa] |

---

## 4. Objetivos do Produto

<!-- O que o produto precisa alcançar. Deve ser mensurável ou verificável. -->

### Objetivos Primários
1. [Objetivo 1 — específico e mensurável]
2. [Objetivo 2]

### Objetivos Secundários
1. [Objetivo secundário 1]

---

## 5. Escopo Funcional

### Incluído nesta versão

| # | Funcionalidade | Prioridade | Motivação |
|---|---------------|------------|-----------|
| 1 | [Nome] | Must-have | [Por que é essencial] |
| 2 | [Nome] | Must-have | [Por que é essencial] |
| 3 | [Nome] | Should-have | [Por que é importante] |
| 4 | [Nome] | Nice-to-have | [Por que seria útil] |

### Fora de Escopo

<!-- Lista explícita do que NÃO está incluído nesta versão. Importante para alinhar expectativas. -->

- [Item 1 — motivo por que está fora]
- [Item 2]
- [Item 3]

---

## 6. Fluxos Principais

<!-- Sequência passo a passo dos fluxos principais. Incluir o happy path e o caminho de erro principal. -->

### Fluxo 1 — [Nome do fluxo]

**Ator:** [Tipo de usuário]
**Pré-condição:** [O que deve ser verdade antes de iniciar]

**Steps:**
1. [Ação do usuário]
2. [Resposta do sistema]
3. [Próxima ação]
...

**Resultado esperado:** [O que acontece ao final do fluxo com sucesso]

**Fluxo de erro:** [O que acontece quando falha e como o usuário é informado]

---

### Fluxo 2 — [Nome do fluxo]

[Repetir estrutura acima]

---

## 7. Regras de Negócio

<!-- Regras que o sistema deve obedecer. Cada regra: condição → resultado. Verificável. -->

| ID | Regra | Condição | Resultado |
|----|-------|----------|-----------|
| RN-001 | [Nome da regra] | [Quando X acontece] | [Então Y deve ocorrer] |
| RN-002 | [Nome da regra] | [Quando X acontece] | [Então Y deve ocorrer] |

---

## 8. Requisitos Funcionais

<!-- Listagem detalhada do que o sistema deve fazer. Agrupado por módulo/funcionalidade. -->

### [Módulo 1]
- RF-001: O sistema deve [ação específica]
- RF-002: O sistema deve [ação específica]

### [Módulo 2]
- RF-003: O sistema deve [ação específica]

---

## 9. Requisitos Não Funcionais

<!-- Performance, segurança, disponibilidade, usabilidade, compatibilidade. -->

| Categoria | Requisito | Critério de Aceite |
|-----------|-----------|-------------------|
| Performance | [Ex: tempo de resposta] | [Ex: < 2s em 95% das requisições] |
| Segurança | [Ex: autenticação] | [Ex: JWT com expiração de 24h] |
| Disponibilidade | [Ex: uptime] | [Ex: 99.5% mensal] |
| Usabilidade | [Ex: acessibilidade] | [Ex: WCAG 2.1 AA] |

---

## 10. Critérios de Aceite

<!-- Por funcionalidade ou fluxo. Cada critério deve ser verificável (sim/não). -->

### [Funcionalidade 1]
- [ ] Dado [contexto], quando [ação], então [resultado esperado]
- [ ] Dado [contexto], quando [ação inválida], então [mensagem de erro específica]

### [Funcionalidade 2]
- [ ] Dado [contexto], quando [ação], então [resultado esperado]

---

## 11. Edge Cases e Cenários de Falha

<!-- Situações não-óbvias que o sistema deve tratar. Derivadas dos fluxos e regras de negócio. -->

| # | Cenário | Condição | Comportamento esperado |
|---|---------|----------|----------------------|
| EC-001 | [Nome] | [O que acontece de incomum] | [Como o sistema deve reagir] |
| EC-002 | [Nome] | [O que acontece de incomum] | [Como o sistema deve reagir] |

---

## 12. Restrições Técnicas e de Negócio

<!-- Limitações que o design e a implementação devem respeitar. -->

### Restrições Técnicas
- [Ex: deve funcionar no browser X]
- [Ex: stack obrigatória: Next.js + Postgres]
- [Ex: integrar com API existente Y]

### Restrições de Negócio
- [Ex: prazo de lançamento]
- [Ex: orçamento de infraestrutura]
- [Ex: requisitos legais/de compliance]

---

## 13. Métricas de Sucesso

<!-- Como medir se o produto funcionou. Quantificável e com baseline quando possível. -->

| Métrica | Baseline atual | Meta | Como medir |
|---------|---------------|------|------------|
| [Métrica 1] | [Valor atual ou N/A] | [Valor alvo] | [Onde/como medir] |
| [Métrica 2] | [Valor atual ou N/A] | [Valor alvo] | [Onde/como medir] |

---

## 14. Premissas

<!-- O que foi assumido sem confirmação explícita. Marcar itens assumidos aqui. -->

- [ASSUMIDO: descrição da premissa 1]
- [ASSUMIDO: descrição da premissa 2]

---

## 15. Riscos

<!-- Riscos identificados e estratégia de mitigação. -->

| Risco | Probabilidade | Impacto | Mitigação |
|-------|--------------|---------|-----------|
| [Risco 1] | Alta/Média/Baixa | Alto/Médio/Baixo | [Como mitigar] |

---

## 16. Dúvidas em Aberto

<!-- Questões não resolvidas que podem impactar o design ou a implementação. -->

| # | Questão | Impacto | Responsável |
|---|---------|---------|-------------|
| 1 | [Dúvida não resolvida] | [O que bloqueia ou pode mudar] | [Quem deve responder] |
