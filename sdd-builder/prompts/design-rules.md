# Design Rules — SDD Builder

Regras internas para elaboração do design técnico no modo `design`.

---

## Princípio Central

**Todo elemento do design deve ter correspondência no PRD.**

Se está no design, existe no PRD. Se não existe no PRD, ou não entra no design, ou vai para "Itens em Aberto".

---

## Regras por Seção

### Seção 3 — Stack

- Usar stack especificada no PRD quando existe
- Se não especificada: escolher stack moderna, justificar brevemente
- Não adicionar dependências sem justificativa
- Versões específicas para bibliotecas críticas (auth, ORM, frameworks)

### Seção 4 — Arquitetura

- Descrever em texto + diagrama quando há 3+ camadas ou componentes
- Justificar separação em módulos quando não for óbvio
- Evitar over-engineering: a arquitetura deve ser proporcional ao produto
- Se o produto for simples: arquitetura simples é a certa

### Seção 5 — Módulos

- Um módulo = uma responsabilidade única
- Nunca criar módulo "Utils" genérico — ser específico sobre o que faz
- Módulo deve ter fronteiras claras: o que entra, o que sai, com quem fala
- Evitar módulos circulares (A depende de B que depende de A)

### Seção 6 — Modelo de Dados

- Derivar entidades dos fluxos e regras de negócio do PRD
- Não criar tabelas "para o futuro" — apenas o que os fluxos exigem
- Campos de auditoria são padrão: `created_at`, `updated_at`
- Soft delete com `deleted_at` quando o contexto exige histórico
- Índices para campos de busca frequente e foreign keys
- Constraints de banco para integridade de dados

### Seção 7 — Contratos

- Tipos completos, não aproximações
- Separar tipos de entrada (request) e saída (response)
- Tipos de erro explícitos
- Usar linguagem da stack escolhida

### Seção 8 — Endpoints

- Um endpoint por operação — sem endpoints "faz tudo"
- Método HTTP semântico: GET para leitura, POST para criação, PUT/PATCH para update, DELETE
- Todos os erros possíveis documentados
- Paginação documentada para listagens
- Todos os campos de request e response tipados

### Seção 9 — Fluxos Técnicos

- Um fluxo técnico para cada fluxo principal do PRD
- Mostrar validações explicitamente
- Mostrar onde podem ocorrer falhas
- Mencionar transações quando múltiplas escritas no banco

### Seção 10 — Auth

- Não inventar sistema de auth — usar o que o PRD especificou
- Se PRD não especificou: escolher a opção mais simples e justificar
- Sempre documentar: quem pode acessar o quê (matriz de permissões)
- Endpoints sem auth devem ser explicitamente marcados como públicos

### Seção 16 — Trade-offs

- Para cada decisão não óbvia: o quê, o porquê, e o que foi descartado
- Justificativas devem ser técnicas e específicas
- Evitar "é mais simples" sem explicar o que tornaria mais complexo

---

## Anti-padrões a Evitar

| Anti-padrão | Descrição | Como evitar |
|------------|-----------|-------------|
| Over-engineering | Microserviços para um produto de uma pessoa | Proporcionar complexidade ao tamanho |
| God Object | Um módulo/serviço que faz tudo | Dividir por responsabilidade única |
| Premature optimization | Cache e CDN antes de ter usuários | Adicionar apenas quando justificado |
| Gold plating | Features de segurança não requeridas | Basear em requisitos do PRD |
| Assumption cascade | Decidir stack com base em suposições | Verificar restrições do PRD primeiro |

---

## Checklist Final do Design

Antes de salvar, verificar:

- [ ] Todo módulo tem correspondência com requisito do PRD
- [ ] Todo endpoint tem correspondência com fluxo do PRD
- [ ] Todos os tipos de usuário do PRD têm regras de autorização
- [ ] Modelo de dados cobre todas as entidades dos fluxos
- [ ] Estratégia de testes cobre os fluxos críticos
- [ ] Itens em Aberto registram todas as decisões pendentes
- [ ] Nenhuma seção está vazia ou com conteúdo genérico
