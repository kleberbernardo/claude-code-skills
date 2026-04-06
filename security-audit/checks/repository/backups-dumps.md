# Backups e Dumps no Repositório

Verificação de arquivos de backup e dumps de banco que podem conter dados sensíveis.

---

## Por que é crítico

Um dump de banco de dados de produção contém TODOS os dados: senhas (mesmo hashadas), PII de clientes, dados financeiros, dados de saúde. Um arquivo `.sqlite` é um banco de dados completo e acessível instantaneamente.

---

## Busca

```bash
# Dumps de banco
find . -name "*.sql" -not -path "*/node_modules/*" -not -path "*/.git/*"
find . -name "*.dump" -not -path "*/node_modules/*"
find . -name "dump.sql" -o -name "backup.sql" -o -name "database.sql"
find . -name "*.sql.gz" -o -name "*.sql.bz2"

# Bancos SQLite (arquivo completo)
find . -name "*.sqlite" -o -name "*.sqlite3" -o -name "*.db" -not -path "*/node_modules/*"
find . -name "db.sqlite3"  # Django default
find . -name "dev.db"       # Prisma default
find . -name "test.db"

# Redis dumps
find . -name "dump.rdb"
find . -name "*.rdb"

# MongoDB
find . -name "*.bson" -not -path "*/node_modules/*"

# Arquivos de backup genéricos
find . -name "*.bak" -o -name "*.backup" -o -name "*.old" | grep -v "node_modules"
find . -name "*_backup*" -o -name "*_bak_*"
```

---

## Análise de Risco por Tipo

### SQLite (`*.sqlite`, `*.sqlite3`, `*.db`)
**Risco: Critical** — banco de dados completo e acessível
```bash
# Verificar se contém dados (não apenas schema)
file database.sqlite  # mostra tipo e tamanho
ls -lh *.sqlite       # tamanho indica se tem dados
# Se > 10KB, provavelmente tem dados reais
```

### SQL Dumps (`.sql`)
**Risco: Critical** — pode conter dados de produção
```bash
# Verificar conteúdo sem expor dados
head -50 dump.sql | grep -E "INSERT INTO|CREATE TABLE|--"
wc -l dump.sql  # muitas linhas = muitos dados
grep -c "INSERT INTO" dump.sql  # conta registros
```

### Backups (`.bak`, `.backup`)
**Risco: High** — verificar conteúdo
```bash
file *.bak  # identificar tipo do arquivo
head -20 *.bak  # inspecionar início
```

---

## Checklist

- [ ] Nenhum arquivo `.sqlite` ou `.db` no repositório com dados
- [ ] Nenhum dump `.sql` no repositório
- [ ] Nenhum `.rdb` do Redis no repositório
- [ ] Arquivos de backup não estão trackeados pelo git
- [ ] Pasta `/backups` não está no repositório

---

## Recomendações

- Bancos SQLite de desenvolvimento: adicionar ao `.gitignore`, usar migrations para schema
- Fixtures/Seeds: usar dados sintéticos, nunca copiar produção
- Schema SQL: ok commitar apenas DDL (CREATE TABLE), nunca DML (INSERT com dados)
- Backups: armazenar em storage separado com acesso restrito, nunca no repositório
