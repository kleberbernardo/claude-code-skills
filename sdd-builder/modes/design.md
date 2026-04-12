# Modo DESIGN — SDD Builder

Gerar design técnico completo a partir do PRD.

---

## Objetivo

Traduzir requisitos de produto em arquitetura técnica concreta, sem lacunas, pronta para ser quebrada em tasks executáveis.

---

## Pré-requisitos

Aceita dois cenários de entrada:

**Cenário A — Produto novo (após `spec`):**
- `.ai/product/prd.md` deve existir e estar completo
- `.ai/product/ux.md` é opcional

**Cenário B — Feature incremental (após `feature`):**
- `.ai/product/features/NNN-nome.md` deve existir
- `.ai/product/prd.md` é opcional (lido como contexto se existir)
- `.ai/product/design.md` existente deve ser lido para entender arquitetura atual

Se nenhum dos dois existir: interromper e orientar a executar `spec` ou `feature` primeiro.

---

## Fluxo de Execução

```
1. Identificar o cenário (A ou B) pelos arquivos passados como argumento
2. Ler todos os artefatos disponíveis (prd, feature spec, design existente, ux)
3. Se Cenário B: verificar arquitetura existente para não conflitar
4. Verificar versões da stack (Passo 1c)
5. Estruturar o design seguindo templates/design-template.md
6. Salvar em .ai/product/design.md
7. Confirmar e indicar próximo passo
```

### Regras para Cenário B (feature incremental)

- Ler o `design.md` existente antes de gerar — não conflitar com arquitetura estabelecida
- Reutilizar módulos, entidades e padrões já definidos
- Adicionar apenas o que é novo — não reescrever seções sem alteração
- Marcar claramente o que é novo vs. extensão de existente: `[NOVO]` e `[ESTENDE: módulo X]`
- Se houver conflito com arquitetura existente: registrar em "Itens em Aberto" e apresentar opções

---

## Passo 1: Leitura e Análise do PRD

Ler o PRD completo antes de gerar qualquer conteúdo.

Extrair:
- Todos os requisitos funcionais
- Todas as regras de negócio
- Todos os fluxos principais
- Todas as integrações mencionadas
- Todas as restrições técnicas
- Edge cases documentados

---

## Passo 1b: Leitura do UX Visual (se disponível)

Se `.ai/product/ux.md` existir, extrair:
- Stack de UI sugerida (ex: se há referências a produtos que usam Tailwind, Shadcn, Material)
- Componentes específicos confirmados (tabelas, gráficos, kanban) — afetam escolha de libraries
- Tema (claro/escuro) — afeta estrutura de theming e variáveis CSS
- Densidade — afeta decisões de component library (Shadcn vs Ant Design vs MUI)
- Telas-chave descritas — informam estrutura de rotas e hierarquia de componentes

**Regra:** Usar o `ux.md` para informar decisões de frontend — não inventar estilo sem base nele.  
Se `ux.md` não existir: tomar decisões padrão justificadas e registrar em "Decisões Técnicas".

---

## Passo 1c: Verificação de Versões da Stack (obrigatório)

**Antes de gerar qualquer conteúdo do design**, identificar todos os pacotes e frameworks que serão usados e verificar as versões mais recentes estáveis via `WebSearch`.

### Protocolo de versões

1. Identificar a stack a partir do PRD (seção Restrições Técnicas) e do `ux.md` (se disponível)
2. Para **cada pacote principal**, executar uma busca: `"[pacote] latest stable version [ano atual]"` ou `"[pacote] current release"`
3. Registrar a versão encontrada — usar sempre a **latest stable**, nunca beta/RC a menos que o usuário tenha especificado
4. Se a busca não retornar resultado confiável: registrar `[versão não verificada — confirmar em npmjs.com / pypi / pkg.go.dev]`

### Pacotes que SEMPRE precisam de verificação de versão

| Categoria | Exemplos |
|-----------|---------|
| Framework principal | Next.js, Nuxt, SvelteKit, Remix, FastAPI, NestJS, Laravel |
| Runtime | Node.js LTS, Python, Go, Bun |
| ORM / banco | Prisma, Drizzle, TypeORM, SQLAlchemy |
| Autenticação | NextAuth, Lucia, Supabase Auth, Clerk |
| UI Library | Shadcn/ui, Radix, MUI, Ant Design, Tailwind CSS |
| Infra / deploy | Docker, Terraform, versão do Postgres, Redis |

**Regra:** Nenhuma versão pode ser inventada ou baseada em conhecimento de treinamento do modelo — versões ficam desatualizadas. Sempre verificar.

---

## Passo 2: Mapeamento de Decisões Técnicas

Para cada seção do design, mapear os requisitos correspondentes do PRD:

| Seção do Design | Fontes no PRD | Fontes no UX (se disponível) |
|----------------|--------------|------------------------------|
| Stack | Restrições técnicas (seção 15) | Component library, framework de UI |
| Módulos | Requisitos funcionais (seção 10) + Fluxos (seção 8) | Telas-chave (seção 6 do ux.md) |
| Modelo de Dados | Entidades implícitas nos fluxos e regras de negócio | — |
| Endpoints | Fluxos principais (seção 8) | — |
| Autenticação | Tipos de usuário (seção 3) + Regras de acesso (seção 9) | — |
| Integrações | Restrições e integrações (seção 15) | — |
| Theming/Design System | — | Cores, tipografia, tema (seções 2–3 do ux.md) |
| Componentes Frontend | User Stories (seção 6) | Componentes específicos (seção 5 do ux.md) |

Se houver decisão técnica sem correspondência no PRD: registrar em "Itens em Aberto".

---

## Passo 3: Geração do Design

Usar `templates/design-template.md` como estrutura base.

### Regras por seção:

**Seção 3 — Stack e Dependências**
- Listar **todos** os pacotes com versão — obrigatório, sem exceção
- Versões devem ter sido verificadas no Passo 1c — nunca usar versão de memória
- Formato: `pacote@versão` (ex: `next@15.2.1`, `prisma@6.5.0`)
- Justificar escolhas não óbvias
- Se o usuário especificou a stack no PRD: usar exatamente o que foi especificado — e verificar a versão atual do que foi especificado
- Se não especificou: usar padrões modernos, verificar versão atual e justificar

**Seção 4 — Arquitetura da Solução**
- Diagrama em texto (ASCII ou Mermaid) quando a relação entre componentes for complexa
- Descrever camadas: apresentação, aplicação, domínio, infraestrutura (quando aplicável)

**Seção 5 — Módulos e Responsabilidades**
- Cada módulo deve ter: nome, responsabilidade única, dependências
- Evitar módulos "Utils" ou "Helpers" genéricos — ser específico

**Seção 6 — Modelo de Dados**
- Definir todas as entidades identificadas no PRD
- Campos com tipos, nullability e descrição breve
- Relacionamentos explícitos
- Índices estratégicos

**Seção 7 — Contratos e Interfaces**
- Tipos TypeScript ou equivalente na linguagem da stack
- Contratos de entrada e saída para operações principais

**Seção 8 — Endpoints / Actions / Events**
- Para cada operação: método, path, request body, response, erros possíveis
- Agrupados por módulo/domínio

**Seção 9 — Fluxos Técnicos**
- Traducão técnica dos fluxos do PRD
- Sequência de chamadas, validações, efeitos colaterais

**Seção 10 — Autenticação e Autorização**
- Mecanismo de autenticação (JWT, session, OAuth, etc.)
- Matriz de permissões por role/tipo de usuário
- Endpoints protegidos vs. públicos

**Seção 16 — Trade-offs e Decisões Técnicas**
- Para cada decisão não óbvia: opção escolhida, alternativas consideradas, motivo
- Sem justificativas vagas como "mais fácil" — ser específico

---

## Passo 4: Validação Antes de Salvar

Verificar antes de salvar:

- [ ] Todos os requisitos funcionais do PRD têm correspondência no design
- [ ] Todos os fluxos do PRD têm fluxo técnico correspondente
- [ ] Todos os tipos de usuário têm regras de autorização definidas
- [ ] Modelo de dados cobre todas as entidades dos fluxos
- [ ] Endpoints cobrem todos os fluxos principais
- [ ] Nenhum módulo inventado sem base no PRD
- [ ] Itens em Aberto registram decisões pendentes

---

## Passo 5: Salvar e Confirmar

1. Salvar em `.ai/product/design.md`
2. Exibir confirmação:

**Se `ux.md` foi lido:**
```
✅ Design técnico salvo em .ai/product/design.md

Próximo passo: /sdd-builder tasks @.ai/product/prd.md @.ai/product/design.md @.ai/product/ux.md
```

**Se apenas PRD foi lido:**
```
✅ Design técnico salvo em .ai/product/design.md

Próximo passo: /sdd-builder tasks @.ai/product/prd.md @.ai/product/design.md
```
