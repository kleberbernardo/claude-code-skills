# Playbook: Resposta a Incidentes de Segurança

Procedimento para responder a incidentes de segurança confirmados.

---

## Níveis de Incidente

| Nível | Critério | Resposta |
|-------|---------|---------|
| P0 — Critical | RCE, acesso total ao banco, dados de clientes expostos | Imediato — 24/7 |
| P1 — High | Auth bypass, IDOR em escala, secrets ativos expostos | < 4 horas |
| P2 — Medium | Vulnerabilidade confirmada sem exploração conhecida | < 48 horas |
| P3 — Low | Vulnerabilidade teórica, baixo impacto | Próximo sprint |

---

## Primeiros 30 Minutos (P0/P1)

```
1. CONFIRMAR — a vulnerabilidade é real? (não falso positivo)
2. ESCALAR — notificar responsáveis técnicos e gestores
3. AVALIAR — o que foi acessado/vazado? Por quanto tempo?
4. CONTER — bloquear o vetor de ataque
5. DOCUMENTAR — registrar timestamps de tudo
```

---

## Contenção por Tipo de Incidente

### Secrets Expostos
Ver `playbooks/secrets-leak.md`

### IDOR / Acesso Não Autorizado

```bash
# 1. Verificar logs de acesso
# Quantas requests? De quais IPs? Em qual período?

# 2. Bloquear temporariamente o endpoint se possível
# feature flag, nginx deny, WAF rule

# 3. Identificar dados acessados
# Quais registros? De quais usuários?

# 4. Notificar usuários afetados (LGPD exige se dados pessoais)
```

### Injeção Ativa (SQL/Command)

```bash
# 1. Verificar logs do banco para queries maliciosas
# 2. Verificar sistema operacional por arquivos/processos suspeitos
# 3. Revogar credenciais de banco comprometidas imediatamente
# 4. Snapshot do sistema para forense
```

---

## Notificação (LGPD)

Se dados pessoais foram expostos:

- **ANPD:** Notificar em até 72 horas após ciência do incidente
- **Titulares:** Notificar "em prazo razoável" se houver risco relevante
- **Informar:** Natureza dos dados, titulares afetados, medidas adotadas

---

## Post-Mortem

Após resolução do incidente:

```markdown
# Post-Mortem — [Nome do Incidente] — [Data]

## Resumo
## Timeline
## Causa Raiz
## Impacto
## O que foi feito
## O que poderia ter prevenido
## Ações preventivas (com owners e datas)
## Lições aprendidas
```

---

## Checklist de Resposta

- [ ] Incidente confirmado e classificado
- [ ] Stakeholders notificados
- [ ] Vetor de ataque contido
- [ ] Logs preservados para forense
- [ ] Secrets comprometidos rotacionados
- [ ] Escopo de dados afetados mapeado
- [ ] Usuários afetados notificados (se aplicável)
- [ ] ANPD notificada (se aplicável — 72h)
- [ ] Correção implementada e testada
- [ ] Post-mortem escrito e distribuído
