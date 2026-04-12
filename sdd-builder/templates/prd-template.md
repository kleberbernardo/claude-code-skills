# Product Requirements Document
<!-- Gerado por /sdd-builder spec -->
<!-- Data: YYYY-MM-DD -->

---

## 1. Visão Geral do Produto

<!-- O que é o produto em 2–4 frases. Inclui: nome, proposta de valor central, contexto de mercado/uso. Não lista features ainda. -->

**Nome:** [Nome do produto]

**Proposta de valor:** [O que faz, para quem, e qual diferencial]

**Contexto:** [Onde será usado, em que plataforma, por quem]

**Categoria:** [SaaS B2B / SaaS B2C / Marketplace / API / Ferramenta interna / outro]

---

## 2. Problema a Ser Resolvido

<!-- O problema exato que o produto resolve. Ser específico: quem sofre, quando, com que frequência, qual o impacto. Evitar generalidades como "as empresas precisam de eficiência". -->

**Problema principal:** [Articulado em 1–3 frases]

**Causa raiz:** [Por que o problema existe]

**Como é resolvido hoje:** [Solução atual e suas limitações]

**Impacto do problema não resolvido:** [Consequências para o usuário]

---

## 3. Público-Alvo e Personas

<!-- Descrição específica de quem usa. Se houver mais de um tipo de usuário, listar cada um com persona detalhada. -->

### Persona Principal

- **Nome fictício:** [Ex: "Ana, Gestora de Projetos"]
- **Perfil:** [Quem é, o que faz, contexto de vida/trabalho]
- **Necessidade principal:** [O que precisa do produto]
- **Frequência de uso:** [Diário, semanal, ocasional]
- **Nível técnico:** [Iniciante, intermediário, avançado]
- **Ponto de dor atual:** [Como sofre sem o produto]
- **Critério de sucesso:** [O que indica que o produto resolveu o problema dela]

### Outros Tipos de Usuário (se houver)

| Tipo | Descrição | Permissões | Necessidade principal |
|------|-----------|------------|----------------------|
| [Admin] | [Descrição] | [O que pode fazer] | [Por que usa] |
| [Tipo 2] | [Descrição] | [O que pode fazer] | [Por que usa] |

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

## 6. User Stories

<!-- User Stories organizadas por épico. Cobrir todas as funcionalidades do escopo funcional.
     Formato: Como [perfil de usuário], quero [ação/funcionalidade] para [valor/benefício].
     Critérios de aceite seguem no formato Dado/Quando/Então. -->

### Épico: [Nome do Épico 1 — ex: Autenticação]

**US-001 — [Título curto]**
> Como [tipo de usuário], quero [ação] para [benefício].

Critérios de aceite:
- Dado [contexto], quando [ação], então [resultado esperado]
- Dado [contexto inválido], quando [ação], então [mensagem de erro específica]

---

**US-002 — [Título curto]**
> Como [tipo de usuário], quero [ação] para [benefício].

Critérios de aceite:
- Dado [contexto], quando [ação], então [resultado esperado]

---

### Épico: [Nome do Épico 2 — ex: Onboarding]

**US-003 — [Título curto]**
> Como [tipo de usuário], quero [ação] para [benefício].

Critérios de aceite:
- Dado [contexto], quando [ação], então [resultado esperado]

---

### Épico: [Nome do Épico 3 — ex: Funcionalidade Principal]

**US-004 — [Título curto]**
> Como [tipo de usuário], quero [ação] para [benefício].

Critérios de aceite:
- Dado [contexto], quando [ação], então [resultado esperado]

---

## 7. SaaS Foundation

<!-- Funcionalidades padrão de SaaS que fazem parte do produto. Listar com as variações confirmadas. -->

### Autenticação e Acesso

| Feature | Especificação | Notas |
|---------|--------------|-------|
| Cadastro | [E-mail + senha / Google / convite / outro] | [Confirmação de e-mail: sim/não] |
| Login | [E-mail + senha / Google / GitHub / outro] | [Sessão: duração confirmada] |
| Recuperação de senha | [Via e-mail com link temporário] | [Expiração do link] |
| 2FA | [Sim/Não — método se sim] | |
| SSO | [Sim/Não — provider se sim] | |

### Onboarding

| Etapa | Descrição | Obrigatório? |
|-------|-----------|-------------|
| Tela de boas-vindas | [O que aparece primeiro] | Sim/Não |
| Setup inicial | [Configuração obrigatória antes de usar] | Sim/Não |
| Tour ou tutorial | [Guiado / tooltip / vídeo / nenhum] | Sim/Não |
| Estado vazio | [O que aparece quando não há dados] | Sim |

### Perfil e Conta

| Feature | Especificação |
|---------|--------------|
| Editar perfil | [Campos editáveis: nome, foto, etc.] |
| Alterar senha | [Dentro do app logado] |
| Deletar conta | [Sim/Não — com confirmação e aviso de dados] |
| Notificações de conta | [E-mails transacionais obrigatórios] |

---

## 8. Fluxos Principais

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

## 9. Regras de Negócio

<!-- Regras que o sistema deve obedecer. Cada regra: condição → resultado. Verificável. -->

| ID | Regra | Condição | Resultado |
|----|-------|----------|-----------|
| RN-001 | [Nome da regra] | [Quando X acontece] | [Então Y deve ocorrer] |
| RN-002 | [Nome da regra] | [Quando X acontece] | [Então Y deve ocorrer] |

---

## 10. Requisitos Funcionais

<!-- Listagem detalhada do que o sistema deve fazer. Agrupado por módulo/funcionalidade. -->

### [Módulo 1 — ex: Autenticação]
- RF-001: O sistema deve [ação específica]
- RF-002: O sistema deve [ação específica]

### [Módulo 2 — ex: Funcionalidade Principal]
- RF-003: O sistema deve [ação específica]

---

## 11. Requisitos Não Funcionais

<!-- Performance, segurança, disponibilidade, usabilidade, compatibilidade. -->

| Categoria | Requisito | Critério de Aceite |
|-----------|-----------|-------------------|
| Performance | [Ex: tempo de resposta] | [Ex: < 2s em 95% das requisições] |
| Segurança | [Ex: autenticação] | [Ex: JWT com expiração de 24h] |
| Disponibilidade | [Ex: uptime] | [Ex: 99.5% mensal] |
| Usabilidade | [Ex: acessibilidade] | [Ex: WCAG 2.1 AA] |
| Compatibilidade | [Ex: browsers] | [Ex: Chrome, Firefox, Safari — últimas 2 versões] |
| Mobile | [Responsivo ou app nativo] | [Ex: funcional em telas ≥ 320px] |

---

## 12. Critérios de Aceite

<!-- Por User Story ou funcionalidade. Cada critério deve ser verificável (sim/não). -->

### [Funcionalidade 1]
- [ ] Dado [contexto], quando [ação], então [resultado esperado]
- [ ] Dado [contexto], quando [ação inválida], então [mensagem de erro específica]

### [Funcionalidade 2]
- [ ] Dado [contexto], quando [ação], então [resultado esperado]

---

## 13. Edge Cases e Cenários de Falha

<!-- Situações não-óbvias que o sistema deve tratar. Derivadas dos fluxos e regras de negócio. -->

| # | Cenário | Condição | Comportamento esperado |
|---|---------|----------|----------------------|
| EC-001 | [Nome] | [O que acontece de incomum] | [Como o sistema deve reagir] |
| EC-002 | [Nome] | [O que acontece de incomum] | [Como o sistema deve reagir] |

---

## 14. Monetização e Modelo de Negócio

<!-- Modelo de receita, planos, trial, e o que muda entre planos. -->

**Modelo:** [Assinatura mensal / por uso / freemium / compra única / gratuito]

### Planos (se houver)

| Plano | Preço | Features incluídas | Limite |
|-------|-------|--------------------|--------|
| [Free/Starter] | [R$ 0] | [Lista de features] | [Limites] |
| [Pro] | [R$ X/mês] | [Lista de features] | [Limites] |
| [Enterprise] | [Custom] | [Lista de features] | [Sem limites] |

**Trial:** [Sim / Não — duração se sim]

**O que acontece após expiração do trial:** [Bloqueio total / downgrade para free / notificação]

---

## 15. Restrições Técnicas e de Negócio

<!-- Limitações que o design e a implementação devem respeitar. -->

### Restrições Técnicas
- [Ex: deve funcionar no browser X]
- [Ex: stack obrigatória: Next.js + Postgres]
- [Ex: integrar com API existente Y]

### Restrições de Negócio
- [Ex: prazo de lançamento]
- [Ex: orçamento de infraestrutura]
- [Ex: requisitos legais/de compliance — LGPD, GDPR]

---

## 16. Métricas de Sucesso

<!-- Como medir se o produto funcionou. Quantificável e com baseline quando possível. -->

| Métrica | Baseline atual | Meta | Como medir |
|---------|---------------|------|------------|
| [Métrica 1] | [Valor atual ou N/A] | [Valor alvo] | [Onde/como medir] |
| [Métrica 2] | [Valor atual ou N/A] | [Valor alvo] | [Onde/como medir] |

---

## 17. Premissas

<!-- O que foi assumido sem confirmação explícita. Marcar itens assumidos aqui. -->

- [ASSUMIDO: descrição da premissa 1]
- [ASSUMIDO: descrição da premissa 2]

---

## 18. Riscos

<!-- Riscos identificados e estratégia de mitigação. -->

| Risco | Probabilidade | Impacto | Mitigação |
|-------|--------------|---------|-----------|
| [Risco 1] | Alta/Média/Baixa | Alto/Médio/Baixo | [Como mitigar] |

---

## 19. Dúvidas em Aberto

<!-- Questões não resolvidas que podem impactar o design ou a implementação. -->

| # | Questão | Impacto | Responsável |
|---|---------|---------|-------------|
| 1 | [Dúvida não resolvida] | [O que bloqueia ou pode mudar] | [Quem deve responder] |
