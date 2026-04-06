# .gitignore — Análise de Proteção

Verificar se o .gitignore protege todos os arquivos sensíveis.

---

## Verificação do .gitignore

```bash
cat .gitignore
# Verificar também
cat .gitignore_global 2>/dev/null
cat ~/.gitignore_global 2>/dev/null
```

---

## Entradas Obrigatórias no .gitignore

### Ambiente e Configuração
```gitignore
# Variáveis de ambiente
.env
.env.local
.env.development.local
.env.test.local
.env.production.local
.env.staging
.env.production
*.env

# Configs com segredos
config/secrets.yml
config/database.yml  # Rails
application-local.properties  # Spring Boot
```

### Chaves e Certificados
```gitignore
# Chaves privadas
*.pem
*.key
*.p12
*.pfx
*.jks
*.keystore
id_rsa
id_ed25519
id_ecdsa
*.ppk  # PuTTY

# Certificados
*.crt  # opcional (cert público é ok)
```

### Credenciais de Cloud
```gitignore
# Google Cloud
credentials.json
service-account*.json
*-credentials.json
gcloud-service-key.json

# AWS
.aws/credentials
aws-credentials.json

# Kubernetes
kubeconfig
kube-config.yaml
*.kubeconfig

# Terraform
*.tfvars
terraform.tfstate
terraform.tfstate.backup
.terraform/
```

### Package Registry
```gitignore
.npmrc         # pode conter NPM_TOKEN
.pypirc        # PyPI tokens
.gem/credentials
```

### Banco de Dados
```gitignore
*.sql          # dumps de banco
*.dump
*.sqlite
*.sqlite3
*.db
/db/*.sqlite3
dump.rdb       # Redis dump
```

### Backup e Temporários
```gitignore
*.bak
*.backup
*.tmp
*.swp
*.orig
*~
```

---

## Verificar Arquivos Já Commitados

O .gitignore não remove arquivos já commitados. Verificar:

```bash
# Listar arquivos trackeados que deveriam estar no .gitignore
git ls-files | grep -E "\.(env|pem|key|p12|pfx|sql|dump|sqlite)" 
git ls-files | grep -E "(credentials|service-account|secrets)\.(json|yml|yaml)"
git ls-files | grep -E "(id_rsa|id_ed25519|\.npmrc|\.pypirc)"
```

**Se encontrado:** O arquivo está no histórico git. Apenas adicionar ao .gitignore não é suficiente — o arquivo precisa ser removido com `git rm --cached` E o histórico precisa ser considerado comprometido.

---

## Checklist

- [ ] `.env` e variações no .gitignore
- [ ] Arquivos de chave (`.pem`, `.key`, `.p12`) no .gitignore
- [ ] Credenciais de cloud no .gitignore
- [ ] Arquivos de banco (`.sql`, `.sqlite`) no .gitignore
- [ ] Nenhum arquivo sensível já commitado
- [ ] .gitignore no repositório (não apenas global)

---

## Red Flags

1. `.gitignore` ausente — qualquer arquivo pode ser commitado por engano
2. `.env` não está no .gitignore
3. Arquivos `.pem` ou `.key` não estão protegidos
4. `.env` ou arquivo de credencial já está trackeado pelo git
