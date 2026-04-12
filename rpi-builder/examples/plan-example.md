# Exemplo de Plan — rpi-builder

Este exemplo ilustra o nível de detalhe esperado em um plan.

**Projeto:** LinkHub
**Task:** 003 — Implementar endpoint de criação de link

---

# Plan - Task 003

## 1. Referências

- Task: .ai/product/tasks/003-criar-endpoint-link.md
- Research: .ai/product/research/003-criar-endpoint-link.md
- PRD: .ai/product/prd.md
- Design: .ai/product/design.md

---

## 2. Objetivo da Implementação

Criar o endpoint `POST /api/links` que recebe URL, título opcional e tags, valida, faz auto-fetch de título quando ausente, verifica duplicata no workspace, persiste e retorna o `LinkRecord` com status 201.

---

## 3. Estratégia Geral

Seguir o padrão handler → service já estabelecido em `src/modules/workspaces/`. O handler (`route.ts`) gerencia autenticação e mapeamento de erros HTTP. O service (`link.service.ts`) contém toda a lógica de negócio: validação de URL, check de duplicata, auto-fetch de título e persistência. A função `fetchPageTitle` fica isolada em `src/lib/fetch-title.ts` para ser testada independentemente. `workspaceId` vem exclusivamente da session — nunca do body da request.

---

## 4. Escopo Exato da Implementação

- [ ] Função `fetchPageTitle(url: string): Promise<string | null>` em `src/lib/fetch-title.ts`
- [ ] Service `createLink(input, userId, workspaceId)` em `src/modules/links/link.service.ts`
- [ ] Route handler `POST /api/links` em `src/app/api/links/route.ts`
- [ ] Testes unitários e de integração em `tests/links/create-link.test.ts`

---

## 5. Fora de Escopo

- Endpoints GET, PUT, DELETE de links (tasks 004, 005, 006)
- Adição de link a coleção (task 007)
- Preview com imagem ou metadados além do título
- Paginação (não há listagem nesta task)
- Não modificar `src/db/schema.ts` — schema é responsabilidade da task 002
- Não modificar `src/lib/auth.ts`, `src/lib/errors.ts` ou `src/db/index.ts`

---

## 6. Arquivos a Criar

| Arquivo | Descrição |
|---------|-----------|
| `src/lib/fetch-title.ts` | Função que faz GET na URL e extrai o `<title>` da página |
| `src/modules/links/link.service.ts` | Service com `createLink` — validação, check de duplicata, fetch de título e insert |
| `src/app/api/links/route.ts` | Route handler Next.js para `POST /api/links` |
| `tests/links/create-link.test.ts` | Testes unitários e de integração |

---

## 7. Arquivos a Modificar

Nenhum arquivo existente será modificado nesta task.

---

## 8. Componentes Impactados

- **Schema `links`** (task 002): será lido/escrito pelo service — deve existir antes desta implementação
- **`src/lib/auth.ts`**: apenas importado, não modificado
- **`src/lib/errors.ts`**: apenas importado, não modificado

---

## 9. Contratos e Interfaces Envolvidos

```typescript
// Copiado de design.md — Seção 7

type CreateLinkInput = {
  url: string;       // URL válida, começa com http:// ou https://
  title?: string;    // Se ausente, sistema tenta buscar automaticamente
  tags?: string[];   // Tags em texto livre; padrão: []
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

// Endpoint — design.md Seção 8
// POST /api/links
// Headers: Authorization: Bearer <jwt>  (obrigatório)
// Request: { url: string, title?: string, tags?: string[] }
// Response 201: LinkRecord
// Erros:
//   400 → URL inválida:          { error: "Invalid URL format" }
//   400 → Título vazio:          { error: "Title is required" }
//   409 → URL duplicada:         { error: "This link already exists in your workspace" }
//   401 → Não autenticado:       { error: "Unauthorized" }
```

---

## 10. Regras de Negócio a Respeitar

| ID | Regra | Condição | Resultado |
|----|-------|----------|-----------|
| RN-001 | Isolamento de workspace | Sempre | `workspaceId` vem da session, nunca do body |
| RN-004 | URL única no workspace | Antes de inserir | Check via query + constraint no schema |
| RN-005 | Título obrigatório | Se title ausente no body | Tentar fetchPageTitle; se falhar e title ainda vazio → 400 |

---

## 11. Passo a Passo Detalhado da Implementação

### Pré-condições
- [ ] Confirmar que `src/db/schema.ts` tem a tabela `links` com constraint `UNIQUE(workspace_id, url)` (task 002)
- [ ] Confirmar que `src/lib/errors.ts` exporta `ValidationError` e `ConflictError`

---

**Passo 1: Criar `src/lib/fetch-title.ts`**

```typescript
export async function fetchPageTitle(url: string): Promise<string | null> {
  try {
    const response = await fetch(url, {
      signal: AbortSignal.timeout(3000),
      headers: { 'User-Agent': 'LinkHub/1.0' }
    })
    if (!response.ok) return null
    const html = await response.text()
    const match = html.match(/<title[^>]*>([^<]+)<\/title>/i)
    return match ? match[1].trim() : null
  } catch {
    return null  // timeout, CORS, erro de rede, qualquer falha → null
  }
}
```

- Timeout: `AbortSignal.timeout(3000)` — 3 segundos
- Seguir redirects automaticamente (comportamento padrão do fetch)
- Qualquer erro → retornar `null`, nunca lançar exceção
- User-Agent para evitar bloqueios simples

---

**Passo 2: Criar `src/modules/links/link.service.ts`**

```typescript
import { db } from '@/db'
import { links } from '@/db/schema'
import { ValidationError, ConflictError } from '@/lib/errors'
import { fetchPageTitle } from '@/lib/fetch-title'
import { eq, and } from 'drizzle-orm'
import type { CreateLinkInput, LinkRecord } from './link.types'

export async function createLink(
  input: CreateLinkInput,
  userId: string,
  workspaceId: string
): Promise<LinkRecord> {
  // 1. Validar URL
  if (!input.url.startsWith('http://') && !input.url.startsWith('https://')) {
    throw new ValidationError('Invalid URL format')
  }

  // 2. Check de duplicata no workspace
  const existing = await db.query.links.findFirst({
    where: and(
      eq(links.url, input.url),
      eq(links.workspaceId, workspaceId)
    )
  })
  if (existing) {
    throw new ConflictError('This link already exists in your workspace')
  }

  // 3. Resolver título
  let title = input.title?.trim() || ''
  if (!title) {
    title = (await fetchPageTitle(input.url)) ?? ''
  }
  if (!title) {
    throw new ValidationError('Title is required')
  }

  // 4. Persistir
  const [link] = await db.insert(links).values({
    url: input.url,
    title,
    tags: input.tags ?? [],
    workspaceId,
    createdById: userId,
  }).returning()

  return link
}
```

---

**Passo 3: Criar `src/modules/links/link.types.ts`**

```typescript
export type { CreateLinkInput, LinkRecord } from '@/types/links'
// Re-exportar os tipos do design para uso local no módulo
```

(Se os tipos do design não existirem em `src/types/links.ts`, defini-los aqui conforme o contrato da seção 9)

---

**Passo 4: Criar `src/app/api/links/route.ts`**

```typescript
import { NextResponse } from 'next/server'
import { getSession } from '@/lib/auth'
import { createLink } from '@/modules/links/link.service'
import { ValidationError, ConflictError } from '@/lib/errors'

export async function POST(req: Request) {
  const session = await getSession()
  if (!session) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 })
  }

  try {
    const body = await req.json()
    const link = await createLink(
      { url: body.url, title: body.title, tags: body.tags },
      session.user.id,
      session.user.workspaceId
    )
    return NextResponse.json(link, { status: 201 })
  } catch (error) {
    if (error instanceof ValidationError) {
      return NextResponse.json({ error: error.message }, { status: 400 })
    }
    if (error instanceof ConflictError) {
      return NextResponse.json({ error: error.message }, { status: 409 })
    }
    console.error('[POST /api/links]', error)
    return NextResponse.json({ error: 'Internal server error' }, { status: 500 })
  }
}
```

---

**Passo 5: Criar `tests/links/create-link.test.ts`**

Implementar os testes da seção 12.

---

## 12. Estratégia de Testes

### 12.1 Testes Unitários

| Função | Caso | Input | Resultado Esperado |
|--------|------|-------|-------------------|
| `fetchPageTitle` | Página com `<title>` | URL mock com `<title>Foo</title>` | `"Foo"` |
| `fetchPageTitle` | Página sem `<title>` | URL mock sem tag title | `null` |
| `fetchPageTitle` | HTTP 404 | URL mock que retorna 404 | `null` |
| `fetchPageTitle` | Timeout | URL que não responde | `null` (em até 3.1s) |
| `createLink` | Happy path | `{ url: "https://a.com", title: "A" }` | LinkRecord com campos completos |
| `createLink` | URL inválida | `{ url: "ftp://a.com" }` | throw `ValidationError("Invalid URL format")` |
| `createLink` | URL duplicada no workspace | URL já existente no mesmo workspaceId | throw `ConflictError("This link already exists in your workspace")` |
| `createLink` | Sem título, fetch falha | `{ url }`, fetch mock retorna null | throw `ValidationError("Title is required")` |
| `createLink` | Sem título, fetch funciona | `{ url }`, fetch mock retorna "Título" | LinkRecord com `title: "Título"` |

### 12.2 Testes de Integração

| Fluxo | Setup | Request | Resultado |
|-------|-------|---------|-----------|
| Criação bem-sucedida | Usuário + workspace no DB | `POST /api/links { url, title, tags }` | 201 + LinkRecord no body |
| URL duplicada | Link com mesma URL já existe no workspace | `POST /api/links { url: existente }` | 409 + `{ error: "This link already exists in your workspace" }` |
| URL em workspace diferente | Link existe em outro workspace | `POST /api/links { url: mesmaUrl }` | 201 (URLs são únicas por workspace) |
| Sem token | Nenhum | `POST /api/links` sem Authorization | 401 + `{ error: "Unauthorized" }` |
| URL inválida | - | `POST /api/links { url: "naoeumaurl" }` | 400 + `{ error: "Invalid URL format" }` |

### 12.3 Validação Manual

1. Fazer login com usuário de teste
2. `POST /api/links` com `{ url: "https://nextjs.org" }` → deve retornar 201 com título preenchido
3. Repetir o mesmo request → deve retornar 409
4. `POST /api/links` com `{ url: "https://httpstat.us/404" }` sem título → deve retornar 400

---

## 13. Critérios de Conclusão

- [ ] `POST /api/links` com URL e título válidos retorna status 201 com LinkRecord completo
- [ ] `POST /api/links` sem título retorna 201 com título buscado automaticamente
- [ ] `POST /api/links` com URL de site inacessível sem título retorna 400 com `{ error: "Title is required" }`
- [ ] `POST /api/links` com URL duplicada no workspace retorna 409
- [ ] `POST /api/links` com URL de outro workspace retorna 201 (unicidade é por workspace)
- [ ] `POST /api/links` sem autenticação retorna 401
- [ ] `fetchPageTitle` nunca lança exceção — retorna `null` em qualquer falha
- [ ] `workspaceId` nos links criados corresponde ao workspace do usuário autenticado
- [ ] Todos os testes em `tests/links/create-link.test.ts` passam

---

## 14. Riscos

| Risco | Origem | Mitigação |
|-------|--------|-----------|
| Schema sem constraint UNIQUE | Research: tabela criada na task 002 | Verificar no início do passo 2; se ausente, está fora do escopo desta task mas deve ser reportado |
| Race condition em check de duplicata | Research: padrão do projeto não usa lock | Constraint UNIQUE no schema é a segunda camada de proteção; ConflictError é lançado se constraint falhar |

---

## 15. Itens que Não Devem Ser Alterados

- `src/lib/auth.ts` — apenas importar `getSession`
- `src/lib/errors.ts` — apenas importar os erros existentes
- `src/db/schema.ts` — apenas ler os tipos, não modificar
- `src/db/index.ts` — apenas importar `db`
- `src/modules/workspaces/` — nenhuma alteração
- Nenhum teste existente

---

## 16. Observações Finais para Execução

- Usar `AbortSignal.timeout(3000)` e não `setTimeout` + AbortController — sintaxe mais limpa e funcional no Node.js 18+
- O padrão do projeto usa `.returning()` no insert — sempre usar para obter o registro criado
- `session.user.workspaceId` foi confirmado no research — usar sem verificação adicional
- Se o schema da task 002 não tiver a constraint UNIQUE: o check em memória ainda funciona, mas reportar ao usuário como observação pós-implementação
