# Modo RESEARCH — rpi-builder

Executar análise profunda da task e do codebase antes de qualquer planejamento.

---

## Objetivo

Produzir um documento factual e analítico que responde: **"o que existe hoje e o que precisa mudar?"**

Não planejar. Não implementar. Apenas entender.

---

## Resolução do ID

O argumento é sempre um número de task (ex: `001`). Antes de qualquer ação:

```
1. Glob .ai/product/tasks/NNN-*.md
2. Match único → usar e confirmar ao usuário o nome completo encontrado
3. Nenhum match → exibir lista de tasks disponíveis e parar
4. Nenhum ID passado → orientar a usar /rpi-builder next
5. Task com Status: done → avisar e aguardar confirmação antes de refazer
```

---

## Pré-requisitos

1. Resolver o ID para o arquivo da task (ver acima)
2. Verificar se research já existe (`.ai/product/research/NNN-*.md`) — se existir, perguntar ao usuário se deve ser refeito
3. Criar pasta `.ai/product/research/` se não existir

---

## Fluxo de Execução

```
1. Ler a task completa
2. Ler .ai/product/prd.md
3. Ler .ai/product/design.md
4. Inspecionar o codebase
5. Compilar o research.md
6. Salvar em .ai/product/research/
7. Atualizar status da task
8. Informar próximo passo
```

---

## Passo 1: Leitura da Task

Ler o arquivo completo da task.

Extrair:
- Objetivo principal
- Escopo (o que faz e o que não faz)
- Arquivos mencionados
- Contratos e tipos referenciados
- Regras de negócio aplicáveis
- Dependências declaradas
- Critérios de conclusão

---

## Passo 2: Leitura do PRD e Design

Ler `.ai/product/prd.md` e `.ai/product/design.md`.

Extrair do PRD:
- Requisitos funcionais relacionados à task
- Regras de negócio que afetam a task
- Critérios de aceite relevantes

Extrair do Design:
- Módulos e responsabilidades relacionados
- Contratos e interfaces mencionados
- Fluxos técnicos que a task implementa
- Estratégia de autenticação/autorização se relevante
- Modelo de dados envolvido

---

## Passo 3: Inspeção do Codebase

Esta é a etapa mais crítica do research.

### O que buscar

**Arquivos relevantes:**
- Arquivos mencionados explicitamente na task
- Arquivos com nomes relacionados ao domínio da task
- Arquivos de configuração relevantes (ex: schema, rotas, config)

**Padrões existentes:**
- Como operações similares já são feitas no projeto
- Estrutura de pastas e convenção de nomes
- Como erros são tratados e retornados
- Como autenticação/autorização é verificada
- Como o banco é acessado (ORM, query builder, SQL direto)
- Como componentes são estruturados (se frontend)
- Como testes são escritos no projeto

**Estado atual:**
- O que já existe e pode ser reaproveitado
- O que existe mas precisará ser modificado
- O que não existe e precisará ser criado

**Dependências reais:**
- Imports e exports relevantes
- Tipos e interfaces que serão usados
- Funções utilitárias disponíveis

### Como inspecionar eficientemente

1. Começar pelos arquivos explicitamente listados na task
2. Para cada arquivo: ler seções relevantes (não o arquivo inteiro se for grande)
3. Usar Grep para encontrar padrões similares: funções, tipos, endpoints existentes
4. Usar Glob para mapear estrutura de pastas relevante
5. Ler arquivos de configuração críticos apenas uma vez

### O que NÃO fazer

- Não ler arquivos irrelevantes para parecer mais completo
- Não listar todos os arquivos do projeto
- Não analisar código que não será tocado
- Não avaliar qualidade geral do código
- Não sugerir refatorações fora do escopo

---

## Passo 4: Compilação do Research

Usar `templates/research-template.md` como estrutura.

**Regras de escrita:**
- Ser factual — descrever o que existe, não o que deveria existir
- Citar arquivos com paths completos e número de linha quando relevante
- Evitar opiniões — guardar para o plan
- Evitar soluções — guardar para o plan
- Ser específico — "o arquivo `src/auth/session.ts` linha 47 exporta a função `getSession`" em vez de "existe código de autenticação"
- Registrar inconsistências encontradas entre task, PRD e design na seção "Lacunas"

---

## Passo 5: Salvar e Atualizar

1. Salvar em `.ai/product/research/NNN-task-name.md`
2. Atualizar o arquivo da task:
   - `Research: done`
   - `Status: in-progress` (se era `pending`)

---

## Passo 6: Confirmar

```
✅ Research salvo em .ai/product/research/001-task-name.md

Próximo passo: /rpi-builder plan 001
```

---

## Checklist de Qualidade do Research

Antes de salvar, verificar:

- [ ] Todos os arquivos da task foram encontrados (ou declarada a ausência)
- [ ] Padrões do projeto foram identificados (não assumidos)
- [ ] Dependências reais foram mapeadas (não a lista da task — a real do código)
- [ ] Estado atual está descrito sem misturar com o estado futuro
- [ ] Riscos e conflitos são baseados em evidências do código, não em hipóteses
- [ ] Seção "Resumo final para planejamento" está pronta para ser lida pelo modo plan
- [ ] Nenhuma sugestão de implementação foi incluída
