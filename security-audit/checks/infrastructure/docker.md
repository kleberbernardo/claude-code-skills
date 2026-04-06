# Docker e Containers — Segurança

Configurações inseguras de containers criam riscos significativos.

---

## Análise de Dockerfile

```bash
find . -name "Dockerfile*" -not -path "*/node_modules/*"
cat Dockerfile
```

### Checklist de Dockerfile

```dockerfile
# 1. USAR imagem base específica (não latest)
FROM node:20-alpine          # BOM — versão fixada
FROM node:latest             # RUIM — versão não controlada
FROM node:20                 # ACEITÁVEL — mas prefira -alpine

# 2. NÃO RODAR COMO ROOT
USER node              # BOM
# (sem USER) = root    # RUIM

# 3. USAR multi-stage build para não incluir dev deps em produção
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
USER node
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules

# 4. NÃO COPIAR TUDO COM WILDCARDS
COPY . .                    # RUIM — copia .env, .git, etc.
COPY src/ package*.json ./  # MELHOR — específico

# 5. TER .dockerignore adequado
```

```bash
# Verificar user no Dockerfile
grep -n "USER" Dockerfile* | grep -v "root"
grep -n "USER" Dockerfile* || echo "ALERTA: sem diretiva USER — roda como root"

# Verificar imagens base
grep -n "^FROM" Dockerfile* | grep "latest\b"

# Verificar se copia .env
grep -n "COPY \." Dockerfile*  # pode copiar .env
cat .dockerignore 2>/dev/null | grep ".env"
```

---

## docker-compose.yml

```bash
# Portas expostas desnecessariamente
grep -A 2 "ports:" docker-compose*.yml

# Volumes perigosos
grep -n "socket.sock\|/var/run/docker.sock\|/proc\|/sys\|/:" docker-compose*.yml

# Privileged containers
grep -n "privileged.*true\|--privileged" docker-compose*.yml

# Secrets no environment
grep -A 20 "environment:" docker-compose*.yml | grep -E "PASSWORD|SECRET|TOKEN|KEY" | grep -v "\${"
```

**Red flags:**
```yaml
# CRÍTICO — socket do Docker montado (escape de container trivial)
volumes:
  - /var/run/docker.sock:/var/run/docker.sock

# CRÍTICO — container privilegiado
privileged: true

# RUIM — porta do banco exposta ao host (e potencialmente internet)
ports:
  - "5432:5432"  # deveria ser apenas interno

# RUIM — secret hardcoded
environment:
  DATABASE_PASSWORD: minha_senha_secreta
```

**Correto:**
```yaml
environment:
  DATABASE_PASSWORD: ${DATABASE_PASSWORD}  # vem do .env ou do sistema
# ou usar secrets nativos do Docker/Compose
secrets:
  db_password:
    file: ./secrets/db_password.txt
```

---

## Imagens Base

```bash
# Verificar CVEs em imagens base
# Usar ferramentas como trivy, snyk, ou docker scan
# (sugerir ao usuário rodar)
echo "Sugestão: rodar 'trivy image <image_name>' para verificar CVEs na imagem base"
```

---

## Checklist

- [ ] Dockerfile tem `USER` (não roda como root)
- [ ] Imagem base com versão fixada (não `latest`)
- [ ] `.dockerignore` protege `.env`, `.git`, etc.
- [ ] Docker socket não montado em containers de aplicação
- [ ] Nenhum container `privileged: true` sem justificativa
- [ ] Portas de banco não expostas ao host
- [ ] Secrets via variáveis de ambiente, não hardcoded no Compose
- [ ] Multi-stage build para remover dependências de dev

---

## Red Flags

1. Sem diretiva `USER` — roda como root
2. `/var/run/docker.sock` montado — escape de container
3. `privileged: true` — acesso completo ao host
4. `FROM node:latest` — versão não controlada
5. Senha hardcoded em `docker-compose.yml`
6. Porta 5432/3306/27017 exposta (`"5432:5432"`)
