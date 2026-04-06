# Frontend Planning Guide — Product Builder

Guia completo para planejar implementações de frontend com qualidade de produção.

---

## Princípios

1. **Frontend é primeira classe** — não é afterthought do backend
2. **Estados primeiro** — antes de codar, mapear todos os estados possíveis
3. **Componentes pequenos** — um componente faz uma coisa bem
4. **Dados reais** — nunca assumir que a API retorna dados perfeitos
5. **Mobile first** — planejar para telas pequenas primeiro

---

## Checklist de Planejamento Frontend

### 1. Entender o Contexto

- [ ] Qual framework? (Next.js App Router / Pages Router / React / Vue / etc.)
- [ ] Como estado é gerenciado? (useState / Zustand / Jotai / Redux / Context)
- [ ] Como API é chamada? (fetch / axios / react-query / SWR / tRPC)
- [ ] Existe design system ou componentes base? (shadcn? Radix? Chakra? Custom?)
- [ ] Qual sistema de estilos? (Tailwind / CSS Modules / Styled Components)
- [ ] Existe padrão de formulários? (react-hook-form? Formik? Manual?)
- [ ] Como autenticação funciona no frontend? (cookies? localStorage? contexto?)

### 2. Mapear Componentes

Criar hierarquia antes de codar:

```
Page (Server Component se Next.js 13+)
  └── ClientWrapper (Client Component se precisar de estado)
        ├── Header (recebe props)
        ├── ListaItens
        │     ├── ItemCard (repetido)
        │     │     ├── BadgeStatus
        │     │     └── BotaoAção
        │     └── EmptyState (quando vazio)
        ├── Modal (condicional)
        └── Toast (global)
```

Regras:
- Se usa estado ou event handlers → Client Component
- Se apenas exibe dados → pode ser Server Component (Next.js)
- Props vs estado: dados externos → props; UI local → estado

### 3. Mapear Todos os Estados

Para CADA componente/tela, listar:

| Estado | Trigger | UI |
|--------|---------|-----|
| Idle | Inicial | [o que aparece] |
| Loading | Request enviado | Skeleton/Spinner |
| Success | Response 2xx | Dados renderizados |
| Empty | Success + lista vazia | Empty state component |
| Error | Response 4xx/5xx | Mensagem + retry |
| Submitting | Form submit | Botão disabled + spinner |
| Disabled | Sem permissão | Campos/botões desabilitados |

### 4. Planejar Chamadas de API

Para cada endpoint que a tela consome:

```typescript
// Documentar no plano:
// 1. Quando chamar
// 2. Como tratar loading
// 3. Como tratar erro
// 4. O que atualizar após sucesso

// Exemplo:
// Carregar lista: ao montar componente
//   Loading → mostrar 3 skeletons
//   Error → mensagem "Não foi possível carregar" + botão "Tentar novamente"
//   Success → renderizar lista

// Criar item: ao submeter formulário
//   Submitting → botão "Criando..." desabilitado
//   Error 400 → mostrar erro de validação no campo
//   Error 500 → toast "Erro interno, tente novamente"
//   Success → fechar modal + adicionar item à lista + toast "Criado!"
```

### 5. Planejar Formulários

Para cada formulário:

| Campo | Tipo | Obrigatório? | Validação | Mensagem de Erro |
|-------|------|-------------|-----------|-----------------|
| nome | text | Sim | min 2 chars | "Nome muito curto" |
| email | email | Sim | formato email | "E-mail inválido" |
| valor | number | Sim | > 0 | "Valor deve ser positivo" |

- Quando validar: no blur (ao sair do campo) e no submit
- Botão submit: desabilitado se form inválido?
- Reset: quando limpar o form?

### 6. Planejar Navegação

- Rota de entrada para esta tela
- Links/botões que saem desta tela e para onde vão
- Redirect após sucesso (ex: criar item → ir para detalhe)
- Redirect para login se não autenticado (com redirect_uri)
- Comportamento do botão "Voltar"

### 7. Planejar Responsividade

Para cada layout:

```
Mobile (< 640px):
  - Stack vertical
  - Botões full-width
  - Tabelas → cards

Tablet (640px - 1024px):
  - Grid 2 colunas
  - Sidebar colapsável

Desktop (> 1024px):
  - Grid 3 colunas
  - Sidebar fixa
```

### 8. Planejar Otimizações

- Dados que podem ser cacheados (react-query staleTime)
- Imagens que precisam de `next/image`
- Código que pode ser lazy loaded
- Listas longas → virtualização necessária?

---

## Padrões Comuns a Identificar no Projeto

Antes de planejar, buscar exemplos existentes de:

```bash
# Como loading é feito
grep -rn "isLoading\|loading\|skeleton" app/ --include="*.tsx" | head -10

# Como erros são tratados
grep -rn "catch\|onError\|error\b" app/ --include="*.tsx" | head -10

# Como API é chamada
grep -rn "fetch\|useQuery\|useMutation\|axios" app/ --include="*.tsx" | head -10

# Componentes de feedback existentes
grep -rn "toast\|notify\|alert\|modal" app/ --include="*.tsx" | head -10

# Como formulários são feitos
grep -rn "handleSubmit\|onSubmit\|useState.*form" app/ --include="*.tsx" | head -10
```

---

## Red Flags de Frontend

- Chamar API sem tratar loading state → tela pisca
- Não tratar erro → tela quebra silenciosamente
- Estado vazio não implementado → lista vazia confunde usuário
- Botão submit sem disabled durante loading → duplo submit
- Validação só no backend → UX ruim
- Sem feedback após ação → usuário não sabe se funcionou
- Props drilling > 3 níveis → usar context ou estado global
- `any` no TypeScript → bugs escondidos
- Sem key em listas renderizadas → bugs de reconciliação React
