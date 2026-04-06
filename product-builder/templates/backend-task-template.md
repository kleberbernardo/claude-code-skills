---
id: BE-TASK-001
feature: FEAT-001
title: [Título Descritivo]
type: backend
status: draft
created: [DATA]
depends_on: []
estimate: S
---

# BE-TASK-001 — [Título Descritivo]

## Contexto

Feature: [FEAT-001 — Nome]
[Por que esta task existe. Qual parte do backend ela implementa.]

## Escopo

O que **será** implementado nesta task:
- [Item 1]
- [Item 2]

O que **não será** implementado (fora do escopo):
- [Item fora do escopo]

## Endpoints

### [METHOD] /api/[path]

**Auth:** Sim (Bearer token) / Não  
**Rate limit:** [X req/min] / Sem limite especial

**Request Body:**
```typescript
{
  campo1: string;    // obrigatório, min 3 chars
  campo2: number;    // obrigatório, > 0
  campo3?: string;   // opcional
}
```

**Response 200:**
```typescript
{
  id: number;
  campo1: string;
  // ...
  created_at: string; // ISO 8601
}
```

**Response 400 (validação):**
```typescript
{ error: string; field?: string }
// Ex: { error: "Campo obrigatório", field: "campo1" }
```

**Response 401:** `{ error: "Token inválido ou expirado" }`  
**Response 403:** `{ error: "Sem permissão" }`  
**Response 404:** `{ error: "[Recurso] não encontrado" }`  
**Response 500:** `{ error: "Erro interno" }`

## Banco de Dados

### Tabela/Modelo Afetado
```
[Tabela]: [ação — criar / modificar / consultar]
  - [campo]: [tipo] — [obrigatório/opcional]
```

### Migration Necessária
```sql
-- Descrever o que a migration faz
-- ALTER TABLE ... / CREATE TABLE ...
```

## Lógica de Negócio

```
1. Validar input com schema
2. Verificar autenticação (extrair userId do token)
3. Verificar autorização (usuário pode fazer isso?)
4. [Lógica específica passo a passo]
5. Persistir no banco
6. Retornar response formatado
```

## Validações

| Campo | Regra | Código de Erro |
|-------|-------|---------------|
| campo1 | obrigatório, min 3, max 100 | CAMPO1_REQUIRED / CAMPO1_TOO_SHORT |
| campo2 | obrigatório, number, > 0 | CAMPO2_REQUIRED / CAMPO2_INVALID |

## Dependências

**Requer (tasks que devem estar prontas antes):**
- [Nenhuma] ou [BE-TASK-NNN: motivo]

**Bloqueia (tasks que dependem desta):**
- [FE-TASK-001: precisa deste endpoint]

## Critérios de Aceite

```
[P0] AC-T001:
Given request POST /api/[path] com body válido e token válido
When executado
Then retorna 201 com objeto criado e todos os campos

[P0] AC-T002:
Given request com campo obrigatório ausente
When executado
Then retorna 400 com { error: "[mensagem]", field: "[campo]" }

[P0] AC-T003:
Given request sem Authorization header
When executado
Then retorna 401

[P1] AC-T004:
Given request de usuário sem permissão
When executado
Then retorna 403

[P1] AC-T005:
Given 2 requests idênticos simultâneos
When executados
Then não cria duplicata (idempotente)
```

## Estimativa

**Tamanho:** [XS | S | M | L | XL]  
**Motivo:** [justificativa da estimativa]
