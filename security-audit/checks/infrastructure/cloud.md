# Cloud Security — AWS, GCP, Azure

Verificação de configurações de cloud — misconfigurations são a principal causa de vazamentos.

---

## AWS

### Busca no Código

```bash
# Credenciais AWS no código
grep -rn "AKIA[0-9A-Z]\{16\}" --include="*.{js,ts,py,go,rb,java,sh,env*}" -n
grep -rn "aws_access_key_id\|aws_secret_access_key" --include="*.{py,ini,conf,env*}" -n

# Configuração de serviços
find . -name "*.tf" | xargs grep -l "aws_" 2>/dev/null
find . -name "cdk.json" -o -name "cdk.context.json"
find . -name "serverless.yml" -o -name "serverless.yaml"
```

### Terraform — Verificações AWS

```bash
# S3 sem Block Public Access
grep -rn "aws_s3_bucket_public_access_block" --include="*.tf" | grep "false"

# Security Groups muito permissivos
grep -rn "0\.0\.0\.0/0\|cidr_blocks.*\[\"0\.0\.0\.0/0\"\]" --include="*.tf" -n -B 3

# RDS exposto ao internet
grep -rn "publicly_accessible.*true" --include="*.tf" -n

# Lambda sem VPC
grep -A 10 "aws_lambda_function" --include="*.tf" | grep -v "vpc_config"

# KMS — verificar se dados sensíveis têm encryption
grep -rn "server_side_encryption_configuration\|encrypt_at_rest" --include="*.tf" -n
```

### Configurações de Alto Risco

```bash
# EC2 com IAM role excessiva
grep -rn "iam_instance_profile\|aws_iam_role" --include="*.tf" -n

# CloudTrail desabilitado
grep -rn "aws_cloudtrail\|enable_logging.*false" --include="*.tf" -n

# GuardDuty desabilitado
grep -rn "aws_guardduty_detector\|enable.*false" --include="*.tf" -n

# MFA Delete no S3 (para dados críticos)
grep -rn "mfa_delete" --include="*.tf" -n
```

---

## Google Cloud Platform

```bash
# Service Account Keys
find . -name "service-account*.json" -o -name "*-credentials.json"
grep -rn "GOOGLE_APPLICATION_CREDENTIALS\|serviceAccountKey" --include="*.{js,ts,py,env*}" -n

# Terraform GCP
grep -rn "google_storage_bucket\b" --include="*.tf" -A 10 | grep -v "uniform_bucket_level_access.*true"
grep -rn "allUsers\|allAuthenticatedUsers" --include="*.{tf,yaml,json}" -n  # acesso público
```

---

## Checklist Geral

- [ ] Credenciais de cloud não no código (usar IAM roles/Workload Identity)
- [ ] Security Groups/Firewall sem `0.0.0.0/0` em portas sensíveis
- [ ] RDS/CloudSQL não exposto ao internet
- [ ] S3/GCS sem acesso público em buckets de dados
- [ ] CloudTrail/Cloud Audit Logs habilitado
- [ ] GuardDuty/Security Command Center habilitado
- [ ] Recursos com tagging adequado (para controle de acesso)
- [ ] Encryption at rest habilitado em todos os serviços de dados

---

## Red Flags

1. `AKIA[...]` hardcoded — AWS key no código
2. `publicly_accessible = true` no RDS
3. `0.0.0.0/0` em security group para porta 22 (SSH) ou 5432 (Postgres)
4. `allUsers` em IAM binding do GCS
5. Service account key commitada no repositório
6. CloudTrail desabilitado — sem audit log
