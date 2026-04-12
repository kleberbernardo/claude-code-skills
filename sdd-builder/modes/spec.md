# Modo SPEC — SDD Builder

Conduzir entrevista interativa e gerar PRD completo.

---

## Objetivo

Transformar uma ideia (vaga ou detalhada) em um PRD preciso, sem ambiguidade, pronto para virar design técnico.

---

## Fluxo de Execução

```
1. Receber a ideia inicial (ou pedir que descreva)
2. Anunciar o SaaS Foundation (funcionalidades padrão que já entram)
3. Avaliar o nível de completude do contexto
4. Conduzir entrevista (seguir core/interview-rules.md)
5. Cobrir TODOS os tópicos obrigatórios das 4 fases
6. Anunciar geração do PRD
7. Gerar o PRD usando templates/prd-template.md
8. Salvar em .ai/product/prd.md
9. Exibir confirmação e próximo passo
```

---

## Passo 1: Recepção da Ideia

**Se o usuário não descreveu nada além do comando:**
```
Vamos começar. Me conta a ideia — pode ser uma frase curta ou um parágrafo.
```

**Se o usuário descreveu a ideia junto ao comando:**
Agradecer brevemente, fazer o anúncio SaaS Foundation e partir para a primeira pergunta.

---

## Passo 2: Anúncio SaaS Foundation

**Antes da primeira pergunta de descoberta**, informar (adaptar ao contexto do produto):

```
Antes de mergulhar: funcionalidades padrão de SaaS vão entrar 
automaticamente no PRD — cadastro, login, recuperação de senha, 
confirmação de e-mail e onboarding inicial. Vou perguntar sobre 
variações delas, mas você não precisa me lembrar que "precisa ter login".

Agora me conta: [primeira pergunta de descoberta]
```

Se o produto claramente não for SaaS (ex: API, biblioteca, CLI), omitir este anúncio.

---

## Passo 3: Avaliação de Completude

Antes de perguntar, avaliar o que já está presente na ideia inicial:

| Elemento | Presente? | Ação se ausente |
|----------|-----------|-----------------|
| O que é o produto | Sim/Não | Primeira pergunta |
| Problema que resolve | Sim/Não | Segunda pergunta prioritária |
| Público-alvo | Sim/Não | Terceira prioridade |
| Fluxos principais | Sim/Não | Cobrir na entrevista |
| Funcionalidades | Sim/Não | Explorar e priorizar |
| Onboarding | Sim/Não | Cobrir na Fase 2 |
| Autenticação | Sim/Não | Cobrir na Fase 3 |
| Monetização | Sim/Não | Cobrir na Fase 4 |
| Restrições técnicas | Sim/Não | Perguntar no final |

---

## Passo 4: Entrevista

Seguir `core/interview-rules.md` rigorosamente.

> **Sobre a Fase 5 (UX Visual):** As perguntas visuais são coletadas para gerar um artefato separado (`ux.md`), não o PRD. Avisar o usuário antes de iniciar essa fase:
> ```
> Agora vou fazer algumas perguntas sobre a experiência visual do produto.
> Essas respostas vão para um arquivo separado (ux.md) — não misturo
> intenção visual com requisitos de produto.
> ```
> Se o usuário não quiser responder ("não sei", "pode escolher você"), encerrar a Fase 5 e não gerar `ux.md`.

### Fase 1 — Fundação do Produto

**Bloco 1 — Produto**
- Qual o nome do produto? Como ele se descreve em uma frase?
- Em qual plataforma vai rodar — web, mobile, API?
- Tem algum produto existente que se parece com o que quer construir?

**Bloco 2 — Problema e Público**
- Qual problema exato está sendo resolvido?
- Quem exatamente sofre com esse problema? (específico, não genérico)
- Como essa pessoa resolve hoje? Por que é insatisfatório?
- O que muda na vida dessa pessoa com o produto?

### Fase 2 — Produto em Detalhe

**Bloco 3 — Funcionalidades**
- O que o produto faz na sua forma mais essencial?
- Quais são as 3–5 funcionalidades sem as quais o produto não existe?
- O que é importante mas pode ficar para depois?
- O que definitivamente não faz parte deste produto?

**Bloco 4 — Fluxos Principais**
- Me conta o passo a passo do fluxo principal (como o usuário usaria da primeira vez)
- Que ação central o usuário vai repetir com mais frequência?
- O que acontece quando o usuário comete um erro nesse fluxo?

**Bloco 5 — Onboarding e Primeiro Valor**
- O que o usuário vê imediatamente após criar a conta?
- Tem algum setup obrigatório antes de poder usar o produto?
- Qual é o "primeiro valor" — o que o usuário precisa conseguir fazer para entender que o produto vale?
- Como é o estado vazio quando o usuário ainda não fez nada?

**Bloco 6 — Tipos de Usuário**
- Existe mais de um tipo de usuário com permissões diferentes?
- Existe o conceito de organização, empresa ou workspace?
- Quem administra o sistema — é o mesmo que usa no dia a dia?

### Fase 3 — Regras e Restrições

**Bloco 7 — Regras de Negócio**
- Existe alguma regra de negócio específica que o produto deve obedecer?
- O que acontece em condições de limite — uso expirado, plano atingido, dados insuficientes?
- Tem alguma ação irreversível que precisa de confirmação?

**Bloco 8 — Autenticação e Acesso**
- Como o usuário vai fazer login — e-mail e senha, login social (Google, GitHub), ou outro método?
- O cadastro é aberto ou por convite?
- Tem algum requisito de segurança específico — 2FA, SSO, política de senha?

**Bloco 9 — Notificações e Comunicação**
- O sistema precisa avisar o usuário de alguma coisa — por e-mail, push ou dentro do app?
- Quais eventos disparam notificação?
- Vai ter e-mail transacional além dos padrões (confirmação de cadastro, redefinição de senha)?

**Bloco 10 — Fora de Escopo**
- O que você definitivamente NÃO quer nessa versão?
- Quais features são para o futuro?

### Fase 4 — Qualidade e Sucesso

**Bloco 11 — Critérios de Aceite**
- Como você saberá que o produto está funcionando?
- Qual é o critério mínimo de aceite para a primeira versão estar pronta?

**Bloco 12 — Monetização**
- O produto é pago? Se sim, como — assinatura, por uso, compra única?
- Vai ter planos diferentes? O que muda entre eles?
- Tem trial ou freemium?

**Bloco 13 — Métricas de Sucesso**
- Qual número muda quando o produto estiver entregando valor?
- Em 3 meses, qual seria um resultado bom?

**Bloco 14 — Restrições Técnicas**
- Existe stack tecnológica definida ou restrição de plataforma?
- Há integrações com sistemas externos obrigatórias?
- Existe prazo de lançamento que impacta o escopo?

### Fase 5 — UX Visual (gera `ux.md` separado)

> Avisar o usuário antes de iniciar esta fase conforme instrução no topo do Passo 4.

**Bloco 15 — Personalidade e Referências**
- Tem algum produto ou app que você admira visualmente como referência?
- Como você imagina o tom visual — minimalista, bold, corporativo, descontraído?
- Tem algo que você definitivamente NÃO quer visualmente?

**Bloco 16 — Cores e Tema**
- Preferência de tema — claro, escuro, ou com alternância?
- Tem cores da marca definidas ou alguma direção de paleta?

**Bloco 17 — Layout e Densidade**
- O produto é denso (muita informação por tela) ou focado (uma ação por vez)?
- Qual é a tela mais importante do produto — o que tem nela e o que o usuário precisa sentir?

**Bloco 18 — Componentes Específicos**
- Vai ter gráficos, tabelas de dados, ou o produto é mais orientado a formulários e fluxos?
- Algum componente específico que você imagina — kanban, editor de texto, upload de arquivos?

**Bloco 19 — Plataforma e Responsividade**
- Precisa funcionar bem em mobile ou o foco é desktop?
- Tem algum contexto de uso que afeta a interface — usado com pressa, em movimento, em escritório?

---

## Passo 5: Transição para Geração

Quando todos os tópicos obrigatórios tiverem sido cobertos (ou o usuário pedir encerramento):

```
Entendido. Tenho o que preciso para gerar o PRD.
Vou estruturar agora com base em tudo que você me contou.
```

Não perguntar se pode gerar. Apenas gerar.

---

## Passo 6: Geração dos Artefatos

### 6a — Gerar o PRD

Usar `templates/prd-template.md` como estrutura base.

**Regras de geração:**
- Ser específico: usar linguagem do usuário, não genérica
- Não inventar funcionalidades não confirmadas
- Incluir a seção SaaS Foundation com as features padrão do produto
- Gerar User Stories para TODAS as funcionalidades confirmadas
- Marcar premissas com `[ASSUMIDO: ...]`
- Registrar incertezas em "Dúvidas em Aberto"
- Critérios de aceite devem ser verificáveis (sim/não testável)
- Edge cases devem ser realistas, não hipotéticos abstratos

### 6b — Gerar o UX Visual (se Fase 5 foi respondida)

Se o usuário respondeu ao menos algumas perguntas da Fase 5:
- Usar `templates/ux-template.md` como estrutura base
- Preencher apenas os campos que foram efetivamente respondidos
- Campos não respondidos: deixar como `[não definido — decisão de design]`
- **Não misturar** conteúdo do PRD no `ux.md` e vice-versa

Se o usuário não quis responder a Fase 5: não gerar `ux.md`.

---

## Passo 7: Salvar e Confirmar

1. Criar pasta `.ai/product/` se não existir
2. Salvar o PRD em `.ai/product/prd.md`
3. Se `ux.md` foi gerado: salvar em `.ai/product/ux.md`
4. Exibir confirmação:

**Se `ux.md` gerado:**
```
✅ PRD salvo em .ai/product/prd.md
✅ UX Visual salvo em .ai/product/ux.md

Próximo passo: /sdd-builder design @.ai/product/prd.md @.ai/product/ux.md
```

**Se apenas PRD:**
```
✅ PRD salvo em .ai/product/prd.md

Próximo passo: /sdd-builder design @.ai/product/prd.md
```

---

## Checklist de Qualidade do PRD

Antes de salvar, verificar:

- [ ] Seção 1: produto descrito em 1–3 frases claras com nome e proposta de valor
- [ ] Seção 2: problema articulado sem ambiguidade, com causa raiz e impacto
- [ ] Seção 3: público específico com perfil, frequência de uso e nível técnico
- [ ] Seção 4: objetivos primários mensuráveis
- [ ] Seção 5: escopo funcional lista apenas o confirmado, com prioridades
- [ ] Seção 5: "Fora de Escopo" com pelo menos 3 itens explícitos
- [ ] Seção 6: User Stories escritas para TODAS as funcionalidades (formato: Como [perfil], quero [ação] para [valor])
- [ ] Seção 7: SaaS Foundation listada com variações confirmadas
- [ ] Seção 8: pelo menos 2 fluxos principais descritos em steps com happy path e erro
- [ ] Seção 9: regras de negócio são verificáveis (condição → resultado)
- [ ] Seção 11: critérios de aceite são testáveis (dado/quando/então)
- [ ] Seção 12: edge cases incluem falhas reais, não apenas happy path
- [ ] Seção 15: premissas registradas explicitamente
- [ ] Seção 17: dúvidas em aberto registradas
