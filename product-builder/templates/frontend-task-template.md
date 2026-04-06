---
id: FE-TASK-001
feature: FEAT-001
title: [Título Descritivo]
type: frontend
status: draft
created: [DATA]
depends_on: [BE-TASK-001]
estimate: M
---

# FE-TASK-001 — [Título Descritivo]

## Contexto

Feature: [FEAT-001 — Nome]
[Por que esta task existe. Qual tela/componente ela implementa.]

## Escopo

O que **será** implementado:
- Tela/componente [X]
- Estado de loading, error, empty, success
- Integração com endpoint [METHOD /api/path]
- Formulário/interação [Y]

O que **não será** implementado:
- [Fora do escopo desta task]

## Telas / Componentes

### [Nome da Tela/Componente]

**Dados exibidos:**
- [Campo 1]: [tipo de dado]
- [Campo 2]: [tipo de dado]

**Ações disponíveis:**
- [Botão/Link 1]: [o que faz]
- [Botão/Link 2]: [o que faz]

**Layout (alto nível):**
```
[Descrição visual ou ASCII art do layout]

Header: título + botão de ação
Body:
  - Se loading: skeleton de 3 itens
  - Se empty: ícone + título + CTA
  - Se data: lista/grid de cards
  - Se error: mensagem + botão retry
Footer: paginação (se aplicável)
```

## Estados a Implementar

| Estado | Trigger | UI |
|--------|---------|-----|
| Loading | Ao montar / ao submeter | Skeleton / botão com spinner |
| Success | Response 2xx | Dados renderizados |
| Empty | Success + lista vazia | [Empty state específico] |
| Error | Response 4xx/5xx | [Mensagem + retry] |
| Disabled | [Condição] | [Botão/campo desabilitado] |

## Integração de API

### Carregar dados (se aplicável)
- Endpoint: `GET /api/[path]`
- Quando: ao montar componente
- Loading: [o que mostrar]
- Error: [como tratar]
- Success: [como renderizar]

### Ação principal (se aplicável)
- Endpoint: `[METHOD] /api/[path]`
- Quando: ao [submit / clicar em botão]
- Submitting: [botão disabled + texto "Salvando..."]
- Error 400: [mostrar erro no campo field]
- Error 500: [toast de erro genérico]
- Success: [toast + redirecionar / atualizar lista]

## Gerenciamento de Estado

| Estado | Onde | Por quê |
|--------|------|---------|
| [dados da lista] | useState local | Não compartilhado |
| [isLoading] | useState local | Scoped ao componente |
| [usuário logado] | Context / store global | Compartilhado entre telas |

## Formulário (se aplicável)

| Campo | Tipo Input | Validação Client | Mensagem |
|-------|-----------|-----------------|---------|
| [campo] | text / email / number | [regra] | "[mensagem]" |

- Validar no: blur (saída do campo) + submit
- Botão submit: disabled se form inválido ou submitting

## Navegação

- Rota de entrada: `[/path]`
- Botão voltar: `[para onde]`
- Após criar/editar: redirecionar para `[/path]`
- Não autenticado: redirecionar para `/login?redirect=[path atual]`

## Responsividade

| Breakpoint | Layout |
|-----------|--------|
| Mobile < 640px | Stack vertical, botões full-width |
| Tablet 640-1024px | [mudança] |
| Desktop > 1024px | [layout completo] |

## Dependências

**Requer (deve estar pronto antes):**
- [BE-TASK-001: endpoint /api/path precisa existir]
- [FE-TASK-NNN: componente base necessário]

**Bloqueia:**
- [QA-TASK-001: precisa desta tela para testar]

## Critérios de Aceite

```
[P0] AC-001:
Given usuário está na tela [X]
When dados carregam com sucesso
Then vê lista de [Y] com todos os campos

[P0] AC-002:
Given usuário está na tela [X]
When dados estão carregando
Then vê skeleton (não tela em branco ou dados antigos)

[P0] AC-003:
Given formulário com campo obrigatório vazio
When usuário clica "Salvar"
Then vê mensagem de erro no campo e botão não ativa o submit

[P1] AC-004:
Given usuário em mobile (375px)
When acessa a tela
Then todo o conteúdo é legível e todos os botões são clicáveis
```

## Estimativa

**Tamanho:** [XS | S | M | L | XL]  
**Motivo:** [justificativa]
