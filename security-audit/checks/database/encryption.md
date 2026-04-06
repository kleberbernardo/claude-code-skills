# Criptografia em Banco de Dados

Proteção de dados em repouso e em trânsito.

---

## Dados que DEVEM ser Criptografados

### Campos Altamente Sensíveis (criptografia obrigatória)
- Números de cartão de crédito (PAN) — PCI-DSS obriga
- CVV/CVC — PCI-DSS proíbe armazenar
- Dados biométricos
- Chaves de recuperação e backup codes (hash)
- Tokens de acesso a sistemas externos

### Campos Sensíveis (recomendado criptografar)
- CPF, RG, passaporte
- Número de conta bancária
- Dados de saúde (PHI)
- Endereços completos de usuários

### Verificação
```bash
# Buscar campos que podem conter dados sensíveis sem criptografia
grep -rn "cpf\|ssn\|credit_card\|card_number\|account_number\|phone" --include="*.prisma" -n
grep -rn "String\|text\|varchar" --include="*.prisma" | grep -i "cpf\|ssn\|card\|account"

# Prisma schema
grep -rn "creditCard\|cardNumber\|cpf\|ssn" --include="*.prisma" | grep -v "@db.VarChar\|String"
```

---

## Criptografia de Campo

```javascript
// Usando @prisma/client com encryption
import { createCipheriv, createDecipheriv, randomBytes } from 'crypto'

const ENCRYPTION_KEY = Buffer.from(process.env.FIELD_ENCRYPTION_KEY, 'hex')  // 32 bytes

function encrypt(text: string): string {
  const iv = randomBytes(16)
  const cipher = createCipheriv('aes-256-gcm', ENCRYPTION_KEY, iv)
  
  let encrypted = cipher.update(text, 'utf8', 'hex')
  encrypted += cipher.final('hex')
  const authTag = cipher.getAuthTag()
  
  return iv.toString('hex') + ':' + authTag.toString('hex') + ':' + encrypted
}

function decrypt(encryptedData: string): string {
  const [ivHex, authTagHex, encrypted] = encryptedData.split(':')
  const iv = Buffer.from(ivHex, 'hex')
  const authTag = Buffer.from(authTagHex, 'hex')
  
  const decipher = createDecipheriv('aes-256-gcm', ENCRYPTION_KEY, iv)
  decipher.setAuthTag(authTag)
  
  let decrypted = decipher.update(encrypted, 'hex', 'utf8')
  decrypted += decipher.final('utf8')
  
  return decrypted
}
```

---

## Encryption at Rest

```bash
# Verificar se banco tem encryption at rest habilitado
# RDS — verificar se StorageEncrypted = true
# MongoDB Atlas — encryption habilitado por padrão no M10+
# Local — verificar documentação do banco
grep -rn "encrypt.*storage\|storage.*encrypt\|at.rest" --include="*.{tf,yaml,yml}" -i -n
```

---

## Conexão com TLS

```bash
# Verificar configuração TLS na conexão
grep -rn "ssl.*false\|sslmode.*disable\|rejectUnauthorized.*false" --include="*.{js,ts,py,env*}" -n
```

---

## Checklist

- [ ] Campos de cartão de crédito não armazenados em texto plano (PCI-DSS)
- [ ] CVV/CVC não armazenados (proibido por PCI-DSS)
- [ ] Dados sensíveis (CPF, dados de saúde) criptografados no banco
- [ ] Chave de criptografia em secrets manager, não no código
- [ ] Conexão com banco usa TLS/SSL
- [ ] Encryption at rest habilitado em banco gerenciado (RDS, Atlas, etc.)
- [ ] Backups criptografados

---

## Red Flags

1. Campos `credit_card_number` ou `cvv` como String/varchar sem indício de criptografia
2. `rejectUnauthorized: false` na conexão com banco de produção
3. Chave de criptografia hardcoded no código
4. PAN (Primary Account Number) armazenado sem mascaramento mínimo
