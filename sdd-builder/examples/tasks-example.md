# Exemplo de Task — SDD Builder

Este exemplo ilustra o nível de detalhe esperado em uma task individual.

**Produto:** LinkHub (do exemplo de PRD)
**Task:** 003 — Implementar salvamento de link

---

# Task 003 — Implementar endpoint de criação de link

## 1. Objetivo

Criar o endpoint `POST /api/links` que valida, persiste e retorna um novo link associado ao workspace do usuário autenticado.

---

## 2. Contexto

Esta task implementa o fluxo "Salvar um link" (PRD seção 6, Fluxo 1) e os requisitos RF-006 a RF-008. Corresponde ao módulo `LinkService` definido no Design Técnico seção 5.

O endpoint é o núcleo do produto — sem ele, nenhum valor é entregue. A lógica central é: validar URL, checar duplicata no workspace (RN-004), buscar título automaticamente (RF-006) e persistir.

**Decisão de design que afeta esta task:** O auto-fetch do título é feito server-side para evitar CORS. Se o fetch falhar, o link é salvo assim mesmo com o título fornecido pelo usuário (degradação graciosa — EC-001).

---

## 3. Dependências

| Task | Motivo |
|------|--------|
| 001 - Setup do projeto | Estrutura de pastas, configuração do banco |
| 002 - Schema de dados | Tabela `links` deve existir no banco |

---

## 4. Escopo da Tarefa

Esta task inclui:
- [ ] Função `fetchPageTitle(url: string): Promise<string | null>` para buscar título via HTTP server-side
- [ ] Service `createLink(input, userId, workspaceId)` com todas as validações
- [ ] Route Handler `POST /api/links` com autenticação obrigatória
- [ ] Teste unitário do service
- [ ] Teste de integração do endpoint

---

## 5. Fora de Escopo

- Auto-completar tags (feature futura)
- Adicionar link a coleção (Task 006)
- Edição ou deleção de links (Tasks 004 e 005)
- Preview com imagem ou metadados além do título

---

## 6. Arquivos Envolvidos

| Arquivo | Status | Descrição |
|---------|--------|-----------|
| `src/lib/fetch-title.ts` | [novo] | Função de auto-fetch do título da página |
| `src/modules/links/link.service.ts` | [novo] | Lógica de criação de link |
| `src/app/api/links/route.ts` | [novo] | Route Handler Next.js |
| `tests/links/create-link.test.ts` | [novo] | Testes unitários e integração |

---

## 7. Componentes Impactados

- **Módulo de autenticação:** Endpoint usa `getServerSession()` para obter `userId` e `workspaceId`
- **Schema `links`:** Inserção na tabela definida na Task 002

---

## 8. Contratos e Regras Relevantes

### Tipos (copiado de design.md — Seção 7)

```typescript
type CreateLinkInput = {
  url: string;        // URL válida, começando com http:// ou https://
  title?: string;     // Se vazio, sistema busca automaticamente
  tags?: string[];    // Lista de tags em texto livre, pode ser vazia
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

### Endpoint (copiado de design.md — Seção 8)

```
POST /api/links

Headers:
  Authorization: Bearer <jwt>  (obrigatório)

Request body:
  { url: string, title?: string, tags?: string[] }

Response 201:
  { id, url, title, tags, workspaceId, createdById, createdAt }

Erros:
  400 → URL inválida: { error: "Invalid URL format" }
  400 → Título vazio (sem auto-fetch): { error: "Title is required" }
  409 → URL duplicada: { error: "This link already exists in your workspace" }
  401 → Não autenticado: { error: "Unauthorized" }
```

### Regras de Negócio Aplicáveis

- **RN-001:** Link é associado ao workspace do usuário autenticado — nunca ao workspace de outro usuário
- **RN-004:** URL deve ser única dentro do workspace — verificar antes de inserir
- **RN-005:** Título não pode ser vazio — tentar auto-fetch; se falhar e usuário não forneceu: retornar 400

---

## 9. Passos de Implementação Sugeridos

1. Criar `src/lib/fetch-title.ts` com função:
   ```
   fetchPageTitle(url: string): Promise<string | null>
   ```
   - Fazer GET na URL com timeout de 3 segundos
   - Parsear HTML e extrair conteúdo da tag `<title>`
   - Retornar `null` em qualquer erro (timeout, 4xx, 5xx, parse error)
   - Nunca lançar exceção — sempre retornar null em falha

2. Criar `src/modules/links/link.service.ts` com função:
   ```
   createLink(input: CreateLinkInput, userId: string, workspaceId: string): Promise<LinkRecord>
   ```
   - Validar que URL começa com `http://` ou `https://` — throw `ValidationError` se inválida
   - Checar duplicata: `db.query.links.findFirst({ where: and(eq(links.url, input.url), eq(links.workspaceId, workspaceId)) })`
   - Se duplicata encontrada: throw `ConflictError('This link already exists in your workspace')`
   - Se título não fornecido: chamar `fetchPageTitle(url)` — timeout máximo 3s
   - Se título ainda vazio após fetch: throw `ValidationError('Title is required')`
   - Inserir: `db.insert(links).values({ url, title, tags: tags ?? [], workspaceId, createdById: userId }).returning()`
   - Retornar o registro inserido

3. Criar `src/app/api/links/route.ts`:
   - Verificar autenticação com `getServerSession()` — retornar 401 se não autenticado
   - Extrair `userId` e `workspaceId` da sessão
   - Parsear body com `await req.json()`
   - Chamar `createLink(input, userId, workspaceId)`
   - Mapear erros: `ValidationError` → 400, `ConflictError` → 409
   - Retornar `NextResponse.json(link, { status: 201 })` em sucesso

---

## 10. Critérios de Conclusão

- [ ] `POST /api/links` com URL válida e título manual retorna status 201 com todos os campos do LinkRecord
- [ ] `POST /api/links` com URL válida sem título retorna status 201 com título buscado automaticamente
- [ ] `POST /api/links` com URL inacessível sem título fornecido retorna status 400 com `{ error: "Title is required" }`
- [ ] `POST /api/links` com URL já existente no workspace retorna status 409
- [ ] `POST /api/links` com URL de outro workspace retorna 201 (URLs são únicas por workspace, não global)
- [ ] `POST /api/links` sem autenticação retorna status 401
- [ ] `fetchPageTitle` não lança exceção — retorna null em qualquer falha
- [ ] Todos os testes em `tests/links/create-link.test.ts` passam

---

## 11. Testes Obrigatórios

### 11.1 Testes Unitários

| Função | Caso | Entrada | Resultado Esperado |
|--------|------|---------|-------------------|
| `fetchPageTitle` | URL válida com `<title>` | `"https://example.com"` (mock) | `"Example Domain"` |
| `fetchPageTitle` | URL retorna 404 | URL mock que retorna 404 | `null` |
| `fetchPageTitle` | Timeout | URL que não responde em 3s | `null` |
| `createLink` | Happy path com título manual | `{ url: "https://a.com", title: "A" }` | LinkRecord completo |
| `createLink` | URL duplicada no workspace | URL já existente no workspace | throw `ConflictError` |
| `createLink` | URL inválida | `"not-a-url"` | throw `ValidationError` |

### 11.2 Testes de Integração

| Fluxo | Setup | Request | Resultado Esperado |
|-------|-------|---------|-------------------|
| Criação bem sucedida | Usuário + workspace no DB | `POST /api/links { url, title, tags }` | 201 + LinkRecord no body + registro no DB |
| URL duplicada | Link já existe no workspace | `POST /api/links` com mesma URL | 409 + `{ error: "This link already exists in your workspace" }` |
| Não autenticado | Nenhum | `POST /api/links` sem token | 401 |

### 11.3 Validação Manual

1. Fazer login com usuário de teste
2. Na tela de links, clicar em "Novo link"
3. Colar `https://nextjs.org` no campo URL
4. Aguardar — título deve ser preenchido automaticamente em < 3s
5. Clicar em "Salvar" — link deve aparecer no topo da lista
6. Tentar salvar o mesmo link novamente — mensagem "Este link já foi salvo neste workspace" deve aparecer

---

## 12. Edge Cases

| Cenário | Condição | Tratamento |
|---------|----------|------------|
| EC-001 | URL existe mas página não tem `<title>` | `fetchPageTitle` retorna null; campo título fica para o usuário preencher; sem título → 400 |
| EC-001b | URL com redirect (301/302) | `fetchPageTitle` deve seguir redirects (máx 3 hops) e buscar título da página final |
| RN-004 | Constraint de unicidade no banco | Se race condition bypassar o check em memória, constraint `UNIQUE(workspace_id, url)` no banco garante integridade e deve ser convertida em ConflictError |

---

## 13. Riscos

| Risco | Probabilidade | Impacto | Mitigação |
|-------|--------------|---------|-----------|
| Sites que bloqueiam fetch server-side | Alta | Baixo | Degradação graciosa — campo manual sempre disponível |
| Race condition em criação simultânea da mesma URL | Baixa | Baixo | Constraint de banco como segunda camada de proteção |

---

## 14. Observações para Execução

- O timeout de 3s no `fetchPageTitle` é intencional — não aumentar para não degradar UX no salvamento
- A constraint `UNIQUE(workspace_id, url)` deve já existir no schema da Task 002 — verificar antes de executar
- Usar `try/catch` amplo no `fetchPageTitle` — qualquer exceção deve virar `null`, nunca propagar
