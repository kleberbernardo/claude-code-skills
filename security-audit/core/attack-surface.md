# Attack Surface Mapping

Metodologia para mapear toda a superfície de ataque de uma aplicação antes de iniciar a análise detalhada.

---

## O que é Superfície de Ataque

Todo ponto onde um atacante pode tentar entrar, extrair dados ou causar danos. Mapear a superfície de ataque antes de auditar evita pontos cegos.

---

## Categorias de Superfície

### 1. HTTP/API Endpoints

**Mapear:**
```bash
# Todos os arquivos de rota
find . -path "*/routes/*" -o -path "*/api/*" -o -path "*/controllers/*" | grep -E "\.(js|ts|py|go|rb|php)$"

# Definições de endpoint
grep -rn "app\.\(get\|post\|put\|patch\|delete\|all\)\|router\.\|@app\.\|@router\." --include="*.{js,ts,py,go}" -n

# Next.js App Router
find . -path "*/app/api/*" -name "route.ts" -o -name "route.js"
find . -path "*/pages/api/*" -name "*.ts" -o -name "*.js"
```

**Classificar cada endpoint:**
- Autenticação requerida: Sim/Não
- Autorização (quais roles): Admin / User / Public
- Operação: Leitura / Escrita / Delete / Exec
- Dados sensíveis: Sim/Não
- Rate limited: Sim/Não

---

### 2. Forms e Input Points

**Mapear:**
- Formulários HTML (especialmente login, registro, busca, upload)
- Parâmetros de query string
- Parâmetros de URL path (`/users/{id}`)
- Headers customizados (`X-User-ID`, `X-Tenant-ID`)
- Cookies
- Body de requests (JSON, form data, multipart)

**Red flags:**
```bash
# Parâmetros de URL usados diretamente
grep -rn "req\.params\.\|request\.args\.\|params\[" --include="*.{js,ts,py}" | grep -v "parseInt\|parseInt\|sanitize\|escape\|validate"

# Headers de controle
grep -rn "x-forwarded-for\|x-real-ip\|x-user-id\|x-tenant" --include="*.{js,ts,py}" -i
```

---

### 3. File System Interface

**Mapear:**
- Endpoints de upload de arquivo
- Endpoints de download de arquivo
- Leitura de arquivo com path do usuário
- Extração de archives (ZIP, tar)

**Red flags:**
```bash
grep -rn "multer\|formidable\|busboy\|multipart\|upload" --include="*.{js,ts}" -l
grep -rn "open(\|read_file\|send_file\|serve_file" --include="*.py" -n
```

---

### 4. Database Interface

**Mapear:**
- Queries que aceitam input do usuário
- Endpoints com filtros dinâmicos
- Endpoints com ordenação dinâmica
- Endpoints com paginação

**Red flags:**
```bash
# Concatenação em queries
grep -rn "WHERE.*+\|WHERE.*\${\|WHERE.*%" --include="*.{js,ts,py,go}" -n
grep -rn "\.raw\|\.query\|\.execute" --include="*.{js,ts}" -n
```

---

### 5. External Service Interface

**Mapear:**
- Webhooks recebidos (Stripe, GitHub, etc.)
- Callbacks OAuth
- SSRF potential (URL do usuário é acessada pelo servidor)
- Integrações com APIs externas

**Red flags:**
```bash
grep -rn "fetch\|axios\|http\.get\|requests\.get\|urllib" --include="*.{js,ts,py}" | grep -i "req\.\|body\.\|params\.\|url\.\|href"
```

---

### 6. Authentication Surface

**Mapear:**
- Login endpoint
- Register endpoint
- Password reset (request + confirm)
- Email verification
- Social login (OAuth callbacks)
- Token refresh
- Logout

**Verificar:**
- Rate limiting em TODOS esses endpoints
- Proteção contra enumeração (respostas idênticas para usuário inexistente vs senha errada)

---

### 7. Admin Interface

**Mapear:**
- Painel de administração
- Endpoints `/admin/*`, `/dashboard/*`, `/manage/*`
- Endpoints de gerenciamento de usuários
- Endpoints de configuração do sistema
- Endpoints de debug/internos

**Red flags:**
```bash
grep -rn "\"admin\"\|'/admin'\|route.*admin\|path.*admin" --include="*.{js,ts,py,go}" -n
grep -rn "debug\|_debug\|/metrics\|/health\|/status\|/info" --include="*.{js,ts,py,go}" -n
```

---

### 8. Network Interface

**Mapear (via docker-compose ou configurações de infra):**
- Portas expostas
- Serviços acessíveis externamente
- Bancos de dados expostos na internet
- Redis/Memcached sem autenticação

```bash
cat docker-compose*.yml | grep -A 2 "ports:"
grep -rn "EXPOSE\|expose" Dockerfile* -n
```

---

### 9. CI/CD Interface

**Mapear:**
- Triggers de pipeline (push, PR, tag, manual, cron, webhook)
- Secrets utilizados no pipeline
- Ambientes de deploy
- Permissões dos tokens de CI/CD

```bash
find . -path "*/.github/workflows/*" -name "*.yml" -o -name "*.yaml"
find . -name ".gitlab-ci.yml" -o -name "Jenkinsfile" -o -name "*.pipeline"
```

---

### 10. Client-Side Interface

**Mapear:**
- Código JavaScript/TypeScript enviado ao browser
- Source maps (expõem código original)
- Variáveis de ambiente em bundle
- Dados sensíveis em estado inicial (server-side rendering)

```bash
# NEXT_PUBLIC_ vars expostas
grep -rn "NEXT_PUBLIC_\|process\.env\." --include="*.{js,ts,jsx,tsx}" | grep -v "\.test\.\|spec\."
grep -rn "window\.__INITIAL_STATE__\|__NEXT_DATA__" --include="*.{js,ts,jsx,tsx}"
```

---

## Mapa de Superfície — Template

Após varredura, documentar:

```
## Superfície de Ataque — [Nome do Projeto]

### Endpoints Públicos (sem autenticação)
- GET  /api/health          → status do sistema
- POST /api/auth/login      → autenticação
- POST /api/auth/register   → criação de conta
- GET  /api/products        → listagem de produtos

### Endpoints Autenticados (usuário comum)
- GET  /api/users/me        → perfil próprio
- PUT  /api/users/me        → atualizar perfil
- GET  /api/orders          → pedidos do usuário

### Endpoints Admin
- GET  /api/admin/users     → listar todos usuários
- DELETE /api/admin/users/:id → deletar usuário

### Upload de Arquivos
- POST /api/upload/avatar   → imagem de perfil (jpeg, png)

### Webhooks
- POST /api/webhooks/stripe → pagamentos (verificar HMAC)

### Superfície de Alto Risco
1. POST /api/auth/login — brute force, credential stuffing
2. POST /api/upload/avatar — upload malicioso, path traversal
3. GET /api/admin/* — privilege escalation
4. POST /api/webhooks/stripe — replay attack, HMAC bypass
```

---

## Checklist de Completude

Antes de prosseguir com análise detalhada, confirmar que mapeou:

- [ ] Todos os endpoints da aplicação
- [ ] Todos os pontos de input de dados
- [ ] Todos os endpoints sem autenticação
- [ ] Todos os endpoints de admin/privilegiados
- [ ] Interfaces externas (webhooks, OAuth, APIs)
- [ ] Portas de rede expostas
- [ ] Pipelines de CI/CD
- [ ] Superfície client-side (JS bundle, env vars)
