# Validação Server-Side

Validação de entrada no servidor — nunca confiar apenas no frontend.

---

## Princípio Fundamental

Frontend validation é UX. Qualquer atacante pode chamar a API diretamente com Postman/curl, bypassando toda validação do frontend.

---

## Busca por Falta de Validação

```bash
# Campos não validados indo para banco
grep -rn "\.create\s*(\s*{" --include="*.{js,ts}" -A 5 | grep "req\.body\." | grep -v "schema\|validate\|sanitize"

# Parâmetros de rota não tipados
grep -rn "req\.params\.id\b" --include="*.{js,ts}" | grep -v "parseInt\|Number(\|uuid\|isUUID"

# Campos opcionais que podem ser perigosos quando presentes
grep -rn "req\.body\.\w\+\s*\|\|" --include="*.{js,ts}" -n  # fallback sem validação
```

---

## Validação de Path Traversal

```bash
grep -rn "path\.join\|path\.resolve\|readFile\|sendFile" --include="*.{js,ts,py}" -n | grep "req\.\|params\.\|body\.\|query\."

# Python
grep -rn "open\s*(\s*\w*path\|open\s*(\s*f\|send_file\|safe_join" --include="*.py" -n | grep "request\.\|args\.\|form\."
```

**Proteção:**
```javascript
import path from 'path'

const BASE_DIR = '/var/uploads'
const requestedPath = req.params.filename

// VULNERÁVEL
const filePath = path.join(BASE_DIR, requestedPath)
// Payload: ../../etc/passwd

// SEGURO
const filePath = path.join(BASE_DIR, path.basename(requestedPath))
// OU
const filePath = path.resolve(BASE_DIR, requestedPath)
if (!filePath.startsWith(BASE_DIR)) {
  return res.status(403).json({ error: 'Access denied' })
}
```

---

## Upload de Arquivo — Validação Completa

```javascript
// VALIDAÇÃO COMPLETA de upload
import fileType from 'file-type'

async function validateUpload(file) {
  // 1. Verificar tamanho
  if (file.size > 5 * 1024 * 1024) throw new Error('File too large (max 5MB)')
  
  // 2. Verificar extensão
  const allowedExtensions = ['.jpg', '.jpeg', '.png', '.webp']
  const ext = path.extname(file.originalname).toLowerCase()
  if (!allowedExtensions.includes(ext)) throw new Error('Invalid extension')
  
  // 3. Verificar magic bytes (não confiar no Content-Type)
  const type = await fileType.fromBuffer(file.buffer)
  const allowedMimes = ['image/jpeg', 'image/png', 'image/webp']
  if (!type || !allowedMimes.includes(type.mime)) throw new Error('Invalid file type')
  
  // 4. Sanitizar nome do arquivo
  const safeName = crypto.randomUUID() + ext  // nome aleatório
  
  return safeName
}
```

---

## Checklist

- [ ] Validação de schema em todos os endpoints de escrita
- [ ] Path traversal prevenido em operações de arquivo
- [ ] Upload valida extensão E magic bytes
- [ ] IDs de rota validados como tipo correto (UUID, integer)
- [ ] Tamanho máximo de payload configurado
- [ ] Depth/complexity limits em JSON aninhado
- [ ] Validação de URL em campos de URL (não aceitar `javascript:` ou `file:`)

---

## Red Flags

1. `req.params.id` em query SQL sem validação de tipo
2. `path.join(BASE_DIR, req.params.file)` sem proteção de path traversal
3. Upload aceita qualquer Content-Type declarado pelo cliente
4. Sem limite de tamanho em body (DoS por payload enorme)
