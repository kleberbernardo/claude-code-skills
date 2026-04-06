# Integration Planning Guide — Product Builder

Como planejar a integração entre frontend e backend.

---

## Contrato de API

Antes de implementar qualquer um dos lados, definir o contrato:

```typescript
// Definir types compartilhados (se possível em arquivo comum)

// Request
interface CriarItemRequest {
  titulo: string;        // min 3, max 100
  valor: number;         // > 0
  status?: 'pendente' | 'ativo';  // default: 'pendente'
}

// Response Success
interface CriarItemResponse {
  id: number;
  titulo: string;
  valor: number;
  status: string;
  created_at: string;  // ISO 8601
}

// Response Error
interface ApiError {
  error: string;
  field?: string;      // presente em erros de validação
  code?: string;       // código de erro para i18n
}
```

---

## Sequência de Implementação Recomendada

```
1. Backend: schema de banco
2. Backend: validação (zod/yup)
3. Backend: endpoint (mock ou real)
4. Frontend: hook/service que chama o endpoint
5. Frontend: componente que usa o hook
6. Integração: testar fluxo completo
```

Por que essa ordem?
- Backend define o contrato
- Frontend consome o contrato
- Evita retrabalho de reescrever types

---

## Mapeamento de Erros Backend → Frontend

| Backend retorna | Frontend exibe |
|----------------|----------------|
| 400 + field | Erro no campo específico do formulário |
| 400 sem field | Toast de erro genérico |
| 401 | Redirect para login |
| 403 | Mensagem "sem permissão" na tela |
| 404 | Redirect para 404 ou empty state |
| 409 | "Já existe" no campo relevante |
| 422 | Erros de validação por campo |
| 429 | "Muitas tentativas, aguarde X minutos" |
| 500+ | Toast "Erro interno, tente novamente" |

---

## Sincronização de Estado

Após mutação bem-sucedida, o frontend deve:

| Operação | Ação no Estado |
|----------|---------------|
| Criar | Adicionar item à lista local OU refetch |
| Atualizar | Atualizar item na lista local OU refetch |
| Deletar | Remover item da lista local (otimista) |
| Erro em delete otimista | Reverter remoção + mostrar erro |

**Preferir update otimista** para operações de delete — UX mais fluida.
**Preferir refetch** para operações de create complexas — dados podem ter server-side transforms.

---

## Checklist de Integração

- [ ] Types são consistentes entre BE e FE
- [ ] Formato de data está padronizado (ISO 8601)
- [ ] Paginação: FE envia `page` e `limit`, BE retorna `total` e `hasMore`
- [ ] Erros de campo: BE retorna `field`, FE mapeia para o input correto
- [ ] Auth token: passado no header `Authorization: Bearer <token>`
- [ ] CORS configurado se BE e FE em domínios diferentes
- [ ] Loading states cobrem toda a duração da request
- [ ] Cancelamento de request se componente desmontar (AbortController)

---

## Problemas Comuns de Integração

### Formato de data inconsistente
```typescript
// Backend retorna: "2026-04-06T10:00:00.000Z"
// Frontend esperava: "06/04/2026"
// Solução: sempre usar ISO 8601 no contrato, formatar no FE
const dataFormatada = new Date(item.created_at).toLocaleDateString('pt-BR');
```

### Snake_case vs camelCase
```typescript
// Backend retorna: { user_id: 1, created_at: "..." }
// Frontend espera: { userId: 1, createdAt: "..." }
// Solução: definir no contrato qual padrão usar. Recomendado: camelCase no FE
```

### Response sem paginação
```typescript
// Backend retorna: []  (sem metadados)
// Frontend não sabe se tem mais dados
// Solução: sempre retornar { data: [], total: N, page: N, limit: N }
```

### Error sem field
```typescript
// Backend: { error: "Validation failed" }
// Frontend não sabe qual campo mostrar o erro
// Solução: { error: "Campo inválido", field: "email", code: "INVALID_EMAIL" }
```
