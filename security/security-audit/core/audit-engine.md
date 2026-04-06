# Audit Engine

Motor de análise da security-audit skill. Define heurísticas, regras de decisão e estratégias de varredura.

---

## Princípios do Motor

### 1. Defense in Depth Analysis
Analise cada camada independentemente. Uma vulnerabilidade em camada N não cancela análise de camada N+1. Encadeamento de vulnerabilidades menores pode criar impacto crítico.

### 2. Assume Breach
Sempre analise o que acontece SE uma vulnerabilidade for explorada. Qual é o raio de explosão? O que um atacante consegue após exploração?

### 3. Completeness over Speed
Prefira análise completa a análise rápida. Um falso negativo em produção tem custo maior que tempo de auditoria.

### 4. Context-Aware Risk
O mesmo código tem risco diferente dependendo do contexto:
- Campo de senha em formulário interno vs externo
- API pública vs API interna
- Dados de sessão vs dados de transação financeira

---

## Heurísticas de Detecção

### Secrets e Credenciais

**Alta confiança (confirmar imediatamente):**
```
# Padrões de alta entropia (32+ chars) próximos a palavras-chave
(password|passwd|pwd|secret|key|token|api_key|auth|credential)\s*[=:]\s*['"][A-Za-z0-9+/]{20,}['"]

# Formatos conhecidos de tokens
sk-[a-zA-Z0-9]{48}           # OpenAI API key
AKIA[0-9A-Z]{16}             # AWS Access Key ID
ghp_[a-zA-Z0-9]{36}         # GitHub Personal Access Token
xoxb-[0-9]+-[a-zA-Z0-9]+   # Slack Bot Token
AIza[0-9A-Za-z-_]{35}       # Google API Key
ya29\.[0-9A-Za-z-_]+        # Google OAuth Token

# Connection strings
(mongodb|postgresql|mysql|redis):\/\/[^:]+:[^@]+@
```

**Média confiança (verificar contexto):**
```
# Variáveis com valores que parecem secrets
[A-Z_]+(KEY|TOKEN|SECRET|PASS|PWD)[A-Z_]* = "[^"]+"

# Comentários com credenciais
# password: admin123
// api_key = abc123
```

**Baixa confiança (investigar mais):**
```
# Placeholders que podem ser reais
"your-secret-here"
"change-me"
"replace-with-your-key"
```

### Injeção

**SQL Injection:**
```python
# Concatenação direta (Alta confiança)
f"SELECT * FROM users WHERE id = {user_input}"
"SELECT * FROM users WHERE id = " + req.params.id

# Template strings com dados do usuário
query = f"SELECT {column} FROM {table}"

# Formatação de string em queries
cursor.execute("SELECT * FROM %s WHERE id = %s" % (table, id))
```

**Command Injection:**
```python
# Os mais perigosos
os.system(f"ping {host}")
subprocess.call(f"ls {path}", shell=True)
eval(user_input)
exec(user_input)
__import__(user_module)
```

**NoSQL Injection:**
```javascript
// MongoDB query com input direto
db.users.find({ username: req.body.username })
// Sem sanitização → { username: { $gt: "" } } bypassa autenticação
```

### Autenticação Fraca

```python
# Hashing fraco (Alta confiança)
hashlib.md5(password)
hashlib.sha1(password)
crypt.crypt(password)  # DES — obsoleto

# Sem salt
hashlib.sha256(password.encode()).hexdigest()

# Comparação timing-unsafe
if stored_hash == computed_hash:  # timing attack possível

# Senha em log
logger.info(f"User {user} logged in with password {password}")
```

### JWT Inseguro

```javascript
// Algorithm none (Crítico)
jwt.verify(token, '', { algorithms: ['none'] })

// Aceitar qualquer algoritmo
jwt.verify(token, secret)  // sem especificar algoritmo

// Secret fraco
const JWT_SECRET = 'secret'
const JWT_SECRET = '123456'

// Sem verificar expiração
jwt.decode(token)  // não verifica nada
```

### Path Traversal

```python
# Acesso direto com input do usuário
file_path = f"/uploads/{user_filename}"
open(base_dir + request.args.get('file'))

# Sem normalização
os.path.join(BASE_DIR, user_input)  # ../../etc/passwd possível
```

---

## Estratégias de Varredura

### Fase 1: Reconhecimento Rápido (5-10 min)
1. `glob **/.env*` — arquivos de configuração
2. `glob **/docker-compose*.yml` — infra local
3. `glob **/*.pem **/*.key **/*.p12` — certificados e chaves
4. `grep -r "password\|secret\|token\|api_key" --include="*.{js,ts,py,go,rb}"` — credenciais
5. `cat package.json / requirements.txt / go.mod` — dependências e versões

### Fase 2: Análise de Código (20-40 min)
Varrer sistematicamente por categoria:
- Autenticação → `checks/authentication/`
- Autorização → `checks/authorization/`
- API → `checks/api/`
- Frontend → `checks/frontend/`
- Backend → `checks/backend/`

### Fase 3: Análise de Infraestrutura (10-20 min)
- Docker, docker-compose, Kubernetes manifests
- Arquivos de CI/CD (.github/workflows, .gitlab-ci.yml)
- Configurações de cloud (AWS CDK, Terraform, Pulumi)
- nginx.conf, apache.conf, Caddy

### Fase 4: Análise de Dependências (10-15 min)
- Checar CVEs conhecidos nas versões declaradas
- Supply chain risks
- Packages abandonados ou suspeitos

### Fase 5: Síntese e Relatório (10-20 min)
- Consolidar findings
- Eliminar duplicatas
- Calcular CVSS scores
- Priorizar por impacto e explorabilidade
- Gerar relatório seguindo `reports/report-template.md`

---

## Decisões de Severidade

### Escalar para Critical quando:
- Secret/credencial confirmada e explorável
- RCE (Remote Code Execution) possível
- Auth bypass total
- SQL injection em tabela de usuários/pagamentos
- IDOR dando acesso a dados de outros usuários em produção

### Escalar para High quando:
- JWT sem verificação adequada
- Falta de HTTPS em endpoints de autenticação
- CORS aberto para domínio de atacante
- Privilege escalation possível
- Logs com dados sensíveis (PII, PCI)

### Manter em Medium quando:
- Missing security headers
- Rate limiting ausente (sem evidência de abuso)
- Informações de stack em error messages
- Dependency com CVE de exploração complexa

### Classificar como Low quando:
- Melhorias de hardening sem risco direto
- Comentários com informações não-sensitivas
- Versões desatualizadas sem CVE ativo

### Classificar como Info quando:
- Boas práticas não seguidas sem risco mensurável
- Sugestões de melhoria de código
- Cobertura de auditoria incompleta

---

## Regras de Não-Falso-Negativo

Sempre verificar explicitamente (mesmo se "parece seguro"):

1. Existe algum endpoint sem autenticação? (incluindo debug, health, metrics)
2. Algum parâmetro de query string vai direto para SQL/NoSQL?
3. Existe algum `eval()`, `exec()`, `system()` com input externo?
4. Secrets estão em variáveis de ambiente MAS também hardcoded como fallback?
5. JWT é verificado em TODOS os endpoints protegidos?
6. Rate limiting está em TODOS os endpoints de auth?
7. Uploads aceitam qualquer tipo de arquivo?
8. S3/GCS/Azure Blob tem policy pública?
9. Dockerfile usa `root` sem necessidade?
10. GitHub Actions têm `pull_request_target` sem proteção?

---

## Encadeamento de Vulnerabilidades (Attack Chains)

Documentar sempre que encontrar combinações exploráveis:

**Exemplo 1: GitHub + Secrets + Admin**
```
.env em repositório público
→ contém DATABASE_URL com senha de admin
→ banco sem IP whitelist
→ acesso total ao banco de produção
CVSS: 10.0 Critical
```

**Exemplo 2: IDOR + PII**
```
GET /api/users/{id}/profile sem verificação de ownership
→ id é inteiro sequencial
→ expõe nome, email, CPF de todos os usuários
CVSS: 8.6 High
```

**Exemplo 3: XSS + Cookies sem HttpOnly**
```
XSS em campo de comentário
→ cookies de sessão sem HttpOnly
→ document.cookie exfiltra session token
→ account takeover completo
CVSS: 8.8 High
```
