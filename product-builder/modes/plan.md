# Modo Plan — Product Builder

Fluxo completo de planejamento técnico para uma task específica.

---

## Objetivo

Analisar a task, explorar o projeto real, e gerar um plano técnico detalhado com passos de implementação, decisões justificadas e critérios de aceite técnicos.

---

## Pré-requisito

Verificar se a task especificada existe em `.ai/product/03-tasks/`.

```bash
# Exemplo: /product-builder plan FEAT-001/BE-TASK-001
ls .ai/product/03-tasks/features/FEAT-001/backend/BE-TASK-001.md
```

Se não existir:
```
Task não encontrada. Execute primeiro: /product-builder tasks
```

---

## Fluxo de Execução

```
1. Ler a task
2. Explorar o projeto real (codebase)
3. Detectar stack e padrões existentes
4. Gerar plano específico por tipo (BE ou FE)
5. Definir critérios de aceite técnicos
6. Salvar plano
7. Apresentar resumo
```

---

## Passo 1: Ler a Task

Ler o arquivo da task e extrair:
- Escopo exato
- Dependências (tasks que precisam estar prontas)
- Critérios de aceite do produto

---

## Passo 2: Explorar o Projeto

**Nunca planejar sem explorar o projeto.**

```bash
# Estrutura geral
ls -la
cat package.json | grep -E "dependencies|scripts"
ls app/ src/ pages/ lib/ utils/ 2>/dev/null

# Stack
grep -r "prisma\|drizzle\|mongoose\|sequelize" package.json
grep -r "next\|express\|fastify\|hono" package.json
grep -r "react-query\|swr\|zustand\|redux" package.json

# Padrões existentes
# Encontrar exemplos de: routes, components, hooks, services similares
```

Para task de **backend**, analisar:
- Estrutura de routes/controllers existentes
- Como validação é feita (zod? yup? manual?)
- Como erros são retornados (formato padrão?)
- ORM e como queries são feitas
- Middleware de auth existente
- Variáveis de ambiente em uso

Para task de **frontend**, analisar:
- Estrutura de componentes existentes
- Como páginas são organizadas
- Como API é chamada (fetch? axios? react-query?)
- Como estado é gerenciado (useState? Zustand? Context?)
- Design system / componentes base disponíveis
- Como loading/error é tratado nos exemplos existentes
- Como formulários são feitos

---

## Passo 3: Identificar Padrões

Documentar no plano:
- Padrões que DEVEM ser seguidos (para consistência)
- Anti-patterns que DEVEM ser evitados
- Código existente que pode ser reutilizado

---

## Plano de Backend

Ver `planning/backend-planning.md` para checklist detalhado.

Estrutura mínima do plano de backend:

```markdown
## Plano Backend: [BE-TASK-001] — [Título]

### 1. Análise do Projeto
- Stack: [Next.js API Routes / Express / etc.]
- ORM: [Prisma / Drizzle / etc.]
- Auth: [como auth funciona]
- Padrão de erro: [formato padrão de resposta de erro]

### 2. Arquivos a Criar/Modificar
| Arquivo | Ação | Descrição |
|---------|------|-----------|
| app/api/[path]/route.ts | Criar | Endpoint principal |
| lib/validations/[name].ts | Criar | Schema de validação |
| prisma/schema.prisma | Modificar | Nova tabela/campo |

### 3. Schema de Banco de Dados (se aplicável)
[Definição dos modelos]

### 4. Endpoints a Implementar
#### POST /api/[path]
- Auth requerida: Sim/Não
- Request body: [schema]
- Response 200: [schema]
- Response 400: [lista de erros]
- Response 401: token inválido
- Response 403: sem permissão
- Response 404: [quando]
- Response 500: erro interno

### 5. Lógica de Negócio
Passo a passo da lógica:
1. Validar input
2. Verificar permissões
3. [Lógica específica]
4. Persistir
5. Retornar resposta

### 6. Tratamento de Erros
[Mapa de erro → resposta]

### 7. Decisões Técnicas
[O que foi decidido e por quê]

### 8. Critérios de Aceite Técnicos
[ACs no formato Given/When/Then]
```

---

## Plano de Frontend

Ver `planning/frontend-planning.md` para checklist detalhado.

Estrutura mínima do plano de frontend:

```markdown
## Plano Frontend: [FE-TASK-001] — [Título]

### 1. Análise do Projeto
- Framework: [Next.js 15 / React / etc.]
- Estado: [useState / Zustand / etc.]
- Chamadas API: [fetch / axios / react-query]
- Componentes base disponíveis: [lista]

### 2. Arquivos a Criar/Modificar
| Arquivo | Ação | Descrição |
|---------|------|-----------|
| app/[path]/page.tsx | Criar | Tela principal |
| components/[Name].tsx | Criar | Componente X |
| hooks/use[Name].ts | Criar | Hook de estado |

### 3. Estrutura de Componentes
[Hierarquia visual]
Page
  └── Container
        ├── Header
        ├── [ComponenteA]
        │     └── [SubComponente]
        └── [ComponenteB]

### 4. Estado e Dados
- Estado local: [o que usar useState]
- Estado global: [o que compartilhar]
- Cache: [o que cachear]

### 5. Chamadas de API
| Ação | Método | Endpoint | Quando |
|------|--------|----------|--------|
| Carregar | GET | /api/X | Montagem |
| Criar | POST | /api/X | Submit |

### 6. Tratamento de Estados de UI
- Loading: [o que renderizar]
- Error: [mensagem + ação]
- Empty: [copy + CTA]
- Success: [feedback para usuário]

### 7. Formulários e Validação
[Por campo: regra + mensagem]

### 8. Navegação
- Rota de entrada: [path]
- Após ação X: [redirecionar para Y]
- Botão voltar: [para onde]

### 9. Responsividade
- Mobile: [mudanças de layout]
- Tablet: [mudanças de layout]

### 10. Critérios de Aceite Técnicos
[ACs de UI no formato Given/When/Then]
```

---

## Passo 6: Salvar Plano

```
.ai/product/04-plans/features/FEAT-XXX/[TASK-ID]-plan.md
```

---

## Passo 7: Apresentar Resumo

```markdown
## Plano Gerado — [TASK-ID]

**Tipo:** Backend / Frontend
**Arquivos afetados:** X criar, Y modificar

**Principais decisões:**
- [Decisão 1 e motivo]
- [Decisão 2 e motivo]

**Dependências identificadas:**
- [O que deve existir antes de iniciar]

**Pronto para executar:** `/product-builder execute [TASK-ID]`
```

---

## Alertas Durante o Planning

Se encontrar conflito entre a task e o projeto:
```
⚠️ Conflito identificado: [descrição]
A task pede [X], mas o projeto usa [Y].
Recomendação: [ajuste sugerido]
Quer ajustar o plano ou a task?
```

Se task depender de outra não completa:
```
⚠️ Dependência não satisfeita: [TASK-ID] ainda não foi executada.
Recomendo executar [TASK-ID] primeiro.
```
