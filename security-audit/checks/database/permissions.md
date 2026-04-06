# Permissões de Banco de Dados

Princípio do menor privilégio aplicado ao banco de dados.

---

## Verificação de Permissões

```bash
# Verificar usuário de banco usado na aplicação
grep -rn "DATABASE_URL\|DB_USER\|POSTGRES_USER\|MYSQL_USER" --include="*.{env*,docker-compose*}" -n

# Se for 'postgres', 'root', 'admin', 'sa' → alerta
grep -rn "postgres://postgres:\|mysql://root:\|://admin:\|://sa:" --include="*.{js,ts,py,env*}" -n
```

---

## Problemas Comuns

### Usuário com Privilégios Excessivos
**Severidade: High**

```sql
-- INSEGURO — aplicação usando superuser
DATABASE_URL=postgresql://postgres:password@localhost/mydb

-- SEGURO — usuário específico com permissões mínimas
DATABASE_URL=postgresql://app_user:password@localhost/mydb
```

**Permissões mínimas para usuário da aplicação:**
```sql
-- Criar usuário com permissões mínimas
CREATE USER app_user WITH PASSWORD 'strong_password';
GRANT CONNECT ON DATABASE mydb TO app_user;
GRANT USAGE ON SCHEMA public TO app_user;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO app_user;
GRANT USAGE ON ALL SEQUENCES IN SCHEMA public TO app_user;

-- NÃO conceder:
-- CREATE TABLE, DROP TABLE, ALTER TABLE
-- pg_catalog access
-- Acesso a outros schemas
```

---

### Banco Exposto na Internet
**Severidade: Critical**

```bash
# docker-compose expondo porta do banco
grep -n "5432:\|3306:\|27017:\|6379:" docker-compose*.yml
```

**Postgres/MySQL/MongoDB/Redis nunca deve ter porta exposta ao internet.**
Deve ser acessível apenas pela aplicação via rede interna.

---

### Credenciais de Banco em Código

Ver `checks/repository/secrets.md`.

---

## RLS (Row Level Security)

Para aplicações multi-tenant, especialmente Supabase:

```sql
-- Verificar se RLS está habilitado
SELECT schemaname, tablename, rowsecurity
FROM pg_tables
WHERE schemaname = 'public';

-- Verificar policies existentes
SELECT * FROM pg_policies WHERE schemaname = 'public';
```

---

## Checklist

- [ ] Aplicação usa usuário de banco com permissões mínimas (não root/postgres)
- [ ] Porta do banco não exposta ao internet
- [ ] RLS habilitado em tabelas multi-tenant (se Postgres)
- [ ] Credenciais de banco em variável de ambiente, não no código
- [ ] Usuário de banco não tem `CREATEDB`, `CREATEROLE`, `SUPERUSER`
- [ ] Backups de banco criptografados e com acesso restrito

---

## Red Flags

1. `postgresql://postgres:` — usando superuser postgres
2. `mysql://root:` — usando root do MySQL
3. Porta 5432, 3306 ou 27017 exposta em docker-compose
4. Credenciais de banco hardcoded no código
