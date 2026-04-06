# Scoring — Pontuação de Risco

Sistema de pontuação para priorização e comunicação de risco.

---

## Score de Risco do Projeto

Calcular com base nos findings:

```
Score = (Critical × 10) + (High × 5) + (Medium × 2) + (Low × 0.5)
```

| Score | Postura | Ação Recomendada |
|-------|---------|-----------------|
| 0 | Excelente | Manutenção preventiva |
| 1-5 | Boa | Corrigir lows no backlog |
| 6-15 | Razoável | Sprint dedicado de segurança |
| 16-30 | Precária | Parar novos features, focar em segurança |
| 31+ | Crítica | Considerar rollback/hotfix imediato |

---

## Priorização de Issues

### Matriz Exploitability × Impact

```
         │ Impacto Baixo │ Impacto Médio │ Impacto Alto
---------│---------------│---------------│--------------
Fácil    │    Medium     │     High      │   Critical
---------│---------------│---------------│--------------
Médio    │     Low       │    Medium     │     High
---------│---------------│---------------│--------------
Difícil  │     Info      │     Low       │    Medium
```

---

## SLA de Remediação Recomendado

| Severidade | SLA |
|-----------|-----|
| Critical | 24-48 horas |
| High | 5-10 dias úteis |
| Medium | 30 dias |
| Low | Próximo quarter |
| Info | Sem SLA |

---

## Tracking de Remediação

Para cada issue, acompanhar:
- **Status:** Open / In Progress / Resolved / Accepted Risk / Won't Fix
- **Owner:** Responsável pela correção
- **Due Date:** Baseado no SLA acima
- **Verification:** Como confirmar que foi corrigido

---

## Métricas de Segurança

Incluir no relatório final:

- **Mean Time to Remediate (MTTR):** Por severidade
- **Vulnerability Density:** Número de vulnerabilidades por 1000 linhas de código
- **Coverage:** % das áreas auditadas
- **Age:** Idade estimada de cada vulnerabilidade
