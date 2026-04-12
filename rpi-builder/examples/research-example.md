# Exemplo de Research — rpi-builder

Este exemplo ilustra o nível de detalhe e especificidade esperado em um research.

**Projeto:** LinkHub (plataforma de gerenciamento de links para times)
**Task:** 003 — Implementar endpoint de criação de link

---

# Research - Task 003

## 1. Referências

- Task: .ai/product/tasks/003-criar-endpoint-link.md
- PRD: .ai/product/prd.md
- Design: .ai/product/design.md

---

## 2. Resumo da Tarefa

Criar o endpoint `POST /api/links` que recebe URL, título opcional e tags opcionais, valida, faz auto-fetch do título se ausente, verifica duplicata no workspace, persiste e retorna o link criado. Requer autenticação. Pertence ao módulo de links do design técnico.

---

## 3. Objetivo da Análise

Esta análise visa responder:
- Como os endpoints existentes no projeto estão estruturados?
- Como autenticação é verificada nas rotas existentes?
- Como o banco é acessado nas operações existentes?
- Quais são os padrões de tratamento de erro?
- O que já existe do módulo de links?
- Existe constraint de unicidade no schema?

---

## 4. Arquivos Relevantes Encontrados

| Arquivo | Status | Relevância |
|---------|--------|-----------|
| `src/app/api/links/route.ts` | Não existe | Precisará ser criado — é o endpoint alvo |
| `src/modules/links/link.service.ts` | Não existe | Precisará ser criado — service do módulo |
| `src/lib/fetch-title.ts` | Não existe | Precisará ser criado — auto-fetch de título |
| `src/app/api/workspaces/route.ts` | Existe | Referência de padrão para route handlers Next.js |
| `src/modules/workspaces/workspace.service.ts` | Existe | Referência de padrão para services |
| `src/db/schema.ts` | Existe | Onde a tabela `links` será definida (task 002) |
| `src/lib/errors.ts` | Existe | Tipos de erro usados no projeto |
| `src/lib/auth.ts` | Existe | Exporta `getServerSession` wrapper |

---

## 5. Componentes / Módulos Envolvidos

### Módulo: workspaces (referência de padrão)
- **Localização:** `src/modules/workspaces/`
- **Responsabilidade:** CRUD de workspaces, membership
- **Relação com a task:** É o padrão de estrutura que o módulo `links` deve seguir

### Módulo: links (a ser criado)
- **Localização:** `src/modules/links/` (pasta ainda não existe)
- **Responsabilidade:** CRUD de links no contexto de um workspace
- **Relação com a task:** Este módulo será criado nesta task

### Lib: errors
- **Localização:** `src/lib/errors.ts`
- **Responsabilidade:** Define `AppError`, `ValidationError`, `ConflictError`, `NotFoundError`
- **Relação com a task:** Os erros desta task usam essas classes

### Lib: auth
- **Localização:** `src/lib/auth.ts`
- **Responsabilidade:** Wrapper de `getServerSession` com tipagem do projeto
- **Relação com a task:** O endpoint usa `getSession()` desta lib

---

## 6. Estado Atual do Sistema

### O que já existe e pode ser reaproveitado

- `src/lib/errors.ts` exporta `ValidationError`, `ConflictError` — usar diretamente
- `src/lib/auth.ts` exporta `getSession(): Promise<Session | null>` — usar para verificar auth
- `src/db/index.ts` exporta `db` (instância Drizzle) — usar para queries
- `src/modules/workspaces/workspace.service.ts` — padrão de estrutura de service a seguir

### O que existe e precisará ser modificado

- `src/db/schema.ts` — a tabela `links` será adicionada na task 002 (dependência); esta task assume que já existe

### O que não existe e precisará ser criado

- `src/modules/links/` (pasta completa)
- `src/lib/fetch-title.ts`
- `src/app/api/links/route.ts`

---

## 7. Dependências Identificadas

### Dependências internas

| Módulo/Arquivo | Tipo | Uso na task |
|---------------|------|-------------|
| `src/lib/auth.ts` | Import direto | Verificar sessão no handler |
| `src/lib/errors.ts` | Import direto | Lançar ValidationError e ConflictError |
| `src/db/index.ts` | Import direto | Queries no service |
| `src/db/schema.ts` | Import via `db` | Schema da tabela `links` (deve existir após task 002) |

### Dependências externas

| Pacote | Versão | Uso |
|--------|--------|-----|
| `next` | `14.1.0` | `NextResponse` no route handler |
| `drizzle-orm` | `0.30.4` | `eq`, `and` para queries |

Não há necessidade de instalar pacotes novos — `node-fetch` não é necessário pois `fetch` é nativo no Node.js 18+.

---

## 8. Regras e Contratos Relevantes

### Padrão do route handler existente (src/app/api/workspaces/route.ts)

```typescript
// Padrão encontrado no projeto
export async function POST(req: Request) {
  const session = await getSession()
  if (!session) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 })
  }

  try {
    const body = await req.json()
    const result = await workspaceService.create(body, session.user.id)
    return NextResponse.json(result, { status: 201 })
  } catch (error) {
    if (error instanceof ValidationError) {
      return NextResponse.json({ error: error.message }, { status: 400 })
    }
    if (error instanceof ConflictError) {
      return NextResponse.json({ error: error.message }, { status: 409 })
    }
    return NextResponse.json({ error: 'Internal server error' }, { status: 500 })
  }
}
```

### Padrão do service existente (src/modules/workspaces/workspace.service.ts)

```typescript
// Estrutura usada no projeto
export async function createWorkspace(name: string, ownerId: string) {
  // 1. Validar input
  if (!name || name.trim().length === 0) {
    throw new ValidationError('Workspace name is required')
  }
  // 2. Check de conflito
  const existing = await db.query.workspaces.findFirst({
    where: eq(workspaces.name, name)
  })
  if (existing) throw new ConflictError('Workspace already exists')
  // 3. Inserir
  const [workspace] = await db.insert(workspaces)
    .values({ name: name.trim(), ownerId })
    .returning()
  return workspace
}
```

### Tipos do Design (design.md — Seção 7)

```typescript
type CreateLinkInput = {
  url: string;
  title?: string;
  tags?: string[];
}

type LinkRecord = {
  id: string;
  url: string;
  title: string;
  tags: string[];
  workspaceId: string;
  createdById: string;
  createdAt: Date;
}
```

### Regras de negócio (PRD seções 7 e 8)

- **RN-001:** Link associado ao workspace do usuário autenticado
- **RN-004:** URL única dentro do workspace — `UNIQUE(workspace_id, url)`
- **RN-005:** Título obrigatório — auto-fetch ou manual

---

## 9. Padrões Existentes no Projeto

### Estrutura de pastas de módulos

```
src/modules/workspaces/
  workspace.service.ts   ← lógica de negócio
  workspace.types.ts     ← tipos locais do módulo (além dos do design)
```

A task deve criar `src/modules/links/` seguindo a mesma estrutura.

### Tratamento de erros

- `ValidationError(message)` → 400
- `ConflictError(message)` → 409
- `NotFoundError(message)` → 404
- Erros não tratados → 500 com "Internal server error"

### Acesso ao banco

```typescript
// Query
db.query.[tabela].findFirst({ where: ... })
db.query.[tabela].findMany({ where: ... })

// Insert
db.insert([tabela]).values({ ... }).returning()

// Update
db.update([tabela]).set({ ... }).where(...).returning()
```

### Session shape (de src/lib/auth.ts)

```typescript
type Session = {
  user: {
    id: string;
    email: string;
    workspaceId: string;  // ← workspaceId está na session
  }
}
```

---

## 10. Impacto Esperado da Mudança

### Arquivos afetados

- `src/modules/links/` (pasta nova com service)
- `src/lib/fetch-title.ts` (arquivo novo)
- `src/app/api/links/route.ts` (arquivo novo)

### Fluxos ativados

- Fluxo técnico "Salvar Link" do design.md (seção 9) será implementado

### Módulos que precisam ser notificados

- Nenhum — a task cria endpoint novo sem alterar módulos existentes

---

## 11. Pontos de Atenção

- `workspaceId` vem da session (`session.user.workspaceId`), não do body da request. Não aceitar workspaceId do cliente.
- `fetch` nativo do Node.js 18+ não suporta timeout via `AbortController` com sintaxe simples — usar `AbortSignal.timeout(3000)` (disponível no Node 18.x).
- O schema da tabela `links` é criado na task 002. Esta task tem task 002 como dependência declarada.

---

## 12. Riscos e Conflitos Possíveis

| Risco | Evidência | Impacto | Para o plano |
|-------|-----------|---------|--------------|
| Race condition no check de duplicata | `workspace.service.ts:34` usa o mesmo padrão sem lock | Baixo (mitigado por constraint) | Plano deve incluir constraint UNIQUE no schema como segunda linha de defesa |
| Sites que bloqueiam bots bloqueiam o fetch de título | Comportamento esperado da web | Baixo (degradação graciosa) | Plano deve definir: se fetch falha, retornar null — nunca lançar exceção |
| Schema da task 002 pode não ter constraint UNIQUE | `src/db/schema.ts` ainda não tem tabela links | Médio | Verificar no início da implementação e adicionar se ausente |

---

## 13. Lacunas ou Dúvidas Encontradas

| # | Lacuna | Impacto |
|---|--------|---------|
| 1 | `Session.user.workspaceId` encontrado em `src/lib/auth.ts` — mas o design não documenta explicitamente que workspaceId vem da session. Confirmado via código. | Nenhum — comportamento está no código; plano pode assumir com segurança |

Nenhuma lacuna que bloqueie o planejamento.

---

## 14. Resumo Final para Planejamento

**Para implementar esta task:**
- Seguir o padrão handler → service já estabelecido em `src/modules/workspaces/`
- Criar 3 arquivos novos: `src/lib/fetch-title.ts`, `src/modules/links/link.service.ts`, `src/app/api/links/route.ts`
- `workspaceId` deve vir de `session.user.workspaceId` — não do body
- `fetchPageTitle` deve usar `AbortSignal.timeout(3000)` e nunca lançar exceção
- Verificar que schema da task 002 tem constraint `UNIQUE(workspace_id, url)` antes de implementar o check de duplicata
- Sem dependências externas novas
- Lacunas: nenhuma
