# Arquivos Sensíveis no Repositório

Varredura por arquivos que não deveriam estar no repositório ou que requerem revisão especial.

---

## Busca Sistemática

```bash
# Arquivos de configuração com potencial de secrets
find . -name "*.env" -o -name ".env*" | grep -v node_modules
find . -name "*.config.js" -o -name "*.config.ts" | xargs grep -l "password\|secret\|key" 2>/dev/null
find . -name "appsettings*.json" | xargs grep -l "ConnectionStrings\|Password" 2>/dev/null
find . -name "web.config" | xargs grep -l "connectionString\|password" 2>/dev/null

# Dumps e backups de banco
find . \( -name "*.sql" -o -name "*.dump" -o -name "*.bak" \) -not -path "*/node_modules/*"
find . \( -name "*.sqlite" -o -name "*.sqlite3" -o -name "*.db" \) -not -path "*/node_modules/*"

# Chaves criptográficas
find . \( -name "*.pem" -o -name "*.key" -o -name "*.p12" -o -name "*.pfx" -o -name "*.jks" \) -not -path "*/node_modules/*"

# Credenciais de cloud
find . -name "credentials.json" -not -path "*/node_modules/*"
find . -name "service-account*.json" -not -path "*/node_modules/*"
find . -name "*.kubeconfig" -o -name "kubeconfig"
find . -name "terraform.tfvars"

# Arquivos de configuração de servidor
find . -name "nginx.conf" -o -name "httpd.conf" -o -name "apache2.conf"
find . -name "Caddyfile"

# Logs (podem conter dados sensíveis)
find . -name "*.log" -not -path "*/node_modules/*" | head -20

# Arquivos de IDE com possível informação
find . -name ".idea" -type d
find . -name "*.suo" -o -name "*.user"  # Visual Studio
```

---

## Arquivos que Requerem Inspeção Manual

### .env.example
Verificar se contém valores reais (não apenas placeholders):
```bash
cat .env.example 2>/dev/null | grep -v "your_\|example\|placeholder\|<\|changeme\|replace" | grep -E "[=:]\s*['\"]?[a-zA-Z0-9+/]{16,}"
```

### Docker Compose
```bash
# Procurar secrets hardcoded em docker-compose
grep -n "password\|secret\|key\|token" docker-compose*.yml 2>/dev/null | grep -v "REDIS_PASSWORD=\${\|variable\|env_file"
```

### Makefiles e Scripts Shell
```bash
grep -rn "export.*=\|PASSWORD=\|TOKEN=\|SECRET=" --include="Makefile" --include="*.sh" --include="*.bash"
```

### Notebooks Jupyter
```bash
# Notebooks podem ter outputs com dados sensíveis
find . -name "*.ipynb" | xargs grep -l "password\|token\|api_key\|secret" 2>/dev/null
```

---

## Checklist

- [ ] Nenhum dump de banco no repositório
- [ ] Nenhuma chave criptográfica exposta
- [ ] Nenhum arquivo de credencial de cloud
- [ ] Arquivos de log não contêm dados sensíveis
- [ ] `.env.example` usa apenas placeholders
- [ ] Docker Compose não hardcoda credenciais
- [ ] Notebooks Jupyter sem outputs sensíveis
- [ ] Arquivos de configuração de servidor sem credenciais

---

## Red Flags Críticas

1. Dump `.sql` com dados de produção no repositório
2. Arquivo `.sqlite` acessível (pode conter todos os dados)
3. `credentials.json` do Google ou `service-account.json`
4. Terraform `*.tfvars` com credenciais reais
5. Logs com PII, senhas ou tokens
