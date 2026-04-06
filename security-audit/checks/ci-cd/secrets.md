# Secrets em CI/CD

Verificação de como secrets são gerenciados nos pipelines de CI/CD.

---

## Busca por Secrets Expostos

```bash
# GitHub Actions
grep -rn "password:\|secret:\|token:\|key:" --include="*.yml" .github/ -n | grep -v "\${{.*secrets\." | grep -v "#"

# GitLab CI
grep -rn "password:\|secret:\|token:\|key:" .gitlab-ci.yml 2>/dev/null | grep -v "\$" | grep -v "#"

# Jenkinsfile
grep -rn "password\|secret\|token\|key" Jenkinsfile 2>/dev/null | grep -v "credentials(\|withCredentials("
```

---

## Padrões Corretos vs Incorretos

```yaml
# INCORRETO — secret hardcoded
env:
  DATABASE_URL: postgresql://admin:minha_senha@db:5432/prod

# CORRETO — via secrets do GitHub
env:
  DATABASE_URL: ${{ secrets.DATABASE_URL }}

# CORRETO — via environment específico
env:
  DATABASE_URL: ${{ secrets.PROD_DATABASE_URL }}  # escopo restrito
```

---

## Isolamento de Environments

```bash
# Verificar se há separação de secrets por environment
cat .github/workflows/*.yml | grep "environment:\|env:\|secrets\." | head -30
```

**Boa prática:** Secrets de produção devem estar apenas em environment `production` com proteção (require approval).

---

## Checklist

- [ ] Nenhum secret hardcoded em arquivos de CI/CD
- [ ] Todos os secrets via `${{ secrets.NAME }}` (GitHub) ou variáveis de ambiente do CI
- [ ] Secrets de produção separados de staging/dev
- [ ] Environment de produção com required reviewers
- [ ] Secrets com escopos mínimos (não usar mesmo token para tudo)
- [ ] Rotação de secrets documentada

---

## Red Flags

1. Senha ou token hardcoded em `.yml` de CI/CD
2. Mesmo secret de produção usado em PRs de forks
3. Secrets de produção sem environment protection
4. Token de deploy com acesso a todos os ambientes
