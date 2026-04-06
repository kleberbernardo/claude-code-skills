---
id: FEAT-001
title: [Nome da Feature]
status: draft
created: [DATA]
depends_on: []
---

# FEAT-001 — [Nome da Feature]

## Descrição

[O que esta feature faz em 2-3 frases. Qual valor entrega para o usuário?]

## Por que Esta Feature Existe

[Motivação do negócio ou da experiência do usuário]

## Regras de Negócio

- RN-001: [Regra explícita — ex: "Apenas o criador pode editar a tarefa"]
- RN-002: [Regra — ex: "Valor mínimo é R$ 1,00"]
- RN-003: [Regra — ex: "Status só pode avançar, não voltar"]
- RN-004: [Regra — ex: "Limite de 100 itens por usuário"]

## Fluxo do Usuário

### Fluxo Principal
1. Usuário está em [tela X]
2. Clica em [ação Y]
3. [O que acontece]
4. Usuário vê [resultado]
5. Pode então [próxima ação]

### Fluxo Alternativo 1: [Nome]
1. [Condição diferente]
2. [Resultado diferente]

### Fluxo de Erro
1. [Quando dá errado]
2. [O que o usuário vê]
3. [O que pode fazer]

## Telas Envolvidas

| Tela | Descrição | Tipo |
|------|-----------|------|
| [Nome da Tela] | [O que contém] | Nova / Modificada |

## Estados de UI

### Loading
[O que aparece enquanto dados carregam]
> Ex: "Skeleton de 3 cards com animação pulse"

### Success
[Estado com dados carregados]

### Empty State
[Quando não há dados]
> Título: "[Texto]"
> Descrição: "[Texto de apoio]"
> CTA: "[Botão para ação principal]"

### Error State
[Quando ocorre erro]
> Mensagem: "[Texto]"
> Ação disponível: "[Botão Tentar Novamente]"

### Disabled State (se aplicável)
[Quando ação não está disponível e por quê]

## Validações

| Campo | Regra | Mensagem de Erro |
|-------|-------|-----------------|
| [Campo] | [Regra — ex: obrigatório, min 3 chars] | "[Mensagem exibida ao usuário]" |

**Quando validar:** ao sair do campo (blur) + ao enviar formulário

## Mensagens ao Usuário

| Situação | Tipo | Texto |
|----------|------|-------|
| Ação concluída com sucesso | Toast success | "[Texto]" |
| Erro de validação | Inline error | "[Texto por campo]" |
| Erro de conexão | Toast error | "[Texto]" |
| Empty state | Inline | "[Título + descrição]" |
| Confirmação de delete | Modal | "[Pergunta de confirmação]" |

## Dependências

### Features que devem existir antes
- [FEAT-XXX: Nome] — [por quê]

### Serviços externos necessários
- [Serviço] — [para quê]

### Dados necessários no banco
- [Entidade] — [campos]

## Critérios de Aceite

### Happy Path
```
[P0] AC-001:
Given [contexto]
When [ação]
Then [resultado esperado]
```

### Validações
```
[P0] AC-002:
Given campo [X] vazio
When usuário tenta submeter
Then vê mensagem "[mensagem específica]"
```

### Estados de UI
```
[P1] AC-003:
Given usuário abriu [tela]
When dados ainda estão carregando
Then vê skeleton/spinner (não tela em branco)
```

### Erros
```
[P0] AC-004:
Given [condição de erro]
When [trigger]
Then [mensagem útil + ação possível]
```

### Responsividade
```
[P1] AC-005:
Given usuário em mobile (375px)
When acessa [tela]
Then layout é legível e todos os botões são clicáveis
```

## Notas de Implementação

[Qualquer detalhe técnico relevante para o planejamento — não é o plano técnico, mas hints]
