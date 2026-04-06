# Storage — Controle de Acesso a Arquivos

Arquivos de usuário devem ser acessíveis apenas pelo próprio usuário (ou com permissão explícita).

---

## Problemas Comuns

### 1. Path Enumerável
```
/uploads/1001/profile.jpg
/uploads/1002/profile.jpg  ← qualquer pessoa pode acessar mudando o número
```

**Solução:** Usar UUIDs no path ou URLs presignadas.

### 2. Path Traversal em Download
```javascript
// VULNERÁVEL
app.get('/files/:filename', (req, res) => {
  res.sendFile(path.join(UPLOADS_DIR, req.params.filename))
  // Payload: ../../../etc/passwd
})

// SEGURO
app.get('/files/:filename', authenticate, async (req, res) => {
  const safeName = path.basename(req.params.filename)  // remove path components
  const filePath = path.join(UPLOADS_DIR, safeName)
  if (!filePath.startsWith(UPLOADS_DIR)) return res.status(403).end()
  
  // Verificar se arquivo pertence ao usuário
  const file = await db.files.findOne({ name: safeName, userId: req.user.id })
  if (!file) return res.status(404).end()
  
  res.sendFile(filePath)
})
```

### 3. Acesso sem Autenticação
```bash
# Endpoints de arquivo sem middleware de autenticação
grep -rn "sendFile\|download\|createReadStream" --include="*.{js,ts}" -B 5 | grep -v "authenticate\|requireAuth\|protect"
```

---

## Checklist

- [ ] Arquivos privados não são servidos sem autenticação
- [ ] Path de arquivo verificado contra path traversal
- [ ] Arquivo verificado como pertencente ao usuário antes de servir
- [ ] Nomes de arquivo usando UUIDs, não IDs sequenciais
- [ ] Arquivos temporários removidos após uso
- [ ] Content-Disposition: attachment para downloads (não inline para arquivos perigosos)
- [ ] Content-Type correto para prevenir XSS via upload

---

## Red Flags

1. `/uploads/{id}/{filename}` com IDs sequenciais sem autenticação
2. `res.sendFile(path.join(BASE, req.params.file))` sem validação
3. Arquivos servidos sem verificar ownership
4. Upload de HTML/SVG servido com Content-Type que permite execução
