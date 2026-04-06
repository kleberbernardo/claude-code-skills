# CI/CD Pipelines — Segurança Geral

Pipelines inseguros podem ser usados para injetar código malicioso em produção.

---

## Verificações Gerais

```bash
# Localizar arquivos de CI/CD
find . -path "*/.github/workflows/*" -name "*.yml"
find . -name ".gitlab-ci.yml" -o -name "Jenkinsfile" -o -name ".circleci/config.yml"
find . -name "*.pipeline" -o -name "buildspec.yml"  # CodePipeline/CodeBuild
```

---

## Riscos por Plataforma

### GitHub Actions
Ver `checks/ci-cd/github-actions.md` para detalhes.

### GitLab CI
```bash
# Verificar se há scripts perigosos
grep -rn "curl.*|\|wget.*|\|bash.*-c" .gitlab-ci.yml -n

# Artifacts sem proteção
grep -rn "artifacts:" .gitlab-ci.yml -A 5 | grep "public.*true\|expose_as:"
```

### Jenkins
```bash
# Groovy script injection
grep -rn "evaluate\|load\|readFile" Jenkinsfile -n | grep "params\.\|env\."
```

---

## Deploy de Produção

```bash
# Verificar se há aprovação antes de deploy em produção
grep -rn "environment.*production\|deploy.*prod\|environment:\s*prod" --include="*.yml" .github/ -n -A 5
```

**Boas práticas:**
- Deploy automático apenas para dev/staging
- Deploy de produção requer aprovação manual
- Deployment keys com escopo mínimo e revogáveis

---

## Checklist

- [ ] Pipeline não faz deploy automático para produção sem aprovação
- [ ] Sem `curl | bash` ou `wget | sh` em scripts
- [ ] Artifacts de build não contêm secrets
- [ ] Container de build não tem acesso a sistemas de produção
- [ ] Logs de pipeline não contêm secrets
- [ ] SAST (Static Analysis) integrado ao pipeline
- [ ] Dependency scanning no pipeline

---

## Red Flags

1. `curl https://... | bash` — execução de código externo sem verificação
2. Deploy automático para produção sem aprovação
3. Container de CI com acesso ao banco de produção
4. Artifacts de build expostos publicamente contendo código sensível
