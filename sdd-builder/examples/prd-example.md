# Exemplo de PRD — SDD Builder

Este exemplo ilustra o nível de detalhe e especificidade esperado no PRD.

**Produto fictício:** Plataforma de gerenciamento de links compartilhados para times.

---

# Product Requirements Document

## 1. Visão Geral do Produto

**Nome:** LinkHub

**Proposta de valor:** Plataforma web para times salvarem, organizarem e acessarem links relevantes ao projeto, substituindo a prática de compartilhar links via Slack que se perdem no histórico.

**Contexto:** Usado no browser, por times de produto e engenharia, durante o trabalho diário em projetos de software.

---

## 2. Problema a Ser Resolvido

**Problema principal:** Links compartilhados no Slack desaparecem no histórico e não são recuperáveis sem busca. Times perdem referências importantes de documentação, designs e tickets.

**Causa raiz:** Ferramentas de chat não são indexadas para recuperação de conteúdo. Busca por links no Slack requer lembrar palavras-chave exatas.

**Como é resolvido hoje:** Enviar links no Slack e torcer para lembrar quando precisar. Alguns usam bookmarks pessoais no browser, mas não são compartilhados com o time.

**Impacto:** Engenheiros perdem 15–30 min/semana buscando links já compartilhados.

---

## 3. Público-Alvo e Usuários

### Usuário Principal

- **Perfil:** Engenheiro de software em time de produto, 5–50 pessoas
- **Necessidade:** Salvar e recuperar links relevantes ao projeto sem depender do histórico do Slack
- **Frequência de uso:** Diário — ao menos 3–5 links salvos ou consultados por dia
- **Nível técnico:** Intermediário a avançado

### Outros Tipos de Usuário

| Tipo | Descrição | Permissões | Necessidade |
|------|-----------|------------|-------------|
| Admin do workspace | Cria e gerencia o workspace, convida membros | Tudo + gerenciar membros | Onboarding de novos membros |
| Membro | Usuário padrão | Criar, editar e deletar próprios links; ver todos do workspace | Uso diário |

---

## 4. Objetivos do Produto

### Objetivos Primários
1. Reduzir em 80% o tempo gasto buscando links já compartilhados pelo time
2. Atingir 70% de adoção diária em times que adotarem o produto

### Objetivos Secundários
1. Ser percebido como mais rápido de usar do que abrir o Slack para buscar

---

## 5. Escopo Funcional

### Incluído nesta versão

| # | Funcionalidade | Prioridade | Motivação |
|---|---------------|------------|-----------|
| 1 | Salvar link com título e tags | Must-have | Funcionalidade central |
| 2 | Listar links do workspace com busca | Must-have | Recuperação é o valor central |
| 3 | Organizar links em coleções | Must-have | Organização por projeto/tema |
| 4 | Autenticação e workspaces isolados | Must-have | Segurança e multi-tenant |
| 5 | Editar e deletar próprios links | Should-have | Manutenção básica |
| 6 | Preview automático do título da página | Nice-to-have | Reduz atrito no salvamento |

### Fora de Escopo

- Integração com Slack (v2)
- Extensão de browser (v2)
- Favoritos pessoais separados do workspace
- Compartilhamento externo (fora do workspace)
- Notificações e @menções

---

## 6. Fluxos Principais

### Fluxo 1 — Salvar um link

**Ator:** Membro
**Pré-condição:** Usuário autenticado e pertence a um workspace

**Steps:**
1. Usuário clica em "Novo link"
2. Sistema abre modal com campo de URL, título e tags
3. Usuário cola a URL
4. Sistema busca o título da página automaticamente e preenche o campo título
5. Usuário pode editar o título e adicionar tags (opcional)
6. Usuário clica em "Salvar"
7. Sistema valida que URL é válida e título não está vazio
8. Sistema persiste o link associado ao workspace e ao usuário
9. Link aparece no topo da lista

**Resultado esperado:** Link salvo e visível para todos os membros do workspace

**Fluxo de erro (URL inválida):**
- No step 7: sistema exibe mensagem "URL inválida" abaixo do campo
- Modal permanece aberto com os dados preenchidos
- Usuário corrige e pode tentar novamente

---

### Fluxo 2 — Buscar e acessar um link

**Ator:** Membro
**Pré-condição:** Workspace tem ao menos um link salvo

**Steps:**
1. Usuário digita no campo de busca
2. Sistema filtra em tempo real por título, URL e tags
3. Resultados atualizam enquanto usuário digita (debounce 300ms)
4. Usuário clica no link desejado
5. Link abre em nova aba

**Resultado esperado:** Usuário acessa o link correto sem sair da plataforma

---

## 7. Regras de Negócio

| ID | Regra | Condição | Resultado |
|----|-------|----------|-----------|
| RN-001 | Isolamento de workspace | Quando membro acessa links | Vê apenas links do próprio workspace |
| RN-002 | Propriedade de link | Quando membro tenta editar/deletar link | Só pode editar/deletar links que criou |
| RN-003 | Admin override | Quando admin tenta deletar qualquer link | Pode deletar links de qualquer membro |
| RN-004 | URL obrigatória e única | Quando membro salva link | URL não pode ser vazia nem duplicada no workspace |
| RN-005 | Título obrigatório | Quando membro salva link | Título não pode ser vazio (auto-preenchido ou manual) |

---

## 8. Requisitos Funcionais

### Autenticação
- RF-001: O sistema deve permitir registro com email e senha
- RF-002: O sistema deve permitir login com email e senha
- RF-003: O sistema deve invalidar sessão após 7 dias de inatividade

### Workspaces
- RF-004: O sistema deve criar workspace automaticamente no primeiro login
- RF-005: O sistema deve permitir que admin convide membros por email

### Links
- RF-006: O sistema deve buscar automaticamente o título de uma URL ao colar no campo
- RF-007: O sistema deve validar que URL começa com http:// ou https://
- RF-008: O sistema deve impedir URLs duplicadas dentro do mesmo workspace
- RF-009: O sistema deve listar links em ordem de criação decrescente (mais recente primeiro)
- RF-010: O sistema deve filtrar links em tempo real por título, URL e tags

### Coleções
- RF-011: O sistema deve permitir criar coleções com nome
- RF-012: O sistema deve permitir adicionar links a uma ou mais coleções

---

## 9. Requisitos Não Funcionais

| Categoria | Requisito | Critério de Aceite |
|-----------|-----------|-------------------|
| Performance | Busca em tempo real | Resultados em < 300ms para workspaces com até 10.000 links |
| Performance | Carregamento inicial | Página carrega em < 2s em conexão 4G |
| Segurança | Autenticação | JWT com expiração de 7 dias |
| Segurança | Isolamento | Nenhuma query retorna dados de outro workspace |
| Disponibilidade | Uptime | 99% mensal |

---

## 10. Critérios de Aceite

### Salvar Link
- [ ] Dado que usuário está autenticado, quando cola URL válida e clica salvar, então link aparece na lista do workspace
- [ ] Dado que usuário cola URL, quando sistema busca título, então campo título é preenchido automaticamente em < 3s
- [ ] Dado que usuário tenta salvar URL duplicada no workspace, então sistema exibe "Este link já foi salvo neste workspace"
- [ ] Dado que usuário tenta salvar sem título, então sistema exibe "Título é obrigatório"

### Busca
- [ ] Dado que workspace tem 100 links, quando usuário digita 3 caracteres, então resultados filtrados aparecem em < 300ms
- [ ] Dado que usuário busca por tag, quando tag existe, então todos os links com aquela tag aparecem

---

## 11. Edge Cases e Cenários de Falha

| # | Cenário | Condição | Comportamento esperado |
|---|---------|----------|----------------------|
| EC-001 | URL inacessível | Auto-fetch do título falha (timeout ou 404) | Campo título fica vazio e editável; usuário digita manualmente |
| EC-002 | Link deletado enquanto alguém visualiza | Membro abre link, admin deleta, membro clica | Link abre normalmente (URL é external); apenas some da lista na próxima atualização |
| EC-003 | Workspace sem membros | Admin deleta todos os membros | Admin permanece; workspace não é deletado |
| EC-004 | Dois usuários salvam mesma URL simultâneo | Race condition no check de duplicata | Constraint no banco garante unicidade; segundo recebe erro de duplicata |

---

## 12. Restrições Técnicas e de Negócio

### Restrições Técnicas
- Stack: Next.js 14 (App Router) + PostgreSQL + Drizzle ORM
- Deploy: Vercel (frontend) + Railway (banco)
- Sem dependência de serviços pagos além da infra

### Restrições de Negócio
- MVP em 3 semanas com time de 1 engenheiro

---

## 13. Métricas de Sucesso

| Métrica | Baseline atual | Meta | Como medir |
|---------|---------------|------|------------|
| Links salvos por usuário/semana | 0 (produto novo) | 10 | Analytics interno |
| DAU / MAU | 0 | 60% | Analytics interno |
| Tempo médio para encontrar link | ~15 min (Slack) | < 30s | Pesquisa com usuários |

---

## 14. Premissas

- [ASSUMIDO: fetch do título da página via server-side para evitar CORS — usuário não especificou]
- [ASSUMIDO: um usuário pertence a apenas um workspace nesta versão]
- [ASSUMIDO: tags são texto livre, sem taxonomia pré-definida]

---

## 15. Riscos

| Risco | Probabilidade | Impacto | Mitigação |
|-------|--------------|---------|-----------|
| Auto-fetch de título bloqueado por CORS ou robots.txt | Alta | Baixo | Graceful degradation: campo editável manualmente |
| Adoção baixa se o time continuar usando Slack para links | Média | Alto | Foco em UX rápido: salvar em < 10s |

---

## 16. Dúvidas em Aberto

| # | Questão | Impacto | Responsável |
|---|---------|---------|-------------|
| 1 | Convite por email envia email real ou apenas link de convite? | Fluxo de onboarding | Produto |
| 2 | Coleções são privadas ou compartilhadas no workspace? | Modelo de dados e permissões | Produto |
