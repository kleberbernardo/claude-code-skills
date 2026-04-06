# Modo QA — Product Builder

Fluxo completo de validação de qualidade de uma feature ou task.

---

## Objetivo

Verificar se a implementação satisfaz os critérios de aceite definidos no PRD e nas tasks. Gerar relatório com status claro: passou, falhou, parcial.

---

## Pré-requisito

Verificar se há implementação a validar:
- Tasks de execução com status `done` em `.ai/product/05-execution/logs/`
- Código implementado no projeto

---

## Fluxo de Execução

```
1. Ler critérios de aceite (PRD + tasks)
2. Explorar implementação
3. Verificar cada AC
4. Identificar gaps
5. Gerar relatório
6. Atualizar status
7. Reportar ao usuário
```

---

## Passo 1: Ler Critérios de Aceite

Fontes de verdade (em ordem de prioridade):
1. `QA-TASK-NNN.md` — ACs da task de QA
2. `feature.md` do PRD — ACs do produto
3. `[BE|FE]-TASK-NNN-plan.md` — ACs técnicos dos planos

---

## Passo 2: Explorar a Implementação

### Para verificar backend
```bash
# Ler rotas implementadas
cat app/api/[path]/route.ts
# Ler schema/validação
cat lib/validations/[name].ts
# Ler schema de banco
cat prisma/schema.prisma
```

### Para verificar frontend
```bash
# Ler componentes
cat app/[path]/page.tsx
cat components/[Name].tsx
# Verificar estados implementados
grep -n "isLoading\|isError\|isEmpty\|error\|loading" app/[path]/page.tsx
```

---

## Passo 3: Verificar Cada AC

Para cada critério de aceite, verificar:

| Status | Significado |
|--------|-------------|
| ✅ Passou | Implementado e funcionando conforme especificado |
| ❌ Falhou | Implementado mas incorreto, ou não implementado |
| ⚠️ Parcial | Implementado mas incompleto ou com ressalvas |
| 🔲 Não verificável | Requer teste dinâmico (não análise estática) |

### Categorias de Verificação

**Funcional (análise estática)**
- Lógica de negócio implementada corretamente?
- Validações de input presentes?
- Todos os endpoints implementados?
- Erros tratados?

**Frontend (análise de código)**
- Estado de loading implementado?
- Estado de erro com mensagem?
- Estado empty implementado?
- Formulário valida antes de submit?
- Campos obrigatórios marcados?

**Segurança (análise de código)**
- Auth verificada nos endpoints?
- Input sanitizado?
- Dados sensíveis não expostos?

**Tipagem**
- TypeScript sem `any` injustificado?
- Interfaces definidas para dados de API?

---

## Passo 4: Identificar Gaps

Um "gap" é algo que:
- Estava nos ACs e não foi implementado
- Foi implementado incorretamente
- Pode causar bug em produção

Classificar gaps:
- **Bloqueante (P0):** impede o uso da feature → deve ser corrigido antes de marcar como done
- **Importante (P1):** degrada experiência significativamente → corrigir em breve
- **Melhoria (P2):** não crítico → pode ficar no backlog

---

## Passo 5: Gerar Relatório

Salvar em `.ai/product/06-qa/reports/[FEAT-ID]-qa-report.md`:

```markdown
---
feature_id: FEAT-001
date: 2026-04-06
status: passed | failed | partial
tasks_validated: [BE-TASK-001, FE-TASK-001]
---

# QA Report — FEAT-001: [Nome da Feature]

## Resultado Geral
**Status: [PASSOU / FALHOU / PARCIAL]**

| AC ID | Descrição | Status |
|-------|-----------|--------|
| AC-001 | Login com credenciais válidas | ✅ Passou |
| AC-002 | Mensagem de erro em senha errada | ❌ Falhou |
| AC-003 | Loading durante submit | ✅ Passou |
| AC-004 | Responsividade mobile | ⚠️ Parcial |

## Gaps Encontrados

### 🔴 Bloqueantes (P0)
#### GAP-001: [Título]
**AC:** [qual AC falhou]
**Problema:** [o que está errado]
**Evidência:** `[arquivo:linha]`
**Correção sugerida:** [como corrigir]

### 🟡 Importantes (P1)
#### GAP-002: [Título]
[mesmo formato]

### 🔵 Melhorias (P2)
#### GAP-003: [Título]
[mesmo formato]

## Resumo
- ACs verificados: X
- Passaram: X
- Falharam: X
- Parciais: X
- Não verificáveis: X

## Próximos Passos
[Se falhou]: Corrigir GAP-001, GAP-002 e re-executar QA
[Se passou]: Feature pronta. Próxima feature: [FEAT-002]
```

---

## Passo 6: Atualizar Status

**Se PASSOU:** 
- Atualizar feature no PRD: `status: done`
- Atualizar tasks: `status: done`
- Atualizar tasks-overview.md

**Se FALHOU:**
- Manter feature como `in-progress`
- Listar tasks que precisam ser corrigidas
- Sugerir: re-executar após correções

---

## Passo 7: Reportar ao Usuário

```markdown
## QA Concluído — FEAT-001: [Nome]

**Resultado: ✅ PASSOU** (ou ❌ FALHOU / ⚠️ PARCIAL)

**Critérios verificados:** X/Y passaram

**Gaps encontrados:**
| Prioridade | Gap | AC |
|-----------|-----|-----|
| 🔴 P0 | Mensagem de erro ausente | AC-002 |
| 🟡 P1 | Layout quebrado em 375px | AC-004 |

**[Se falhou]:** Corrija os gaps P0 e re-execute: `/product-builder qa FEAT-001`
**[Se passou]:** Próxima feature: `/product-builder plan FEAT-002/BE-TASK-001`
```

---

## Checklist de QA Completo

### Funcional
- [ ] Todos os ACs do PRD verificados
- [ ] Happy path funciona
- [ ] Todos os cenários de erro cobertos
- [ ] Validações de input corretas

### Frontend
- [ ] Loading implementado
- [ ] Error state implementado com mensagem útil
- [ ] Empty state implementado
- [ ] Formulários validam antes de submit
- [ ] Responsivo em mobile (375px) e tablet (768px)
- [ ] Sem console.error em operações normais

### Backend
- [ ] Todos os endpoints retornam schemas corretos
- [ ] Erros retornam formato padrão do projeto
- [ ] Auth verificada onde necessário
- [ ] Validação de input presente

### Código
- [ ] Sem TypeScript errors
- [ ] Sem console.log de debug
- [ ] Sem TODO/FIXME não intencionais
- [ ] Padrões do projeto seguidos
