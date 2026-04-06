# IAM — Identity and Access Management

Permissões excessivas em roles e service accounts são causa frequente de comprometimentos em escala.

---

## Princípio do Menor Privilégio

**Regra:** Conceder apenas as permissões mínimas necessárias para a função executar sua tarefa.

---

## AWS IAM — Verificações

```bash
# Políticas com permissões excessivas
grep -rn '"Action": "\*"\|"Action": \[""\]' --include="*.{tf,json}" -n
grep -rn '"Resource": "\*"' --include="*.{tf,json}" -n | grep -v "#"
grep -rn "AdministratorAccess\|PowerUserAccess" --include="*.{tf,json}" -n

# Terraform
grep -rn "aws_iam_role_policy\|aws_iam_policy" --include="*.tf" -A 10 | grep '"\*"'
```

**Políticas Problemáticas:**
```json
// CRÍTICO — acesso a tudo
{
  "Effect": "Allow",
  "Action": "*",
  "Resource": "*"
}

// PROBLEMÁTICO — acesso a todos os recursos de um serviço
{
  "Effect": "Allow",
  "Action": "s3:*",
  "Resource": "*"
}

// CORRETO — escopo mínimo
{
  "Effect": "Allow",
  "Action": ["s3:GetObject", "s3:PutObject"],
  "Resource": "arn:aws:s3:::my-bucket/uploads/*"
}
```

---

## Service Accounts (GCP)

```bash
# Roles excessivos em service accounts
grep -rn "roles/owner\|roles/editor\b" --include="*.{tf,yaml,json}" -n
grep -rn "google_project_iam_member\|google_project_iam_binding" --include="*.tf" -A 5 | grep "owner\|editor"
```

---

## CI/CD Tokens

```bash
# Verificar escopo de tokens usados no CI
grep -rn "GITHUB_TOKEN\|CI_JOB_TOKEN\|DEPLOY_KEY" --include="*.{yml,yaml}" | grep "permissions\|scopes"
cat .github/workflows/*.yml | grep -A 5 "permissions:"
```

**GitHub Actions — Permissões Mínimas:**
```yaml
permissions:
  contents: read     # apenas leitura ao código
  packages: write    # apenas se necessário para publicar
  # NÃO usar: permissions: write-all
```

---

## Checklist

- [ ] Nenhuma policy com `Action: "*"` e `Resource: "*"` 
- [ ] Service accounts com roles mínimos (não Owner/Editor)
- [ ] CI/CD tokens com permissões mínimas
- [ ] Tokens de deploy com escopo específico ao ambiente
- [ ] Rotação periódica de credenciais de longa duração
- [ ] MFA obrigatório para usuários IAM humanos

---

## Red Flags

1. `Action: "*", Resource: "*"` — acesso total
2. `roles/owner` para service account de aplicação
3. GitHub Actions com `permissions: write-all`
4. Usuário de CI com permissões de produção completas
5. Ausência de MFA em usuário root da AWS
