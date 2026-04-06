# Playbook: Hardening de Infraestrutura

Guia de hardening para Docker, cloud e CI/CD.

---

## Docker Hardening

```dockerfile
# Dockerfile seguro — template
FROM node:20-alpine AS base
WORKDIR /app

FROM base AS builder
COPY package*.json ./
RUN npm ci --only=production
COPY src/ ./src/
RUN npm run build

FROM base AS runner
# Criar usuário não-root
RUN addgroup -g 1001 -S nodejs && adduser -S nextjs -u 1001

# Copiar apenas o necessário
COPY --from=builder --chown=nextjs:nodejs /app/dist ./dist
COPY --from=builder --chown=nextjs:nodejs /app/node_modules ./node_modules

USER nextjs
EXPOSE 3000
CMD ["node", "dist/server.js"]
```

```yaml
# docker-compose seguro
services:
  app:
    build: .
    ports:
      - "3000:3000"  # apenas porta necessária
    environment:
      NODE_ENV: production
      DATABASE_URL: ${DATABASE_URL}  # de variável, não hardcoded
    read_only: true  # filesystem read-only
    tmpfs: /tmp     # apenas /tmp writável
    security_opt:
      - no-new-privileges:true
    cap_drop:
      - ALL
    cap_add:
      - NET_BIND_SERVICE  # apenas se necessário

  db:
    image: postgres:16-alpine
    # SEM ports: expostos ao host
    environment:
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - db_data:/var/lib/postgresql/data
```

---

## AWS Hardening

```bash
# Checklist AWS mínimo

# 1. Habilitar MFA para root e usuários IAM
# 2. Habilitar CloudTrail em todas as regiões
# 3. Habilitar GuardDuty
# 4. Habilitar Security Hub
# 5. S3 Block Public Access no nível da conta
# 6. Habilitar versioning em buckets críticos
# 7. Habilitar encryption at rest (KMS) nos serviços de dados
# 8. Security Groups: sem 0.0.0.0/0 em portas não-web
# 9. RDS: publicly_accessible = false
# 10. IAM: remover access keys não utilizadas há > 90 dias
```

---

## CI/CD Hardening

```yaml
# GitHub Actions — template seguro
name: Deploy

on:
  push:
    branches: [main]

permissions:
  contents: read        # mínimo para checkout
  id-token: write       # para OIDC com AWS/GCP

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production  # requer aprovação manual
    
    steps:
      - uses: actions/checkout@v4  # versão fixada
      
      # Usar OIDC em vez de credentials de longa duração
      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::123456789:role/GitHubActions
          aws-region: us-east-1
      
      - name: Deploy
        run: |
          # Scripts de deploy sem secrets hardcoded
          echo "Deploying..."
```

---

## Checklist de Infra Hardening

- [ ] Dockerfiles não rodam como root
- [ ] Imagens base com versão fixada e scan de CVEs
- [ ] docker-compose sem portas de banco expostas
- [ ] docker-compose sem `privileged: true`
- [ ] docker-compose sem socket do Docker montado
- [ ] Security Groups sem `0.0.0.0/0` em portas sensíveis
- [ ] Bancos de dados não públicos na internet
- [ ] CloudTrail/Audit logs habilitados
- [ ] CI/CD usa OIDC em vez de credenciais de longa duração
- [ ] Actions de terceiros com hash de commit fixado
