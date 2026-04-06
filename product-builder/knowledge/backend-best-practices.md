# Backend Best Practices — Product Builder

Práticas essenciais para backend de produção.

---

## Sempre Validar Input

- Toda rota que aceita input deve validar com schema (Zod, Yup, etc.)
- Nunca confiar no frontend para validação de negócio
- Mensagens de erro de validação devem indicar o campo problemático
- Sanitizar strings que vão para queries, logs ou emails

---

## Autenticação e Autorização

- Verificar autenticação antes de qualquer lógica de negócio
- Verificar ownership do recurso (não só autenticação)
- Nunca retornar dados de outro usuário sem verificar permissão
- Extrair userId do token, nunca do body da request

---

## Respostas Consistentes

Definir formato padrão e seguir em toda a aplicação:

```typescript
// Sucesso
{ data: T }  // ou diretamente T

// Erro
{ error: string; field?: string; code?: string }

// Paginação
{ data: T[]; total: number; page: number; limit: number }
```

---

## Erros HTTP Semânticos

| Situação | Status |
|----------|--------|
| Input inválido | 400 |
| Não autenticado | 401 |
| Sem permissão | 403 |
| Não encontrado | 404 |
| Conflito/duplicata | 409 |
| Validação de negócio | 422 |
| Erro interno | 500 |

Nunca retornar 200 com `{ success: false }` para erro.

---

## Database

- Usar ORM para evitar SQL injection
- Selecionar apenas os campos necessários (`select`)
- Usar `include` com critério (não incluir tudo por padrão)
- Paginação obrigatória em listagens
- Índices para campos de filtro frequentes (userId, status, createdAt)
- Transações para operações que afetam múltiplas tabelas

---

## Não Vazar Informações

- Nunca retornar stack trace em produção
- Log de erro interno, mensagem genérica para o cliente
- Não retornar IDs sequenciais onde IDOR é risco (usar UUIDs)
- Não retornar campos sensíveis (senha hash, tokens internos)

---

## Anti-patterns

| Anti-pattern | Problema |
|-------------|---------|
| `if (user)` sem checar ownership | IDOR — acessa dados de outros |
| Catch sem log | Erros silenciosos em produção |
| Query sem WHERE de userId | Retorna dados de todos os usuários |
| String concatenation em query | SQL injection |
| Senha em plaintext | Crítico |
| Token em URL query param | Leak em logs de servidor |
