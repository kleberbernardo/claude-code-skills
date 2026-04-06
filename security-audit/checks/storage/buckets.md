# Cloud Storage — Buckets e Permissões

S3, GCS, Azure Blob: buckets públicos são uma das causas mais comuns de vazamentos de dados massivos.

---

## Casos Reais

- **Capital One 2019:** 100 milhões de clientes expostos via misconfiguration de S3
- **Twitch 2021:** 125GB de código-fonte e dados via bucket exposto
- **GoDaddy 2020:** Configuração incorreta expôs 28.000 clientes

---

## Busca no Código

```bash
# Configuração de buckets
grep -rn "s3\.createBucket\|new S3\|AWS\.S3\|boto3\.client.*s3\|storage\.bucket" --include="*.{js,ts,py,go}" -n

# ACL pública
grep -rn "ACL.*public\|acl.*public\|PublicRead\|public-read" --include="*.{js,ts,py,go,tf,yaml}" -n

# URLs presignadas com tempo longo
grep -rn "Expires.*[0-9]\{6,\}\|expiry.*[0-9]\{6,\}" --include="*.{js,ts,py}" -n  # muito tempo

# Política de bucket
find . -name "bucket-policy*.json" -o -name "*s3-policy*.json"
grep -rn "\"Principal\".*\"\\*\"" --include="*.json" -n  # acesso para qualquer um
```

---

## Verificações de Configuração

### AWS S3

```bash
# Verificar se Block Public Access está habilitado
grep -rn "BlockPublicAcls\|BlockPublicPolicy\|IgnorePublicAcls\|RestrictPublicBuckets" --include="*.{tf,yaml,json}" -n

# Terraform
grep -rn "aws_s3_bucket_public_access_block" --include="*.tf" -n
grep -rn "block_public_acls\s*=\s*true\|block_public_policy\s*=\s*true" --include="*.tf" -n
```

### Google Cloud Storage

```bash
grep -rn "allUsers\|allAuthenticatedUsers" --include="*.{tf,yaml,json}" -n  # acesso público
grep -rn "uniformBucketLevelAccess\|uniform_bucket_level_access" --include="*.{tf,yaml}" -n
```

---

## URLs Presignadas

```javascript
// BOAS PRÁTICAS — URLs presignadas com expiração curta
const url = await s3.getSignedUrlPromise('getObject', {
  Bucket: BUCKET_NAME,
  Key: objectKey,
  Expires: 3600,  // 1 hora — adequado
  // Expires: 86400 * 30  // 30 dias — muito longo
})

// VERIFICAR — nunca expor URL direta de bucket privado
// Usar sempre presigned URL com expiração razoável
```

---

## Checklist

- [ ] Buckets de dados privados não têm ACL pública
- [ ] Block Public Access habilitado em S3 (Account-level)
- [ ] URLs presignadas com expiração razoável (< 24h para dados sensíveis)
- [ ] CORS em buckets configurado corretamente (não wildcard para dados sensíveis)
- [ ] Versionamento habilitado (proteção contra deleção acidental)
- [ ] Encryption at rest habilitado
- [ ] Logging de acesso habilitado
- [ ] Bucket de backup não público
- [ ] Nenhum dump de banco armazenado em bucket público

---

## Red Flags

1. `"Principal": "*"` em bucket policy — acesso para qualquer pessoa
2. `ACL: 'public-read'` em bucket de dados de usuários
3. `aws_s3_bucket_public_access_block` ausente em Terraform
4. Bucket de logs ou backup com acesso público
5. Dados de PII em bucket sem criptografia
