---
id: BE-TASK-001-plan
task: BE-TASK-001
feature: FEAT-001
status: draft
created: [DATA]
---

# Plano Backend — BE-TASK-001: [Título]

## 1. Análise do Projeto

**Framework:** [Next.js API Routes / Express / Fastify / Hono]  
**ORM:** [Prisma / Drizzle / Mongoose / raw SQL]  
**Auth:** [JWT / Session / NextAuth / Clerk]  
**Validação:** [Zod / Yup / class-validator / manual]  
**Formato de erro padrão:** `{ error: string, field?: string }`  
**Rate limiting:** [global / por endpoint / ausente]

**Padrões identificados:**
- Estrutura de route existente: [exemplo de route similar]
- Como auth é verificada: [exemplo]
- Como validação é feita: [exemplo]

## 2. Arquivos a Criar/Modificar

| Arquivo | Ação | Descrição |
|---------|------|-----------|
| `app/api/[path]/route.ts` | Criar | Handler do endpoint |
| `lib/validations/[name].ts` | Criar | Schema de validação Zod |
| `prisma/schema.prisma` | Modificar | Novo modelo [Nome] |
| `lib/[name].ts` | Criar | Lógica de negócio |

## 3. Schema de Banco de Dados

```prisma
model [NomeModelo] {
  id         Int      @id @default(autoincrement())
  [campo1]   String
  [campo2]   Float
  userId     Int
  user       User     @relation(fields: [userId], references: [id])
  createdAt  DateTime @default(now())
  updatedAt  DateTime @updatedAt

  @@index([userId])
}
```

**Migration:** [descrever o que a migration faz]

## 4. Schema de Validação

```typescript
// lib/validations/[name].ts
import { z } from 'zod';

export const criar[Nome]Schema = z.object({
  campo1: z.string().min(3, "Mínimo 3 caracteres").max(100),
  campo2: z.number().positive("Deve ser positivo"),
  campo3: z.enum(["opcao1", "opcao2"]).optional().default("opcao1"),
});

export type Criar[Nome]Input = z.infer<typeof criar[Nome]Schema>;
```

## 5. Lógica de Negócio

```typescript
// Pseudocódigo do handler principal
async function POST(req: NextRequest) {
  // 1. Verificar autenticação
  const session = await getSession(req);
  if (!session) return 401;
  
  // 2. Validar input
  const body = await req.json();
  const result = schema.safeParse(body);
  if (!result.success) return 400 com erros;
  
  // 3. Verificar regras de negócio
  const [verificação específica];
  if (!válido) return 422;
  
  // 4. Persistir
  const item = await db.[modelo].create({ data: { ...result.data, userId: session.userId } });
  
  // 5. Retornar
  return 201 com item criado;
}
```

## 6. Tratamento de Erros

| Situação | Status | Response |
|----------|--------|----------|
| Input inválido | 400 | `{ error: "[msg]", field: "[campo]" }` |
| Não autenticado | 401 | `{ error: "Autenticação necessária" }` |
| Sem permissão | 403 | `{ error: "Sem permissão" }` |
| Recurso não encontrado | 404 | `{ error: "[Recurso] não encontrado" }` |
| Conflito/duplicata | 409 | `{ error: "[Recurso] já existe" }` |
| Erro interno | 500 | `{ error: "Erro interno" }` |

## 7. Decisões Técnicas

| Decisão | Alternativa | Motivo |
|---------|------------|--------|
| [Decisão 1] | [Alt] | [Motivo] |

## 8. Critérios de Aceite Técnicos

```
[P0] AC-T001:
Given POST /api/[path] com body válido e token válido
When executado
Then retorna 201 com objeto completo

[P0] AC-T002:
Given campo obrigatório ausente no body
When executado
Then retorna 400 com { error: "...", field: "[campo]" }

[P0] AC-T003:
Given request sem Authorization header
When executado
Then retorna 401

[P1] AC-T004:
Given userId do token diferente do recurso
When executado tentando modificar recurso alheio
Then retorna 403
```

## 9. Passos de Implementação

1. Criar migration e schema no Prisma
2. Executar `npx prisma migrate dev`
3. Criar schema de validação Zod
4. Criar lógica de negócio em `lib/[name].ts`
5. Criar route handler em `app/api/[path]/route.ts`
6. Testar manualmente com curl/Postman
