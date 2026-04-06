# Nginx e Apache — Segurança de Servidor Web

---

## Nginx — Verificações

```bash
find . -name "nginx.conf" -o -name "*.nginx" -o -name "default.conf" | grep -v node_modules
cat nginx.conf
```

### Headers de Segurança

```nginx
# Verificar presença desses headers
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
add_header Content-Security-Policy "default-src 'self';" always;
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
add_header Permissions-Policy "camera=(), microphone=(), geolocation=()" always;

# Remover header que expõe versão
server_tokens off;
```

```bash
grep -n "add_header\|server_tokens\|X-Frame-Options\|HSTS\|CSP" nginx.conf
```

### SSL/TLS

```nginx
# Protocolos seguros
ssl_protocols TLSv1.2 TLSv1.3;

# Evitar protocolos obsoletos
# ssl_protocols SSLv2 SSLv3 TLSv1 TLSv1.1;  # INSEGURO

# Ciphers seguros
ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:...;
ssl_prefer_server_ciphers off;
```

```bash
grep -n "ssl_protocols\|ssl_ciphers\|SSLv\|TLSv1\b\|TLSv1.1" nginx.conf
```

### Exposição de Arquivos

```nginx
# Evitar expor arquivos sensíveis
location ~ /\. {
    deny all;  # nega acesso a .env, .git, etc.
}

location ~ \.(env|git|sql|bak|log)$ {
    deny all;
}
```

---

## Apache — Verificações

```bash
find . -name "httpd.conf" -o -name "apache2.conf" -o -name ".htaccess" | grep -v node_modules
```

```apache
# Remover versão
ServerTokens Prod
ServerSignature Off

# Desabilitar directory listing
Options -Indexes

# Headers de segurança
Header always set X-Frame-Options "SAMEORIGIN"
Header always set X-Content-Type-Options "nosniff"
Header always set Strict-Transport-Security "max-age=31536000"
```

---

## Checklist

- [ ] `server_tokens off` (Nginx) ou `ServerTokens Prod` (Apache)
- [ ] Headers de segurança: X-Frame-Options, X-Content-Type-Options, HSTS
- [ ] CSP configurado
- [ ] TLSv1 e TLSv1.1 desabilitados
- [ ] Acesso a `.env`, `.git`, `.sql` bloqueado
- [ ] Directory listing desabilitado
- [ ] HTTPS obrigatório (redirect de HTTP)

---

## Red Flags

1. `ssl_protocols TLSv1 TLSv1.1` — protocolos obsoletos com vulnerabilidades
2. `Options Indexes` no Apache — directory listing
3. Sem `location ~ /\.` block — `.env` e `.git` acessíveis
4. Sem headers de segurança
5. Sem HSTS — permite downgrade para HTTP
