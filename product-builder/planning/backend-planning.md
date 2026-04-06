# Backend Planning Guide — Product Builder

Guia completo para planejar implementações de backend robustas.

---

## Checklist de Planejamento Backend

### 1. Entender o Contexto

- [ ] Qual framework? (Next.js API Routes / Express / Fastify / Hono / NestJS)
- [ ] Qual ORM? (Prisma / Drizzle / Mongoose / TypeORM / Sequelize)
- [ ] Como auth é feita? (JWT? Session? NextAuth? Clerk?)
- [ ] Existe middleware de validação? (zod? yup? class-validator?)
- [ ] Qual formato de resposta de erro padrão?
- [ ] Existe rate limiting global?
- [ ] Existe logging estruturado?

### 2. Mapear Endpoints

Para cada endpoint:

```
METHOD /path

Auth: Sim (roles: [admin, user]) / Não
Rate limit: X req/min / sem limite

Request:
  Params: { id: number }
  Query: { page?: number, limit?: number }
  Body: {
    campo: tipo — obrigatório/opcional — validação
  }

Response 200: { schema }
Response 400: { error: string, field?: string }
Response 401: { error: "Não autenticado" }
Response 403: { error: "Sem permissão" }
Response 404: { error: "Não encontrado" }
Response 500: { error: "Erro interno" }
```

### 3. Planejar Schema de Banco

Para cada entidade nova:

```
Tabela: [nome]
  id          Int       @id @default(autoincrement())
  [campo]     [tipo]    — obrigatório/opcional — índice?
  created_at  DateTime  @default(now())
  updated_at  DateTime  @updatedAt

Índices necessários: (para queries frequentes)
Relacionamentos: (FK, cascade?)
Constraints: (unique, check)
```

Perguntas a responder:
- Dados são soft-delete ou hard-delete?
- Precisa de histórico/auditoria?
- Dados têm tenant_id (multi-tenant)?
- Campos sensíveis precisam de criptografia?

### 4. Planejar Lógica de Negócio

Para cada operação, escrever pseudo-código:

```
CRIAR ITEM:
1. Validar input (schema)
2. Verificar autenticação
3. Verificar autorização (usuário pode criar?)
4. Verificar regras de negócio (ex: limite de itens)
5. Persistir no banco
6. Disparar eventos/side effects se necessário
7. Retornar resposta formatada

BUSCAR LISTA:
1. Verificar autenticação
2. Aplicar filtros (só do usuário atual, por status, etc.)
3. Paginação
4. Ordenação
5. Retornar com metadados (total, page, limit)
```

### 5. Planejar Validação

Regras de validação por campo:

| Campo | Tipo | Min | Max | Formato | Obrigatório |
|-------|------|-----|-----|---------|-------------|
| titulo | string | 3 | 100 | texto livre | Sim |
| email | string | - | 255 | email válido | Sim |
| valor | number | 0.01 | 999999 | decimal | Sim |

Validação client-side vs server-side:
- **Sempre validar no backend** — cliente pode ser bypassado
- Frontend valida para UX (feedback imediato)
- Backend valida para segurança (verdade final)

### 6. Planejar Tratamento de Erros

Mapa de erros por endpoint:

| Situação | HTTP Status | Mensagem |
|----------|------------|---------|
| Input inválido | 400 | Mensagem específica por campo |
| Não autenticado | 401 | "Autenticação necessária" |
| Sem permissão | 403 | "Sem permissão para esta ação" |
| Não encontrado | 404 | "[Recurso] não encontrado" |
| Conflito | 409 | "[Recurso] já existe" |
| Erro interno | 500 | "Erro interno, tente novamente" |

### 7. Planejar Performance

- Queries que podem causar N+1 → usar `include` do Prisma ou JOIN
- Queries em tabelas grandes → índices necessários
- Operações pesadas → considerar background job
- Responses grandes → paginação obrigatória

---

## Padrões a Identificar no Projeto

```bash
# Formato de resposta de erro
grep -rn "return.*status\|NextResponse.json" app/api/ --include="*.ts" | head -10

# Como validação é feita
grep -rn "zod\|yup\|validate\|safeParse" app/ lib/ --include="*.ts" | head -10

# Como auth é verificada
grep -rn "getSession\|verifyToken\|auth()\|currentUser" app/api/ --include="*.ts" | head -10

# Padrão de response de sucesso
grep -rn "NextResponse.json.*200\|return.*data" app/api/ --include="*.ts" | head -10
```

---

## Red Flags de Backend

- Query sem WHERE clause em tabela de usuários → retorna todos os dados
- Sem verificação de ownership (userId não validado) → IDOR
- Input de usuário em query SQL diretamente → SQL injection
- Senha em plaintext → crítico
- Token JWT sem verificação de algoritmo → algorithm confusion
- Sem rate limiting em endpoints de auth → brute force
- Error messages revelando stack trace → information disclosure
- `console.log` com dados sensíveis → leak em logs
- Sem paginação em listagens → DoS com lista grande
- Foreign key sem cascade definido → dados órfãos
