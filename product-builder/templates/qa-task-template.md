---
id: QA-TASK-001
feature: FEAT-001
title: QA — [Nome da Feature]
type: qa
status: draft
created: [DATA]
depends_on: [BE-TASK-001, FE-TASK-001]
estimate: S
---

# QA-TASK-001 — QA: [Nome da Feature]

## Escopo

Tasks a validar:
- [BE-TASK-001]: [Título]
- [FE-TASK-001]: [Título]

Critérios de aceite fonte:
- `.ai/product/02-prd/features/FEAT-001/feature.md`
- Tasks individuais listadas acima

## Checklist de Testes

### Funcional
- [ ] Happy path completo executado
- [ ] Todas as validações de input testadas
- [ ] Todos os cenários de erro cobertos
- [ ] Permissões e autorização verificadas
- [ ] Edge cases do PRD verificados

### UI/UX
- [ ] Estado de loading presente (não tela em branco)
- [ ] Estado de erro com mensagem útil
- [ ] Empty state implementado com CTA
- [ ] Feedback após ações (toast/mensagem)
- [ ] Formulário valida antes de submit
- [ ] Botão submit desabilitado durante loading

### Responsividade
- [ ] Mobile 375px: layout legível, botões clicáveis
- [ ] Tablet 768px: layout não quebra
- [ ] Desktop 1280px: layout completo

### Integração
- [ ] Frontend chama endpoints corretos
- [ ] Erros do backend são exibidos corretamente
- [ ] Dados persistem após ação (refetch ou update local)
- [ ] Loading aparece durante chamadas de API

### Qualidade de Código
- [ ] Sem console.log de debug
- [ ] Sem TypeScript errors
- [ ] Padrões do projeto respeitados

## Critérios de Aceite (do PRD)

Copiar ACs relevantes da feature.md:
- [ ] [P0] AC-001: [Descrição]
- [ ] [P0] AC-002: [Descrição]
- [ ] [P1] AC-003: [Descrição]

## Bloqueadores Conhecidos

[Listar dependências não resolvidas antes de iniciar QA]
