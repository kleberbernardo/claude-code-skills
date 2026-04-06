# Secrets no Repositório

Detecção de credenciais, tokens e segredos hardcoded no código-fonte.

---

## Por que é crítico

Repositórios — mesmo privados — são comprometidos. Funcionários saem. Credenciais em código ficam para sempre no histórico git. Um único `.env` commitado pode expor todo o sistema de produção.

**Caso real:** Em 2022, um desenvolvedor da Toyota commitou credenciais de API da AWS em repositório público no GitHub por 5 anos. Dados de 296.000 clientes foram expostos.

---

## Padrões de Alta Confiança

### Tokens de serviços conhecidos

```regex
# AWS
AKIA[0-9A-Z]{16}                    # AWS Access Key ID
[0-9a-zA-Z/+]{40}                   # AWS Secret Access Key (40 chars)

# Google
AIza[0-9A-Za-z-_]{35}               # Google API Key
ya29\.[0-9A-Za-z-_]+               # Google OAuth Access Token
[0-9]+-[a-z0-9]+\.apps\.googleusercontent\.com  # Client ID

# GitHub
ghp_[a-zA-Z0-9]{36}                # GitHub Personal Access Token
ghs_[a-zA-Z0-9]{36}                # GitHub App Token
github_pat_[a-zA-Z0-9_]{82}        # Fine-grained PAT

# Slack
xoxb-[0-9]+-[0-9A-Za-z-]+          # Bot Token
xoxs-[0-9]+-[0-9A-Za-z-]+          # User Session Token

# Stripe
sk_live_[0-9a-zA-Z]{24,}           # Live Secret Key (CRITICAL)
sk_test_[0-9a-zA-Z]{24,}           # Test Secret Key (High)
rk_live_[0-9a-zA-Z]{24,}           # Restricted Key Live

# OpenAI
sk-[a-zA-Z0-9]{48}                  # API Key
sk-proj-[a-zA-Z0-9-_]{50,}          # Project Key

# Twilio
AC[a-zA-Z0-9]{32}                   # Account SID
SK[a-zA-Z0-9]{32}                   # API Key SID

# SendGrid
SG\.[a-zA-Z0-9_-]{22}\.[a-zA-Z0-9_-]{43}  # API Key

# JWT tokens (já assinados com dados)
eyJ[a-zA-Z0-9_-]+\.[a-zA-Z0-9_-]+\.[a-zA-Z0-9_-]+

# Private Keys
-----BEGIN (RSA |EC |OPENSSH |DSA |PGP )?PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
```

### Credenciais em código

```bash
# Busca de alta prioridade
grep -rn "password\s*[=:]\s*['\"][^'\"]\+" --include="*.{js,ts,py,go,rb,php,java,cs}"
grep -rn "api_key\s*[=:]\s*['\"]" --include="*.{js,ts,py,go,rb,php}"
grep -rn "secret\s*[=:]\s*['\"][^'\"]\+" --include="*.{js,ts,py,go}"
grep -rn "token\s*[=:]\s*['\"][^'\"]\+" --include="*.{js,ts,py,go}"

# Connection strings com credenciais
grep -rn "mongodb://[^'\"]*:[^@]*@" --include="*.{js,ts,py,go}"
grep -rn "postgresql://[^'\"]*:[^@]*@" --include="*.{js,ts,py,go}"
grep -rn "mysql://[^'\"]*:[^@]*@" --include="*.{js,ts,py,go}"
grep -rn "redis://:[^@]*@" --include="*.{js,ts,py,go}"
grep -rn "amqp://[^'\"]*:[^@]*@" --include="*.{js,ts,py,go}"
```

---

## Padrões de Média Confiança

```bash
# Variáveis com nomes suspeitos e valores não-vazios
grep -rn "[A-Z_]*(KEY|TOKEN|SECRET|PASSWORD|PASSWD|PWD|AUTH|CREDENTIAL)[A-Z_]*\s*=\s*['\"][^'\"]" --include="*.{js,ts,py,go,rb}"

# URLs com autenticação básica
grep -rn "https\?://[^:\"']*:[^@\"']*@" --include="*.{js,ts,py,go}"

# Comentários com credenciais
grep -rn "# password:\|// password:\|<!-- password\|# api.key:\|// api.key:" --include="*.{js,ts,py,go,html}"
```

---

## Verificação no Histórico Git

**Mesmo que removido do código atual, pode estar no histórico:**

```bash
# Buscar no histórico git (requer permissão do usuário)
git log --all --full-history -S "password" -- "*.env" 2>/dev/null
git log --all --full-history -S "AKIA" 2>/dev/null  # AWS keys
git log --all --full-history --oneline -- "*.env" ".env*" 2>/dev/null | head -20
```

**Se encontrado no histórico:**
- Considerar credencial comprometida mesmo se removida
- Recomendar rotação imediata de todos os secrets do histórico
- Guiar para rewrite de histórico (apenas se necessário e com cuidado)

---

## Arquivos Mais Perigosos

```bash
# Arquivos que NUNCA deveriam estar commitados
find . -name ".env" -o -name ".env.local" -o -name ".env.production"
find . -name "*.pem" -o -name "*.key" -o -name "*.p12" -o -name "*.pfx"
find . -name "credentials.json"        # Google service account
find . -name "service-account*.json"   # GCP
find . -name "*.keystore"              # Android/Java
find . -name "id_rsa" -o -name "id_ed25519"  # SSH keys
find . -name "kubeconfig" -o -name "kube-config.yaml"  # Kubernetes
find . -name ".npmrc" -o -name ".pypirc"  # Package registry tokens
find . -name "terraform.tfvars"         # Terraform com valores

# Verificar .gitignore (ver checks/repository/gitignore.md)
```

---

## Checklist de Verificação

- [ ] Nenhum `.env` commitado (nem `.env.example` com valores reais)
- [ ] Nenhuma chave privada (`.pem`, `.key`, `.p12`) no repositório
- [ ] Nenhum `credentials.json` ou `service-account.json`
- [ ] Nenhuma connection string com usuário/senha no código
- [ ] Nenhum token de API hardcoded
- [ ] Nenhum JWT assinado hardcoded (mesmo de teste)
- [ ] Histórico git não contém secrets (verificar se possível)
- [ ] `.gitignore` cobre todos os arquivos sensíveis

---

## Red Flags Imediatas (escalar para Critical)

1. `sk_live_` (Stripe live key) encontrada no código
2. `AKIA` (AWS Access Key) encontrada no código
3. Arquivo `.env` com variáveis populadas commitado
4. Chave privada RSA/EC no repositório
5. `credentials.json` do Google no repositório
6. Connection string com senha de banco de produção

---

## Resposta ao Encontrar Secret

**Se encontrado:**

1. **NÃO expor o valor completo** — mascarar: `sk-***...***abc`
2. **Classificar como Critical** com confidence High
3. **Recomendar rotação imediata** como ação #1
4. **Verificar escopo do secret** — o que esse token pode fazer?
5. **Verificar se está em produção** — dano potencial maior
6. **Verificar histórico git** — pode estar em commits anteriores
7. **Recomendar implementação de secret scanning** no CI/CD

---

## Boas Práticas

### Gestão de Secrets
```bash
# .env.example sem valores reais
DATABASE_URL=postgresql://user:password@host:5432/db
STRIPE_SECRET_KEY=sk_test_your_key_here
JWT_SECRET=your-jwt-secret-here

# .gitignore protegendo .env
echo ".env" >> .gitignore
echo ".env.local" >> .gitignore
echo ".env.*.local" >> .gitignore
```

### Ferramentas de Secret Scanning
- **gitleaks** — scan de repositório local e histórico
- **truffleHog** — análise de entropia em git
- **GitHub Secret Scanning** — nativo no GitHub (gratuito)
- **GitGuardian** — monitoramento em tempo real

### Onde Guardar Secrets
- **Desenvolvimento:** `.env` local (no .gitignore)
- **CI/CD:** Secrets do GitHub Actions / GitLab CI
- **Produção:** AWS Secrets Manager, GCP Secret Manager, Azure Key Vault, HashiCorp Vault
