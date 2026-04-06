---
id: FE-TASK-001-plan
task: FE-TASK-001
feature: FEAT-001
status: draft
created: [DATA]
---

# Plano Frontend — FE-TASK-001: [Título]

## 1. Análise do Projeto

**Framework:** [Next.js 15 App Router / React / Vue / etc.]  
**Estado:** [useState / Zustand / Jotai / Context]  
**Chamadas API:** [fetch nativo / axios / react-query / SWR]  
**Componentes base:** [shadcn/ui / Radix / custom / nenhum]  
**Estilos:** [Tailwind / CSS Modules / Styled Components]  
**Formulários:** [react-hook-form / Formik / manual]  
**Auth:** [como o token está disponível no frontend]

**Padrões identificados no projeto:**
- Loading: [como outros componentes fazem]
- Erros: [como outros componentes tratam]
- API calls: [padrão existente]
- Formulários: [padrão existente]

## 2. Arquivos a Criar/Modificar

| Arquivo | Ação | Descrição |
|---------|------|-----------|
| `app/[path]/page.tsx` | Criar | Tela principal |
| `components/[Name].tsx` | Criar | Componente X |
| `hooks/use[Name].ts` | Criar | Hook de dados |
| `app/[path]/loading.tsx` | Criar | Loading state (Next.js) |

## 3. Estrutura de Componentes

```
[NomePage] (Server Component)
  └── [ClientWrapper] (Client Component)
        ├── [HeaderComponent]
        │     └── [ActionButton]
        ├── [ListComponent]
        │     ├── [ItemCard] (×N)
        │     │     └── [StatusBadge]
        │     ├── [LoadingSkeleton] (condicional)
        │     └── [EmptyState] (condicional)
        └── [ErrorMessage] (condicional)

[Modal] (condicional, fora da árvore principal via portal)
  └── [Form]
        ├── [InputField] (×N)
        └── [SubmitButton]
```

## 4. Estado e Dados

| Estado | Tipo | Onde | Motivo |
|--------|------|------|--------|
| `items` | `Item[]` | useState | Dados da lista |
| `isLoading` | boolean | useState | Controle de loading |
| `error` | string \| null | useState | Mensagem de erro |
| `isModalOpen` | boolean | useState | Controle do modal |

## 5. Chamadas de API

### Carregar lista (ao montar)
```typescript
// Endpoint: GET /api/[path]
// Loading: mostrar skeleton
// Error: mostrar mensagem + botão retry
// Success: setar items
```

### Criar item (submit do formulário)
```typescript
// Endpoint: POST /api/[path]
// Submitting: botão "Salvando..." + disabled
// Error 400 com field: mostrar erro no campo
// Error 500: toast "Erro interno"
// Success: fechar modal + adicionar à lista + toast "Criado!"
```

## 6. Tratamento de Estados de UI

**Loading inicial:**
```
→ Renderizar skeleton com [N] itens placeholder
→ Animação pulse no skeleton
```

**Erro de carregamento:**
```
→ Ícone de erro
→ Mensagem: "[Texto específico]"
→ Botão: "Tentar novamente" → refetch
```

**Lista vazia:**
```
→ Ícone: [ícone relevante]
→ Título: "[Texto encorajador]"
→ Descrição: "[Orientação de próximo passo]"
→ CTA: "[Botão para criar primeiro item]"
```

## 7. Formulário e Validação

| Campo | Tipo | Validação | Mensagem | Trigger |
|-------|------|-----------|---------|---------|
| [campo] | text | min 3 chars | "[mensagem]" | blur + submit |

Botão submit:
- Disabled quando: form inválido OU isSubmitting
- Texto: "Salvar" / "Salvando..." (durante submit)

## 8. Navegação

- Rota desta tela: `[/path]`
- Redirect após criar: `[/path/id]`
- Não autenticado: `[/login?redirect=/path]`
- Botão voltar: `[/path/parent]`

## 9. Responsividade

| Breakpoint | Mudança de Layout |
|-----------|-----------------|
| Mobile < 640px | [descrever] |
| Tablet 640-1024px | [descrever] |
| Desktop > 1024px | [layout padrão] |

## 10. Decisões Técnicas

| Decisão | Alternativa Considerada | Motivo da Escolha |
|---------|------------------------|------------------|
| [Decisão 1] | [Alternativa] | [Motivo] |

## 11. Critérios de Aceite Técnicos

```
[P0] AC-T001:
Given componente montado com dados disponíveis
When renderiza
Then exibe todos os campos sem erro no console

[P0] AC-T002:
Given isLoading = true
When renderiza
Then exibe skeleton, não dados ou tela em branco

[P0] AC-T003:
Given submit com campo inválido
When usuário clica Salvar
Then campo mostra erro, submit não é enviado

[P1] AC-T004:
Given tela em 375px de largura
When renderiza
Then nenhum elemento está cortado ou inacessível
```

## 12. Passos de Implementação

1. Criar tipos TypeScript para os dados
2. Criar hook `use[Name]` com estado e lógica de API
3. Criar componente `[ItemCard]` (menor, sem dependências)
4. Criar `[EmptyState]` e `[LoadingSkeleton]`
5. Criar `[ListComponent]` que usa os componentes acima
6. Criar formulário/modal
7. Criar página principal que agrega tudo
8. Adicionar à navegação se necessário
