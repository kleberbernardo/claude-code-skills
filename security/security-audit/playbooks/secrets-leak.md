# Playbook: Vazamento de Secrets

Procedimento para lidar com secrets encontrados no repositório ou código.

---

## Classificação do Incidente

### Nível 1 — Secret Exposto Publicamente
- Repositório público com .env ou token
- **Ação:** Rotação IMEDIATA (< 1 hora)

### Nível 2 — Secret em Repositório Privado
- Credencial hardcoded em código acessível ao time
- **Ação:** Rotação em < 24h, investigação de uso

### Nível 3 — Secret em Repositório Interno Desconhecido
- Encontrado em análise, sem certeza de acesso externo
- **Ação:** Rotação preventiva em < 48h

---

## Procedimento de Resposta

### Passo 1: Avaliar Escopo
```
□ Qual secret foi exposto? (AWS, Stripe, GitHub, banco, etc.)
□ Quais permissões esse secret tem?
□ Há evidência de uso não autorizado? (logs de acesso, transações suspeitas)
□ Há quanto tempo está exposto?
□ Repositório é público ou privado?
□ Está no histórico git ou apenas em commit recente?
```

### Passo 2: Rotação Imediata

**AWS Keys:**
```bash
# Desabilitar key comprometida
aws iam update-access-key --access-key-id AKIA... --status Inactive

# Criar nova key
aws iam create-access-key --user-name [username]

# Atualizar secret no ambiente
# Testar novo funcionamento
# Deletar key antiga
aws iam delete-access-key --access-key-id AKIA...
```

**Stripe:**
```
Dashboard → Developers → API keys → Roll key
```

**GitHub Personal Access Token:**
```
GitHub Settings → Developer Settings → Personal Access Tokens → Revoke
```

**Banco de dados:**
```sql
ALTER USER app_user WITH PASSWORD 'nova_senha_segura';
-- Atualizar DATABASE_URL em todos os ambientes
```

### Passo 3: Remoção do Código/Histórico

```bash
# Remover do código atual
git rm --cached .env
echo ".env" >> .gitignore
git add .gitignore
git commit -m "Remove .env from tracking"

# Para remover do histórico (use com extremo cuidado)
# Isso reescreve o histórico — apenas se repositório NÃO foi clonado amplamente
git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch .env' \
  --prune-empty --tag-name-filter cat -- --all

# Alternativa moderna (mais segura)
# Usar git-filter-repo ou BFG Repo-Cleaner
```

### Passo 4: Verificar Uso Não Autorizado

**AWS:**
```bash
# Verificar CloudTrail para uso da key comprometida
aws cloudtrail lookup-events --lookup-attributes AttributeKey=AccessKeyId,AttributeValue=AKIA...
```

**Stripe:**
```
Dashboard → Logs → filtrar pelo período de exposição
```

**GitHub:**
```
Verificar settings → Security log
```

### Passo 5: Implementar Prevenção

```bash
# Adicionar pre-commit hook para detecção de secrets
npm install --save-dev @secretlint/secretlint secretlint

# .secretlintrc.json
{
  "rules": [
    { "id": "@secretlint/secretlint-rule-preset-recommend" }
  ]
}

# .husky/pre-commit
npx secretlint "**/*"
```

---

## Checklist de Confirmação

- [ ] Secret identificado e escopo mapeado
- [ ] Secret rotacionado em TODOS os ambientes
- [ ] Código limpo (sem secret hardcoded)
- [ ] .gitignore atualizado
- [ ] Histórico analisado e limpo se necessário
- [ ] Logs de acesso analisados
- [ ] Pre-commit hook implementado
- [ ] Time notificado
- [ ] Incidente documentado

---

## Nunca Fazer

- Apenas deletar o arquivo e commitar — histórico git mantém o secret
- Apenas adicionar ao .gitignore sem verificar se já foi commitado
- Reutilizar o mesmo secret após rotação
- Demorar mais de 24h para rotacionar após descoberta
