# Playbook: Hardening de Banco de Dados

Guia para fortalecer segurança do banco de dados.

---

## PostgreSQL

```sql
-- 1. Criar usuário com permissões mínimas
CREATE USER app_user WITH PASSWORD 'senha_forte_aqui';
GRANT CONNECT ON DATABASE mydb TO app_user;
GRANT USAGE ON SCHEMA public TO app_user;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO app_user;
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA public TO app_user;

-- Para novas tabelas
ALTER DEFAULT PRIVILEGES IN SCHEMA public
  GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO app_user;

-- 2. Revogar acesso ao schema information_schema
REVOKE ALL ON SCHEMA information_schema FROM app_user;

-- 3. Verificar RLS para multi-tenant
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;

CREATE POLICY tenant_isolation ON orders
  FOR ALL
  TO app_user
  USING (tenant_id = current_setting('app.tenant_id')::uuid);

-- 4. Configurar connection via TLS
-- postgresql.conf:
-- ssl = on
-- ssl_cert_file = 'server.crt'
-- ssl_key_file = 'server.key'
```

---

## MySQL/MariaDB

```sql
-- Criar usuário com permissões mínimas
CREATE USER 'app_user'@'app_hostname' IDENTIFIED BY 'senha_forte';
GRANT SELECT, INSERT, UPDATE, DELETE ON mydb.* TO 'app_user'@'app_hostname';
FLUSH PRIVILEGES;

-- Verificar usuários sem senha
SELECT User, Host, authentication_string FROM mysql.user WHERE authentication_string = '';

-- Desabilitar remote root
DELETE FROM mysql.user WHERE User='root' AND Host NOT IN ('localhost', '127.0.0.1', '::1');
FLUSH PRIVILEGES;
```

---

## MongoDB

```javascript
// Criar usuário com permissões mínimas
db.createUser({
  user: "app_user",
  pwd: "senha_forte",
  roles: [
    { role: "readWrite", db: "mydb" }
    // Não usar "root" ou "dbAdmin" desnecessariamente
  ]
})

// Habilitar autenticação (mongod.conf)
// security:
//   authorization: enabled

// Verificar se autenticação está habilitada
db.runCommand({ connectionStatus: 1 })
```

---

## Checklist

- [ ] Usuário da aplicação com permissões mínimas
- [ ] Root/admin não acessível remotamente
- [ ] TLS habilitado na conexão
- [ ] RLS habilitado (PostgreSQL) para dados multi-tenant
- [ ] Backups criptografados
- [ ] Backups testados periodicamente (restore test)
- [ ] Slow query log habilitado (detectar abuso)
- [ ] Conexões máximas configuradas (DoS prevention)
- [ ] Banco não acessível da internet (apenas via app server)
