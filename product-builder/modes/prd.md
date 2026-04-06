# Modo PRD — Product Builder

Fluxo completo de geração do Product Requirements Document.

---

## Objetivo

Transformar o MVP aprovado em um PRD detalhado por feature, com regras de negócio, fluxos, estados de UI, validações e critérios de aceite.

---

## Pré-requisito

Verificar se `.ai/product/01-discovery/mvp.md` existe e tem status `approved`.

Se não existir:
```
O PRD requer um MVP aprovado. Execute primeiro: /product-builder mvp
```

---

## Fluxo de Execução

```
1. Ler MVP aprovado
2. Listar features do MVP
3. Para cada feature:
   a. Verificar se já tem PRD
   b. Aprofundar com perguntas se necessário
   c. Gerar feature.md
4. Gerar prd.md consolidado
5. Salvar todos os artefatos
6. Apresentar sumário
```

---

## Passo 1: Ler MVP

```bash
# Verificar
cat .ai/product/01-discovery/mvp.md
```

Extrair:
- Lista de features (nome + descrição curta)
- Jornadas principais
- Mapa de telas

---

## Passo 2: Listar Features

Apresentar ao usuário a lista de features do MVP e confirmar:
```
Vou gerar o PRD para as seguintes features:
1. [Feature 1]
2. [Feature 2]
...

Confirma, ou quer adicionar/remover alguma?
```

---

## Passo 3: Processar Cada Feature

Para cada feature, verificar lacunas usando `prompts/prd-questions.md` e gerar `feature.md` usando `templates/feature-template.md`.

**O que cada feature precisa ter:**

### 3.1 Descrição
- O que faz em 2-3 frases
- Por que existe (valor para o usuário)

### 3.2 Regras de Negócio
- Restrições explícitas
- Quem pode fazer o quê
- Cálculos ou lógicas específicas
- Limites (ex: máximo 10 itens, mínimo R$ 10,00)

### 3.3 Fluxo do Usuário
- Passo a passo da jornada principal
- Pontos de decisão
- Caminhos alternativos

### 3.4 Telas Envolvidas
- Lista de telas que compõem a feature
- Breve descrição do que cada tela contém

### 3.5 Estados de UI
- **Loading:** o que aparece enquanto carrega
- **Success:** estado após ação bem-sucedida
- **Error:** mensagens de erro (por tipo de erro)
- **Empty:** quando não há dados para mostrar
- **Disabled:** quando ação não está disponível

### 3.6 Validações
- Por campo: regra + mensagem de erro
- Validação client-side vs server-side

### 3.7 Mensagens ao Usuário
- Toasts/notificações de sucesso
- Mensagens de erro
- Textos de empty state
- Textos de loading

### 3.8 Dependências
- Features que precisam existir antes
- APIs externas necessárias
- Dados que precisam estar no banco

### 3.9 Critérios de Aceite
- Seguir `core/acceptance-criteria-rules.md`
- Mínimo: happy path + 3 edge cases + estados de UI

---

## Passo 4: Gerar PRD Consolidado

Após todas as features, gerar `prd.md` com:
- Executive summary
- Lista de features com links para cada feature.md
- Mapa de dependências entre features
- Ordem recomendada de implementação
- Glossário (se necessário)

---

## Passo 5: Salvar Artefatos

```
.ai/product/02-prd/prd.md
.ai/product/02-prd/features/FEAT-001-nome/feature.md
.ai/product/02-prd/features/FEAT-002-nome/feature.md
...
```

---

## Passo 6: Apresentar Sumário

```markdown
## PRD Gerado — [Nome do Produto]

**Features documentadas:**
| ID | Nome | Telas | ACs |
|----|------|-------|-----|
| FEAT-001 | Autenticação | 3 | 8 |
| FEAT-002 | Dashboard | 2 | 6 |
| FEAT-003 | Perfil | 2 | 5 |

**Dependências:**
FEAT-002 depende de FEAT-001

**Próximo passo:** `/product-builder tasks`
```

---

## Perguntas de Aprofundamento por Categoria

### Regras de Negócio
- "Quem pode [fazer ação X]? Só o dono, ou outros papéis também?"
- "O que acontece se [X e Y ao mesmo tempo]?"
- "Tem algum limite? Máximo de itens, valor mínimo, prazo?"

### Estados de UI
- "O que aparece enquanto os dados carregam?"
- "E se a lista vier vazia? O usuário vê o quê?"
- "E se der erro de internet? Como o usuário sabe o que fazer?"

### Validações
- "O campo [X] aceita qualquer texto ou tem formato específico?"
- "A validação acontece quando o usuário digita ou só quando envia?"

### Permissões
- "Existe hierarquia de usuários? Quem pode ver o quê?"
- "Um usuário pode ver dados de outro?"

---

## Checklist de Qualidade do PRD

Por feature:
- [ ] Descrição clara e não ambígua
- [ ] Todas as regras de negócio explícitas (não implícitas)
- [ ] Fluxo do usuário completo (passo a passo)
- [ ] 4 estados de UI documentados (loading, success, error, empty)
- [ ] Todas as validações com mensagens
- [ ] Dependências mapeadas
- [ ] Mínimo 5 critérios de aceite (incluindo AC de erro)
- [ ] Sem contradições com outras features
