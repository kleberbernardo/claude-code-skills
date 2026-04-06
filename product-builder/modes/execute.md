# Modo Execute — Product Builder

Fluxo de execução precisa e disciplinada de uma task.

---

## Objetivo

Implementar exatamente o que está no plano aprovado — nem mais, nem menos. Código de produção, padrões do projeto respeitados.

---

## Pré-requisito

Verificar se o plano existe para a task especificada:

```bash
# Exemplo: /product-builder execute BE-TASK-001
ls .ai/product/04-plans/features/*/BE-TASK-001-plan.md
```

Se não existir:
```
Plano não encontrado para BE-TASK-001.
Execute primeiro: /product-builder plan [feature/BE-TASK-001]
```

---

## Fluxo de Execução

```
1. Ler o plano da task
2. Verificar dependências (tasks que precisam estar prontas)
3. Ler arquivos que serão modificados
4. Executar implementação passo a passo
5. Verificar consistência com padrões do projeto
6. Atualizar status da task
7. Criar log de execução
8. Reportar ao usuário
```

---

## Passo 1: Ler o Plano

Ler o plano completo. Extrair:
- Lista de arquivos a criar/modificar
- Passos de implementação em ordem
- Decisões técnicas já tomadas
- Critérios de aceite técnicos

**Nunca improvisar — seguir o plano.**

Se o plano estiver incompleto ou ambíguo:
```
O plano de [TASK-ID] tem ambiguidade em: [ponto específico]
Recomendo re-gerar o plano com: /product-builder plan [task]
Ou me confirma: [pergunta específica sobre a ambiguidade]?
```

---

## Passo 2: Verificar Dependências

Para cada task listada em "Dependências":
1. Verificar se o log de execução existe em `.ai/product/05-execution/logs/`
2. Verificar se o código esperado existe no projeto

Se dependência não satisfeita:
```
⚠️ [TASK-ID] depende de [DEP-TASK-ID] que ainda não foi executada.
Recomendo executar [DEP-TASK-ID] primeiro.
Quer continuar mesmo assim? (risco de conflito)
```

---

## Passo 3: Ler Arquivos Existentes

Antes de criar ou modificar qualquer arquivo:
1. Ler o arquivo se ele existir
2. Entender o contexto ao redor do código a modificar
3. Identificar importações necessárias
4. Verificar que não vai quebrar código adjacente

---

## Passo 4: Implementar

### Princípios de Implementação

**DO:**
- Seguir o plano exatamente
- Usar os padrões já existentes no projeto
- Reutilizar componentes/utils/hooks existentes
- Tipagem explícita (TypeScript)
- Tratar todos os casos de erro definidos no plano
- Implementar todos os estados (loading, error, empty, success)

**DON'T:**
- Adicionar features não previstas no plano
- Refatorar código não relacionado
- Mudar padrões estabelecidos sem justificar
- Deixar `TODO`, `FIXME`, `console.log` de debug
- Criar novas dependências sem alertar
- Implementar "só uma melhoria rápida" não solicitada

### Ordem de Implementação para Backend
1. Migration/schema de banco (se necessário)
2. Types/interfaces
3. Validação de input (schema)
4. Service/business logic
5. Route/controller
6. Testes (se o projeto tiver)

### Ordem de Implementação para Frontend
1. Types/interfaces
2. Hook de dados/estado
3. Componentes base (do mais simples ao mais complexo)
4. Página/tela que os agrega
5. Integração com rota de navegação

---

## Passo 5: Verificar Consistência

Após implementar, verificar:

```bash
# TypeScript sem erros
npx tsc --noEmit 2>&1 | head -20

# Lint sem erros críticos
npm run lint 2>&1 | head -20

# Build não quebrou (se aplicável)
npm run build 2>&1 | tail -10
```

Se encontrar erros: corrigir antes de reportar como concluído.

---

## Passo 6: Atualizar Status da Task

Atualizar o frontmatter do arquivo da task:
```yaml
status: done
updated: [data atual]
```

---

## Passo 7: Criar Log de Execução

Salvar em `.ai/product/05-execution/logs/[FEAT-ID]-[TASK-ID].log.md`:

```markdown
---
task_id: BE-TASK-001
feature: FEAT-001
date: 2026-04-06
status: done
---

# Log de Execução — BE-TASK-001

## Arquivos Criados
- `app/api/users/route.ts` — endpoint de criação de usuário

## Arquivos Modificados
- `prisma/schema.prisma` — adicionado modelo User

## Decisões Tomadas em Execução
- [Qualquer desvio do plano e motivo]

## Pendências / Alertas
- [Algo que a próxima task deve saber]
```

---

## Passo 8: Reportar ao Usuário

```markdown
## ✅ Execução concluída — [TASK-ID]

**Arquivos criados:**
- `app/api/users/route.ts`
- `lib/validations/user.ts`

**Arquivos modificados:**
- `prisma/schema.prisma`

**Próximos passos:**
- Execute [FE-TASK-001] para implementar o frontend
- Ou valide agora: `/product-builder qa FEAT-001`
```

---

## Tratamento de Situações Especiais

### Se o projeto mudou desde o plano
```
⚠️ O arquivo [X] foi modificado desde que o plano foi gerado.
A mudança pode conflitar com a implementação planejada.
Recomendo re-gerar o plano: /product-builder plan [task]
```

### Se encontrar bug não relacionado
```
ℹ️ Encontrei um bug em [arquivo]: [descrição].
NÃO vou corrigir agora (fora do escopo desta task).
Recomendo criar uma nova task para isso.
```

### Se precisar de dado/config inexistente
```
⚠️ A task requer [X] (ex: variável de ambiente, config) que não existe.
Criando [X] com valor de placeholder.
Você precisará configurar [X] antes de colocar em produção.
```
