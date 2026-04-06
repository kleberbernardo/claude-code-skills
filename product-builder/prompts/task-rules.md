# Task Rules — Product Builder

Regras para geração e gestão de tasks.

---

## Regra 1: Sempre Separar Frontend e Backend

Nunca misturar em uma task:
- ❌ "Implementar tela de login com endpoint"
- ✅ BE-TASK: "Endpoint POST /api/auth/login"
- ✅ FE-TASK: "Tela de login com formulário e integração"

Por quê: cada camada tem ciclo de desenvolvimento próprio, pode ser trabalhada independentemente, e tem critérios de aceite diferentes.

---

## Regra 2: Tasks Pequenas e Independentes

**Tamanho máximo:** 1-2 dias de trabalho (M/L)  
**Se XL:** dividir em tasks menores

**Independente significa:**
- Pode ser iniciada sem outra task em andamento
- Tem critérios de aceite próprios
- Entrega valor por si só (não é "metade de uma feature")

---

## Regra 3: Toda Task Tem Critérios de Aceite

Antes de executar qualquer task, os ACs devem estar escritos.  
Sem AC → não tem como saber se está pronto.

Mínimo por task:
- 1 AC de happy path
- 1 AC de erro/validação
- 1 AC técnico (tipagem, build, lint)

---

## Regra 4: Dependências Explícitas

Toda task deve listar:
- O que precisa estar pronto antes de ela começar
- O que ela bloqueia após ser concluída

Dependências circulares → sinal de que tasks precisam ser reorganizadas.

---

## Regra 5: Uma Task de QA Por Feature

QA não é task separada por BE/FE.  
É uma task única que valida a feature inteira:
- BE endpoints funcionam
- FE integra corretamente
- Fluxo end-to-end passa
- ACs do PRD satisfeitos

---

## Regra 6: Tasks-Overview Deve Ser Atualizado

Quando task muda de status, atualizar `tasks-overview.md`.

Status válidos: `🔲 Pending | 🔄 In Progress | ✅ Done | ❌ Blocked`

---

## Anti-patterns de Tasks

| Anti-pattern | Problema | Correção |
|-------------|---------|---------|
| "Implementar autenticação" | Escopo enorme e vago | Dividir: migration, endpoints, middleware, tela de login, tela de registro |
| "Melhorar o frontend" | Sem escopo definido | Especificar exatamente o que mudar |
| Task sem AC | Impossível verificar conclusão | Adicionar ACs antes de executar |
| Task com 10 dependências | Bloqueio em cadeia | Reorganizar ordem de features |
| "Arrumar bugs do QA" | Vago demais | Criar task por gap identificado no QA |
