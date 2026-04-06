# Audit Flow — 16 Passos

Fluxo completo e obrigatório de auditoria de segurança. Cada passo deve ser executado em sequência. Resultados de passos anteriores alimentam análise dos posteriores.

---

## Passo 1: Detecção de Stack

**Objetivo:** Entender o que está sendo auditado antes de qualquer análise.

**Ações:**
```bash
# Linguagens e frameworks
ls *.json *.toml *.yaml *.yml *.lock 2>/dev/null
cat package.json 2>/dev/null          # Node.js
cat requirements.txt setup.py 2>/dev/null  # Python
cat go.mod 2>/dev/null                 # Go
cat Gemfile 2>/dev/null                # Ruby
cat composer.json 2>/dev/null          # PHP
cat pom.xml build.gradle 2>/dev/null   # Java

# Framework específico
grep -r "express\|fastify\|nextjs\|nuxt\|django\|flask\|fastapi\|rails\|spring\|gin\|echo" package.json requirements.txt 2>/dev/null

# Banco de dados
grep -r "prisma\|sequelize\|typeorm\|mongoose\|sqlalchemy\|activerecord\|gorm" . --include="*.{json,toml,py,js,ts,go,rb}" -l
```

**Registrar:**
- Linguagem(ns)
- Framework(s)
- ORM / Query builder
- Banco de dados
- Cloud provider
- Containerização
- CI/CD platform

---

## Passo 2: Mapeamento de Arquitetura

**Objetivo:** Entender a estrutura do sistema antes de auditar componentes individuais.

**Ações:**
```bash
# Estrutura geral
find . -type f -name "*.md" | head -20  # documentação
ls -la                                    # arquivos raiz
cat docker-compose*.yml 2>/dev/null       # serviços
cat README.md 2>/dev/null | head -100     # overview

# Endpoints
grep -r "router\.\|app\.\|@app\.\|@router\." --include="*.{js,ts,py,go}" -n
grep -r "route\|endpoint\|path\|url" --include="*.{js,ts,py,go}" -l
```

**Mapear:**
- Serviços e suas responsabilidades
- Comunicação entre serviços
- Endpoints públicos vs internos
- Fluxo de dados (entrada → processamento → saída)
- Dados sensíveis e onde trafegam

---

## Passo 3: Mapeamento de Superfície de Ataque

Consultar `core/attack-surface.md` para metodologia completa.

**Ações:**
- Listar todos os endpoints com método HTTP
- Identificar endpoints sem autenticação
- Identificar endpoints que aceitam uploads
- Identificar endpoints que executam ações sensíveis (pagamento, admin, deletar dados)
- Identificar interfaces externas (webhooks, callbacks, OAuth redirects)

---

## Passo 4: Arquivos Sensíveis

Consultar `checks/repository/` para checklists detalhados.

**Ações:**
```bash
# .env e variações
find . -name ".env*" -type f
find . -name "*.env" -type f

# Chaves e certificados
find . -name "*.pem" -o -name "*.key" -o -name "*.p12" -o -name "*.pfx" -o -name "*.jks"

# Backups e dumps
find . -name "*.sql" -o -name "*.dump" -o -name "*.bak" -o -name "*.backup"
find . -name "*.db" -o -name "*.sqlite" -o -name "*.sqlite3"

# Config com credenciais
find . -name "*.config.js" -o -name "config.yaml" -o -name "secrets.yaml"
find . -name "credentials.json" -o -name "service-account*.json"

# .gitignore — verificar o que não está protegido
cat .gitignore
```

---

## Passo 5: Padrões Inseguros no Código

Consultar `core/audit-engine.md` para heurísticas completas.

**Busca sistemática:**
```bash
# Secrets hardcoded
grep -rn "password\s*=\s*['\"][^'\"]\+" --include="*.{js,ts,py,go,rb,php,java}"
grep -rn "api_key\s*=\s*['\"]" --include="*.{js,ts,py,go,rb,php,java}"
grep -rn "secret\s*=\s*['\"]" --include="*.{js,ts,py,go,rb,php,java}"

# Funções perigosas
grep -rn "eval\|exec\|system\|popen\|shell_exec" --include="*.{js,ts,py,go,rb,php}"
grep -rn "innerHTML\s*=" --include="*.{js,ts,jsx,tsx}"  # XSS
grep -rn "dangerouslySetInnerHTML" --include="*.{jsx,tsx}"  # React XSS

# Logs com dados sensíveis
grep -rn "console\.log\|logger\.\|print(" --include="*.{js,ts,py}" -A 1 | grep -i "password\|token\|secret\|credit\|ssn\|cpf"
```

---

## Passo 6: Revisão de Autenticação

Consultar `checks/authentication/` para checklists detalhados.

**Verificar:**
- [ ] Hashing de senhas: bcrypt/argon2/scrypt (não MD5/SHA1/SHA256 simples)
- [ ] Politica de senhas: comprimento mínimo, complexidade
- [ ] Proteção contra brute force: rate limit, lockout, CAPTCHA
- [ ] Tokens JWT: algoritmo, secret, expiração, validação
- [ ] OAuth: state parameter, PKCE, redirect_uri validation
- [ ] Sessions: ID aleatório, regenerar após login, expiração
- [ ] MFA: disponibilidade e enforcement para contas privilegiadas
- [ ] Reset de senha: token seguro, expiração, uso único

---

## Passo 7: Revisão de Autorização

Consultar `checks/authorization/` para checklists detalhados.

**Verificar:**
- [ ] Verificação de propriedade em cada operação (IDOR)
- [ ] RBAC consistente em todos os endpoints
- [ ] Multi-tenant isolation (RLS, tenant_id em queries)
- [ ] Privilege escalation impossível
- [ ] Endpoints admin protegidos adequadamente
- [ ] Soft-delete não expõe dados deletados

---

## Passo 8: Revisão de API

Consultar `checks/api/` para checklists detalhados.

**Verificar:**
- [ ] Validação de input em todos os parâmetros
- [ ] Rate limiting em endpoints críticos
- [ ] CORS configurado corretamente
- [ ] Sem injeção (SQL, NoSQL, Command, LDAP)
- [ ] Content-Type validation
- [ ] Sem mass assignment / over-posting
- [ ] GraphQL: depth limiting, introspection em prod
- [ ] Sem endpoints de debug/admin expostos

---

## Passo 9: Revisão de Frontend

Consultar `checks/frontend/` para checklists detalhados.

**Verificar:**
- [ ] Sem secrets no código JavaScript/bundle
- [ ] Sem dados sensíveis em localStorage/sessionStorage
- [ ] XSS: sanitização de output, CSP
- [ ] CSRF: tokens em forms, SameSite cookies
- [ ] Postman/Swagger UI exposto em produção
- [ ] Source maps expondo código fonte em produção
- [ ] Sem chaves privadas em arquivos estáticos

---

## Passo 10: Revisão de Backend

Consultar `checks/backend/` para checklists detalhados.

**Verificar:**
- [ ] Lógica de negócio não bypassável via API
- [ ] Validação server-side (não confiar só no frontend)
- [ ] Error handling não vaza stack traces
- [ ] Sem debug mode em produção
- [ ] Logs não contêm PII/PCI/PHI
- [ ] Race conditions em operações críticas (pagamentos, inventário)
- [ ] File upload: tipo, tamanho, nome sanitizado

---

## Passo 11: Revisão de Banco de Dados

Consultar `checks/database/` para checklists detalhados.

**Verificar:**
- [ ] Sem SQL injection (queries parametrizadas)
- [ ] Permissões mínimas para usuário da aplicação
- [ ] Dados sensíveis criptografados at rest
- [ ] RLS (Row Level Security) onde aplicável
- [ ] Backups criptografados
- [ ] Conexão via TLS
- [ ] Sem credenciais no connection string em código

---

## Passo 12: Revisão de Storage

Consultar `checks/storage/` para checklists detalhados.

**Verificar:**
- [ ] Buckets S3/GCS/Azure Blob não são públicos (a menos que intencional)
- [ ] Acesso via URLs presignadas com expiração curta
- [ ] Sem PII em buckets sem criptografia
- [ ] CORS em buckets configurado corretamente
- [ ] Versionamento e proteção contra exclusão

---

## Passo 13: Revisão de Infraestrutura

Consultar `checks/infrastructure/` para checklists detalhados.

**Verificar:**
- [ ] Dockerfile: não roda como root, imagem base atualizada
- [ ] Docker-compose: sem ports desnecessários expostos
- [ ] Nginx/Apache: headers de segurança, SSL configuração
- [ ] Cloud: IAM roles com least privilege
- [ ] Secrets em cloud secrets manager (não env vars de container)
- [ ] Network segmentation (VPC, security groups)
- [ ] Firewall: apenas portas necessárias abertas

---

## Passo 14: Revisão de CI/CD

Consultar `checks/ci-cd/` para checklists detalhados.

**Verificar:**
- [ ] Secrets em CI/CD secrets store (não em código)
- [ ] GitHub Actions: sem `pull_request_target` inseguro
- [ ] Sem `--privileged` em containers de build
- [ ] Dependency review em PRs
- [ ] SAST/DAST no pipeline
- [ ] Sem deploy automático para produção sem aprovação
- [ ] Tokens de deploy com escopo mínimo

---

## Passo 15: Revisão de Dependências

Consultar `checks/dependencies/` para checklists detalhados.

**Verificar:**
- [ ] CVEs conhecidos em dependências declaradas
- [ ] Dependências com versões fixas (não `*` ou `latest`)
- [ ] Packages abandonados ou com baixo mantenimento
- [ ] Typosquatting em nomes de packages
- [ ] Integridade de packages (checksums)
- [ ] Lock files commitados

---

## Passo 16: Relatório Final

Consultar `reports/report-template.md` para formato completo.

**Ações:**
1. Consolidar todos os findings dos passos 1-15
2. Eliminar duplicatas e falsos positivos óbvios
3. Calcular severidade e confiança de cada finding
4. Ordenar por: severidade DESC, confiança DESC, impacto DESC
5. Gerar executive summary com top 5 riscos
6. Criar roadmap de remediação priorizado
7. Incluir métricas: total por severidade, coverage de cada área

---

## Adaptação por Modo

### Modo `quick` (passos executados)
4, 5 (partial), 16

### Modo `auth`
1, 2, 6, 7, 16

### Modo `secrets`
1, 4, 5 (secrets only), 14 (secrets only), 16

### Modo `infra`
1, 2, 12, 13, 14, 16

### Modo `data`
1, 2, 11, 12, 16

### Modo `deep`
Todos os 16 passos
