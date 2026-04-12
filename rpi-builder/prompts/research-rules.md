# Research Rules — rpi-builder

Como conduzir pesquisa de codebase de forma eficiente e útil para o planejamento.

---

## Princípio Central

**O research existe para informar o plan, não para impressionar.**

Um research que lista 50 arquivos irrelevantes é pior do que um research que identifica com precisão os 5 arquivos que realmente importam.

---

## O que Pesquisar (e o que Não Pesquisar)

### Pesquisar sempre

| O que | Como | Por quê |
|-------|------|---------|
| Arquivos explicitamente mencionados na task | Read diretamente | São a base da implementação |
| Como operações similares já são feitas | Grep por padrões | Garantir consistência |
| Estrutura de pastas relevante | Glob no módulo relacionado | Saber onde criar arquivos |
| Tipos e interfaces existentes relacionados | Grep por type/interface | Evitar duplicação |
| Padrão de tratamento de erro | Grep por throw/catch nos arquivos similares | Seguir convenção |
| Padrão de acesso ao banco | Ler um service existente do projeto | Manter consistência |

### Pesquisar se relevante à task

| O que | Quando |
|-------|--------|
| Configuração de middleware | Task envolve rota protegida |
| Schema do banco | Task envolve persistência |
| Variáveis de ambiente | Task envolve configuração |
| Testes existentes de módulo similar | Task inclui testes |
| Componentes de UI similares | Task envolve frontend |

### Não pesquisar

- Arquivos que não serão tocados
- Histórico de commits
- Toda a base de código genericamente
- Qualidade geral do código
- Oportunidades de refatoração

---

## Estratégia de Inspeção Eficiente

### Ordem de leitura

1. Ler a task completamente (uma vez)
2. Ler PRD e Design — apenas seções citadas na task
3. Ler arquivos explicitamente listados na task
4. Grep por símbolos relevantes: tipos, funções, endpoints similares
5. Ler 1–2 arquivos de módulos similares para capturar padrões
6. Ler arquivos de configuração apenas se a task os impacta

### Usando Grep eficientemente

Para encontrar padrões:
```
Grep: "export (function|const|class|type) [NomeRelacionado]"
Grep: "router\.(get|post|put|delete)\("
Grep: "db\.insert\|db\.query\|db\.update"
Grep: "getServerSession\|requireAuth\|withAuth"
```

Para encontrar uso de um módulo:
```
Grep: "from '[caminho-do-modulo]'"
Grep: "import.*[NomeDoModulo]"
```

### Usando Glob eficientemente

Para mapear estrutura:
```
Glob: "src/modules/[nome-modulo]/**/*"
Glob: "src/routes/**/*.ts"
Glob: "tests/**/*.test.ts"
```

---

## Como Descrever o Estado Atual

### Ser factual

Ruim: "O projeto não tem um bom sistema de autenticação"
Bom: "A autenticação é verificada em `src/middleware/auth.ts` linha 23 via `getServerSession()` do next-auth"

Ruim: "Existe código de validação em algum lugar"
Bom: "Validação de input é feita com zod em `src/lib/validators.ts` — todas as rotas existentes importam de lá"

### Incluir números de linha quando útil

Útil quando: a linha específica será modificada ou é a evidência de um risco.
Não útil quando: descrevendo estrutura geral de um arquivo.

### Distinguir o que existe do que vai existir

- "Existe atualmente: [descrição]" — estado do código hoje
- "Precisará existir: [descrição]" — não vai aqui, vai no plan

---

## Identificando Padrões

Padrão é o que o projeto faz consistentemente. Para identificar:

1. Ler 2–3 exemplos do mesmo tipo de artefato
2. Identificar o que é igual entre eles
3. Registrar como padrão

Exemplo:
```
Padrão identificado: serviços do projeto sempre:
1. Recebem input tipado
2. Validam na primeira linha
3. Fazem operação de banco com try/catch
4. Lançam erros tipados (AppError com código HTTP)
5. Retornam o tipo definido no design
```

---

## Registrando Riscos com Evidência

Risco sem evidência = opinião.
Risco com evidência = informação útil para o plano.

Ruim: "Pode haver problemas de concorrência"
Bom: "O check de duplicata é feito em memória antes do insert (src/links/link.service.ts:45). Sem lock ou constraint de banco, dois requests simultâneos podem passar pelo check e causar duplicata. O schema em `src/db/schema.ts` não tem índice unique para (workspace_id, url)."

---

## Quando o Código Não Segue o Design

Se o código existente contradiz o design.md:

1. Registrar na seção "Lacunas ou Dúvidas Encontradas"
2. Descrever: o que o design diz vs o que o código faz
3. Não decidir qual está certo — deixar para o plan resolver
4. Não ignorar a diferença
