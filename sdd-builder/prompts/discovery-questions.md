# Discovery Questions — SDD Builder

Banco de perguntas por categoria para uso no modo `spec`.

**Regra de uso:** Escolher UMA pergunta por turno. Nunca disparar várias de uma vez.

---

## Categoria: Produto e Ideia

Quando não está claro o que o produto é:
- "Me conta a ideia em uma frase — o que o produto faz?"
- "Qual é o nome do produto, ou como você costuma se referir a ele?"
- "Em qual plataforma vai rodar — web, mobile, desktop, API?"
- "Se você tivesse que explicar para alguém em 10 segundos, o que diria?"
- "Tem algum produto existente que se parece com o que você quer construir?"

---

## Categoria: Problema

Quando o problema não está claro:
- "Qual é a dor exata que ele resolve?"
- "Quando isso vira um problema real para alguém?"
- "Com que frequência esse problema acontece?"
- "Qual é o custo de não resolver — em tempo, dinheiro, frustração?"

Quando a resposta é genérica:
- "Me dá um exemplo concreto de alguém enfrentando esse problema."
- "O que essa pessoa faz hoje para contornar isso?"
- "Por que a solução atual não é boa o suficiente?"
- "Qual é o momento exato em que ela percebe que tem esse problema?"

---

## Categoria: Público-Alvo

Quando o público é vago ("empresas", "qualquer pessoa"):
- "Quem seria o primeiro usuário pagante — descreve essa pessoa."
- "Qual é o cargo ou função dessa pessoa?"
- "Essa pessoa toma decisões sozinha ou precisa de aprovação?"
- "Ela usa esse produto no trabalho ou na vida pessoal?"
- "Qual é o nível técnico dela — consegue usar Excel sozinha?"

Quando há múltiplos tipos de usuário:
- "Existe mais de um tipo de usuário com acessos diferentes?"
- "Quem administra o sistema — é o mesmo que usa no dia a dia?"
- "O admin vê tudo? O usuário comum vê o quê?"
- "Existe um perfil de 'super admin' ou dono de conta?"

---

## Categoria: Fluxos Principais

Quando os fluxos não estão descritos:
- "Me conta o passo a passo do fluxo principal — do começo ao fim."
- "O que o usuário faz primeiro quando abre o produto?"
- "Qual é a ação mais importante que o usuário realiza?"
- "O que o usuário vê na tela principal depois de logar?"

Quando o fluxo está superficial:
- "O que acontece depois de [ação X]? E depois?"
- "O que acontece se o usuário errar nesse step?"
- "Quem vê o resultado dessa ação — só o usuário ou outras pessoas?"
- "Existe alguma confirmação ou aprovação nesse caminho?"
- "O que o sistema faz em segundo plano enquanto o usuário espera?"

---

## Categoria: Funcionalidades

Quando a lista de features é vaga ou muito longa:
- "Se você tivesse que lançar em 2 semanas, qual funcionalidade não pode faltar?"
- "O que é essencial para o primeiro usuário conseguir valor?"
- "O que você cortaria sem pena se precisasse?"
- "Quais features são diferenciais do produto e quais são commodity?"

Quando uma funcionalidade está mal definida:
- "Como funciona [feature X] exatamente — me descreve passo a passo."
- "[Feature X] é para todos os usuários ou só para alguns?"
- "O usuário controla isso manualmente ou é automático?"
- "O que o usuário vê antes, durante e depois de usar [feature X]?"
- "Tem algum limite nessa funcionalidade — de uso, de quantidade, de tempo?"

---

## Categoria: Autenticação e Acesso

Quando o método de autenticação não foi definido:
- "Como o usuário vai fazer login — e-mail e senha, Google, ou outro método?"
- "Vai ter login social (Google, GitHub, Apple)? Ou só e-mail e senha?"
- "O cadastro é aberto para qualquer pessoa ou precisa de convite/aprovação?"

Quando há dúvidas sobre sessão e segurança:
- "Quanto tempo a sessão do usuário fica ativa sem precisar logar de novo?"
- "Se o usuário tentar logar de um device novo, o sistema faz alguma verificação?"
- "Tem algum requisito de segurança específico — 2FA, SSO corporativo?"
- "O que acontece se o usuário tentar acessar uma área sem permissão?"

---

## Categoria: Onboarding

Quando o fluxo de primeiro acesso não foi descrito:
- "O que acontece logo depois que o usuário cria a conta — o que ele vê primeiro?"
- "Tem algum passo obrigatório antes de poder usar o produto (setup, configuração inicial)?"
- "O produto vai guiar o usuário em algum tutorial ou ele cai direto na interface?"
- "Qual é o 'primeiro valor' — a coisa que o usuário precisa conseguir fazer para perceber que vale a pena?"

Quando o onboarding está vago:
- "O usuário precisa importar dados ou configurar algo para o produto funcionar?"
- "Como você imagina o estado vazio — quando o usuário ainda não fez nada?"
- "Existe alguma notificação ou e-mail de boas-vindas após o cadastro?"
- "O onboarding é igual para todos os tipos de usuário ou varia?"

---

## Categoria: Regras de Negócio

Quando as regras não estão explícitas:
- "Tem alguma restrição de negócio importante que o sistema deve obedecer?"
- "Quem pode criar, editar ou deletar [entidade X]?"
- "Existe alguma aprovação ou validação necessária antes de [ação Y]?"
- "O que acontece quando [condição de limite] — ex: limite de uso, prazo expirado?"
- "Tem alguma regra que muda dependendo do plano ou perfil do usuário?"

---

## Categoria: Notificações e Comunicação

Quando o sistema de notificações não foi mencionado:
- "O sistema precisa avisar o usuário de alguma coisa — por e-mail, push, ou dentro do app?"
- "Quais eventos geram notificação? O usuário pode controlar isso?"
- "Vai ter e-mail transacional — confirmação de cadastro, redefinição de senha, recibo?"
- "Tem algum tipo de notificação em tempo real dentro do app (toast, badge, alert)?"

Quando notificações estão vagas:
- "Essa notificação é para o usuário que gerou a ação ou para outros usuários também?"
- "O usuário pode desligar alguma notificação ou todas são obrigatórias?"

---

## Categoria: Monetização e Modelo de Negócio

Quando o modelo de receita não está claro:
- "O produto é pago? Se sim, como funciona — assinatura mensal, por uso, compra única?"
- "Vai ter planos diferentes com features distintas? Me conta como você imagina isso."
- "Tem período trial ou freemium? Quanto tempo dura?"
- "O pagamento é feito por usuário, por empresa, ou por consumo?"

Quando há planos ou tiers:
- "O que muda entre um plano e outro — features bloqueadas, limites de uso?"
- "O que acontece quando o trial expira — o acesso é bloqueado ou só limitado?"
- "Quem controla o pagamento — o admin da conta ou cada usuário individual?"

---

## Categoria: UX e Experiência Funcional

Quando aspectos de experiência funcional (não visual) não foram mencionados:
- "O produto precisa funcionar em mobile também ou só em desktop por enquanto?"
- "Qual é o estado mais importante que o usuário vai passar mais tempo — me descreve o que tem nessa tela."
- "Quando o produto está em segundo plano e o usuário volta, o que ele vê?"
- "Tem alguma ação que precisa de confirmação antes de executar — algo irreversível?"

---

## Categoria: UX Visual (gera `ux.md` separado)

> Usar apenas na Fase 5 — após o PRD estar completo. Avisar o usuário que essas respostas vão para um arquivo separado.

**Personalidade e referências:**
- "Tem algum produto ou app que você admira visualmente como referência?"
- "Como você imagina o tom visual — minimalista, bold, corporativo, descontraído?"
- "Tem algo que você definitivamente NÃO quer visualmente no produto?"
- "Se o produto fosse uma pessoa, que tipo de pessoa seria — formal, descolada, técnica?"

**Cores e tema:**
- "Preferência de tema — claro, escuro, ou com alternância pelo usuário?"
- "Tem cores da marca já definidas? Pode me passar o hex ou descrever a direção?"
- "Tem alguma cor que você associa ao produto ou que gostaria de evitar?"

**Layout e densidade:**
- "O produto é denso — muita informação por tela — ou focado — uma ação principal por vez?"
- "Qual é a tela mais importante do produto e o que o usuário precisa sentir ao vê-la?"
- "O produto vai ter uma sidebar de navegação, tabs, ou outra estrutura de navegação?"

**Componentes específicos:**
- "Vai ter gráficos ou dashboards com métricas?"
- "Vai ter tabelas com muitos dados para filtrar e ordenar?"
- "Algum componente incomum — editor de texto rico, kanban, calendário, upload com preview?"

**Plataforma e contexto:**
- "O foco é desktop, mobile, ou precisa funcionar bem nos dois?"
- "Em que contexto o produto vai ser usado — escritório com tempo, em movimento com pressa, outro?"
- "Tem algum requisito de acessibilidade — daltonismo, leitores de tela, navegação por teclado?"

---

## Categoria: Dados e Privacidade

Quando dados sensíveis ou privacidade não foram mencionados:
- "O produto lida com dados pessoais sensíveis — saúde, finanças, menores de idade?"
- "O usuário pode exportar ou deletar os dados dele?"
- "Existe algum requisito de conformidade — LGPD, GDPR, SOC2?"
- "Os dados de um usuário são visíveis para outros usuários ou são privados?"

---

## Categoria: Multi-tenancy e Workspaces

Quando não está claro se há isolamento de dados:
- "Vários usuários de uma mesma empresa usam juntos ou cada um tem sua conta isolada?"
- "Existe o conceito de 'organização', 'workspace' ou 'empresa' no produto?"
- "Um usuário pode pertencer a mais de uma organização?"
- "Os dados de uma organização podem ser vistos por outra?"

---

## Categoria: Fora de Escopo

Quando não está claro o que fica de fora:
- "O que você definitivamente NÃO quer nessa versão?"
- "[Feature típica de produtos similares] — está dentro ou fora?"
- "Tem alguma funcionalidade que você pensou mas decidiu deixar para depois?"

---

## Categoria: Critérios de Aceite

Quando não está claro o que é "pronto":
- "Como você saberá que essa feature está funcionando corretamente?"
- "O que um usuário real conseguiria fazer que hoje não consegue?"
- "Tem algum estado de erro que obrigatoriamente precisa de mensagem específica?"

---

## Categoria: Métricas de Sucesso

Quando o sucesso não foi definido:
- "Como você vai medir se o produto funcionou?"
- "Qual número muda quando o produto estiver entregando valor?"
- "Em 3 meses, qual seria um resultado bom?"
- "O que faria você dizer 'esse produto foi um fracasso'?"

---

## Categoria: Restrições Técnicas

Quando a stack não foi mencionada:
- "Tem alguma tecnologia obrigatória ou proibida?"
- "Isso vai ser integrado com algum sistema que já existe?"
- "Tem alguma limitação de infraestrutura ou orçamento que devo saber?"
- "Existe algum prazo de lançamento que impacta o escopo?"

---

## Perguntas de Aprofundamento Universal

Para quando qualquer resposta for vaga:
- "Me conta mais sobre isso."
- "Me dá um exemplo concreto."
- "O que você quis dizer com [termo vago]?"
- "Entendido que [resumo]. Mas quando [cenário específico], o que acontece?"
- "Essa é sempre a regra, ou tem exceções?"
- "Você mencionou [X] — isso é obrigatório para o MVP ou pode esperar?"
- "Se isso não existisse no produto, o usuário conseguiria o valor mesmo assim?"
- "Quem toma essa decisão — o usuário ou o sistema decide automaticamente?"
