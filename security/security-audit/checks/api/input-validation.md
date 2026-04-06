# Validação de Input na API

Toda entrada externa é potencialmente maliciosa. Validação inadequada é base para a maioria dos ataques.

---

## Princípios

1. **Validar no servidor** — frontend validation é UX, não segurança
2. **Whitelist, não blacklist** — definir o que é permitido, não o que é proibido
3. **Validar tipo, tamanho, formato e range**
4. **Rejeitar input inválido** — não tentar "corrigir" e continuar

---

## Busca por Falta de Validação

```bash
# Endpoints que usam req.body diretamente
grep -rn "req\.body\.\w\+\b" --include="*.{js,ts}" | grep -v "validate\|schema\|zod\|joi\|yup\|class-validator"

# Python sem validação
grep -rn "request\.json\['\|request\.form\['\|request\.args\['" --include="*.py" | grep -v "validate\|schema\|pydantic"

# Mass assignment direto
grep -rn "\.create(req\.body\|\.update(req\.body\|\.save(req\.body" --include="*.{js,ts}" -n
```

---

## Validação com Schemas

### Zod (TypeScript)
```typescript
import { z } from 'zod'

const loginSchema = z.object({
  email: z.string().email().max(255),
  password: z.string().min(8).max(128),
})

app.post('/api/auth/login', (req, res) => {
  const result = loginSchema.safeParse(req.body)
  if (!result.success) {
    return res.status(400).json({ errors: result.error.errors })
  }
  const { email, password } = result.data  // tipado e validado
})
```

### Pydantic (Python)
```python
from pydantic import BaseModel, EmailStr, validator

class LoginRequest(BaseModel):
    email: EmailStr
    password: str

    @validator('password')
    def password_length(cls, v):
        if len(v) < 8 or len(v) > 128:
            raise ValueError('Invalid password length')
        return v

@app.post('/auth/login')
async def login(body: LoginRequest):
    # body.email e body.password já validados
    ...
```

---

## Tipos de Validação Necessários

### Tipos de dado
```typescript
// ID deve ser UUID, não string arbitrária
id: z.string().uuid()

// Quantidade deve ser inteiro positivo
quantity: z.number().int().positive().max(1000)

// Enum de valores permitidos
status: z.enum(['pending', 'active', 'inactive'])
```

### Tamanho e range
```typescript
// Limitar tamanho de strings
name: z.string().min(2).max(100)

// Limitar arrays
tags: z.array(z.string()).max(20)

// Limitar objetos aninhados (depth bombing)
metadata: z.record(z.string()).optional()
```

### File Uploads
```javascript
// Verificar tipo MIME (não apenas extensão)
const allowedTypes = ['image/jpeg', 'image/png', 'image/webp']
if (!allowedTypes.includes(file.mimetype)) {
  return res.status(400).json({ error: 'Invalid file type' })
}

// Verificar tamanho
if (file.size > 5 * 1024 * 1024) {  // 5MB
  return res.status(400).json({ error: 'File too large' })
}

// Verificar magic bytes (não confiar no Content-Type)
const fileType = await fileTypeFromBuffer(file.buffer)
```

---

## Checklist

- [ ] Todos os endpoints de escrita têm schema de validação
- [ ] Validação no servidor (não apenas no frontend)
- [ ] Tipos de dados verificados (string não vira SQL, número não vira string)
- [ ] Tamanhos máximos definidos para strings, arrays e payloads
- [ ] Uploads verificam tipo MIME e magic bytes
- [ ] Uploads têm limite de tamanho
- [ ] IDs validados como UUID/numérico antes de usar em queries
- [ ] Sem mass assignment (não passar `req.body` diretamente para ORM)

---

## Red Flags

1. `await db.create(req.body)` — mass assignment direto
2. Sem schema de validação em nenhum endpoint
3. Upload aceita qualquer Content-Type
4. Strings sem limite máximo de tamanho (DoS por payload grande)
5. IDs do tipo `req.params.id` usados em queries sem validação de tipo
