# Exemplo: Secrets Audit

Auditoria focada em secrets, credenciais e arquivos sensíveis.

---

## Invocação

```
/security-audit secrets
```

## O que Verifica

- Arquivos .env no repositório e histórico
- Tokens hardcoded (AWS, Stripe, OpenAI, GitHub, etc.)
- Connection strings com credenciais
- Chaves privadas e certificados
- Credenciais em docker-compose e CI/CD
- .gitignore adequado

## Exemplo de Saída

```markdown
# SECRETS AUDIT — MyProject

## 🔴 CRITICAL: 2 issues encontrados

### Stripe Live Key em Código
- Arquivo: src/lib/stripe.ts:5
- Padrão: sk_live_***...***Kxz (mascarado)
- Status: KEY ATIVA (não rotacionada)
- Ação IMEDIATA: Revogar no Stripe Dashboard → criar nova

### .env Commitado com Valores Reais
- git ls-files mostra .env trackeado
- Contém: DATABASE_URL com senha, JWT_SECRET="dev-secret-123"
- Ação: git rm --cached .env + rotação + ver playbooks/secrets-leak.md

## 🟠 HIGH: 1 issue

### Docker Compose com Senha Hardcoded
- Arquivo: docker-compose.yml:18
- POSTGRES_PASSWORD: admin123
- Ação: Mover para variável de ambiente

## ✅ Verificado sem Issues
- Nenhuma AWS key (AKIA...) encontrada
- Nenhum dump de banco (.sql, .sqlite) encontrado
- Nenhuma chave privada (.pem, .key) encontrada
- GitHub Actions usa ${{ secrets.* }} corretamente

## .gitignore — Status
- ✅ .env está protegido
- ✅ *.pem está protegido
- ⚠️ *.sql NÃO está no .gitignore — recomendado adicionar
```
