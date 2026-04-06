# Exemplo: Quick Audit

Demonstração de uma auditoria rápida (`/security-audit quick`).

---

## Invocação

```
/security-audit quick
```

## O que faz

Executa apenas os passos mais críticos:
- Passo 4: Arquivos sensíveis
- Passo 5 (parcial): Secrets e padrões críticos
- Relatório simplificado

## Saída Esperada

```markdown
# QUICK SECURITY SCAN — MyApp

Duração: ~5 minutos | Modo: quick | Data: 2024-01-15

## Findings Imediatos

### 🔴 CRITICAL: .env File Committed
- Arquivo `.env` encontrado no repositório (rastreado pelo git)
- Contém: DATABASE_URL, STRIPE_SECRET_KEY (sk_live_***...***xyz), JWT_SECRET
- Ação: Rotação imediata de todos os secrets. Ver playbooks/secrets-leak.md

### 🔴 CRITICAL: Stripe Live Key Hardcoded
- Arquivo: `src/services/payment.ts:12`
- Padrão: sk_live_***...***abc
- Ação: Rotação imediata + mover para variável de ambiente

### ✅ Nenhum dump de banco encontrado
### ✅ Nenhuma chave privada (.pem/.key) no repositório

## Próximos Passos
Para análise completa: `/security-audit deep`
```
