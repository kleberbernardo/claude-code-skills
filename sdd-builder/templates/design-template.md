# Technical Design Document
<!-- Gerado por /sdd-builder design -->
<!-- Baseado em: .ai/product/prd.md -->
<!-- Data: YYYY-MM-DD -->

---

## 1. Visão Técnica Geral

<!-- Descrição técnica do sistema em 3–5 frases. O que é, como está organizado, quais as fronteiras do sistema. -->

---

## 2. Objetivos Técnicos

<!-- O que a arquitetura precisa alcançar. Derivado dos requisitos não funcionais do PRD. -->

1. [Objetivo técnico 1 — ex: latência < 200ms em operações principais]
2. [Objetivo técnico 2 — ex: suportar X usuários simultâneos]
3. [Objetivo técnico 3 — ex: deploy sem downtime]

---

## 3. Stack e Dependências

### Stack Principal

| Camada | Tecnologia | Versão | Motivo |
|--------|-----------|--------|--------|
| Runtime | [Ex: Node.js] | [Ex: 20.x] | [Motivo] |
| Framework | [Ex: Next.js] | [Ex: 14.x] | [Motivo] |
| Banco de dados | [Ex: PostgreSQL] | [Ex: 16.x] | [Motivo] |
| ORM | [Ex: Drizzle] | [Ex: 0.30.x] | [Motivo] |
| Autenticação | [Ex: better-auth] | [Ex: latest] | [Motivo] |

### Dependências Principais

```
[pacote]@[versão] — [para que serve]
[pacote]@[versão] — [para que serve]
```

---

## 4. Arquitetura da Solução

<!-- Visão da arquitetura em alto nível. Usar diagrama textual quando a relação entre componentes for complexa. -->

### Diagrama de Componentes

```
[Cliente/Browser]
    ↓ HTTP/WebSocket
[Camada de Apresentação]
  └─ [Componentes UI]
    ↓ API Calls
[Camada de Aplicação]
  └─ [Controllers / Route Handlers]
    ↓
[Camada de Domínio]
  └─ [Services / Use Cases]
    ↓
[Camada de Infraestrutura]
  ├─ [Banco de Dados]
  ├─ [Storage]
  └─ [Serviços Externos]
```

### Decisão de Arquitetura

[Descrever a decisão principal de arquitetura e por que foi escolhida]

---

## 5. Módulos e Responsabilidades

<!-- Cada módulo: o que é, o que faz, com quem se comunica. Baseado nos requisitos funcionais do PRD. -->

| Módulo | Responsabilidade | Dependências | Arquivo/Pasta base |
|--------|-----------------|--------------|-------------------|
| [Módulo 1] | [Uma frase: o que faz] | [Quais módulos usa] | `src/[caminho]` |
| [Módulo 2] | [Uma frase: o que faz] | [Quais módulos usa] | `src/[caminho]` |

### [Módulo 1] — Detalhamento

- **Responsabilidade:** [Descrição completa]
- **Expõe:** [Funções/classes públicas]
- **Consome:** [Módulos e serviços externos]
- **Estado:** [Stateful ou stateless]

---

## 6. Modelo de Dados

<!-- Todas as entidades do sistema. Para cada entidade: campos, tipos, relacionamentos. -->

### Entidade: [Nome]

```sql
CREATE TABLE [nome_tabela] (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  [campo]     [TIPO] [NOT NULL | NULL],  -- [descrição]
  created_at  TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);

-- Índices
CREATE INDEX idx_[tabela]_[campo] ON [tabela]([campo]);
```

**Relacionamentos:**
- `[tabela.campo]` → `[outra_tabela.campo]` ([tipo de relação])

### Diagrama de Relacionamentos

```
[Entidade A]
  ├─ id
  ├─ [campo]
  └─ [fk_campo] ──→ [Entidade B].id

[Entidade B]
  ├─ id
  └─ [campo]
```

---

## 7. Contratos e Interfaces

<!-- Tipos e interfaces das operações principais. Usar a linguagem da stack escolhida. -->

```typescript
// [Domínio]

type [NomeTipo] = {
  [campo]: [tipo];  // [descrição]
}

interface [NomeInterface] {
  [método]([params]: [Tipo]): Promise<[Retorno]>;
}
```

---

## 8. Endpoints / Actions / Events

<!-- Para cada operação exposta externamente: método, path, request, response, erros. -->

### [Grupo/Módulo]

#### `[MÉTODO] [/path]`

**Descrição:** [O que faz]
**Autenticação:** [Requerida/Pública] — [tipo]
**Autorização:** [Quem pode acessar]

**Request:**
```json
{
  "[campo]": "[tipo] — [descrição]"
}
```

**Response (200):**
```json
{
  "[campo]": "[tipo] — [descrição]"
}
```

**Erros:**
| Código | Condição | Mensagem |
|--------|----------|---------|
| 400 | [Condição de erro] | `[mensagem]` |
| 401 | [Não autenticado] | `[mensagem]` |
| 403 | [Sem permissão] | `[mensagem]` |
| 404 | [Não encontrado] | `[mensagem]` |

---

## 9. Fluxos Técnicos

<!-- Tradução técnica dos fluxos do PRD em sequências de operações internas. -->

### Fluxo: [Nome do fluxo do PRD]

```
1. [Camada X] recebe [input]
2. [Validação]: verificar [condição]
   ├── Inválido → retornar [erro]
   └── Válido → continuar
3. [Service Y] executa [operação]
4. [DB] persiste [entidade] com [campos]
5. [Evento/Side effect]: [o que acontece depois]
6. Retornar [response]
```

**Garantias:**
- [Ex: operação é idempotente]
- [Ex: falha é revertida em transação]

---

## 10. Autenticação e Autorização

### Mecanismo de Autenticação

- **Método:** [JWT / Session / OAuth / etc.]
- **Provedor:** [Ex: better-auth, Auth.js, custom]
- **Token lifetime:** [Duração]
- **Refresh:** [Como funciona]

### Matriz de Permissões

| Operação | [Role 1] | [Role 2] | [Role 3] | Público |
|----------|---------|---------|---------|---------|
| [Endpoint/Action 1] | ✅ | ❌ | ❌ | ❌ |
| [Endpoint/Action 2] | ✅ | ✅ | ❌ | ❌ |

### Proteção de Rotas

```
Rotas públicas:
  [GET /path] — [motivo]

Rotas protegidas (auth requerida):
  [POST /path] — requer: [roles permitidos]
```

---

## 11. Segurança

<!-- Controles de segurança implementados. Derivado dos requisitos do PRD. -->

| Área | Controle | Implementação |
|------|---------|---------------|
| Input | Validação | [Biblioteca/abordagem] |
| Auth | [Proteção] | [Como implementado] |
| API | Rate limiting | [Biblioteca/estratégia] |
| Dados | Criptografia em repouso | [Quando aplicável] |
| Headers | Security headers | [Biblioteca] |

---

## 12. Integrações Externas

<!-- Para cada integração listada no PRD. -->

### [Serviço Externo]

- **Propósito:** [Para que serve]
- **Tipo:** [REST API / SDK / Webhook]
- **Autenticação:** [Como autentica]
- **Pontos de integração:** [Quais operações usam esse serviço]
- **Fallback:** [O que acontece se o serviço estiver indisponível]

---

## 13. Estratégia de Persistência

- **Banco principal:** [Tipo e uso]
- **Cache:** [Redis/in-memory — quando usar]
- **Storage de arquivos:** [Se aplicável]
- **Estratégia de backup:** [Frequência e retenção]
- **Migrations:** [Estratégia — ex: Drizzle migrate, Flyway]

---

## 14. Estratégia de Observabilidade

| Camada | O que monitorar | Ferramenta | Alerta |
|--------|----------------|------------|--------|
| Logs | [O que logar] | [Biblioteca] | [Quando alertar] |
| Métricas | [O que medir] | [Ferramenta] | [Threshold] |
| Erros | [Captura de exceções] | [Ex: Sentry] | [Criticidade] |
| Traces | [Rastreamento distribuído] | [Ex: OTel] | - |

---

## 15. Estratégia de Testes

| Tipo | O que testar | Ferramenta | Cobertura alvo |
|------|-------------|------------|----------------|
| Unitário | [Services, regras de negócio] | [Ex: Vitest] | [Ex: 80%] |
| Integração | [Endpoints, DB] | [Ex: Vitest + testcontainers] | [Ex: fluxos críticos] |
| E2E | [Fluxos principais do usuário] | [Ex: Playwright] | [Ex: happy paths] |

---

## 16. Trade-offs e Decisões Técnicas

<!-- Para cada decisão não óbvia: o que foi escolhido, o que foi descartado e por quê. -->

| Decisão | Escolha | Alternativa descartada | Motivo |
|---------|---------|----------------------|--------|
| [Área de decisão] | [O que foi escolhido] | [O que foi descartado] | [Motivo específico] |

---

## 17. Riscos Técnicos

| Risco | Probabilidade | Impacto | Mitigação |
|-------|--------------|---------|-----------|
| [Risco técnico] | Alta/Média/Baixa | Alto/Médio/Baixo | [Como mitigar] |

---

## 18. Itens em Aberto

<!-- Decisões técnicas pendentes de clarificação ou que dependem de informação externa. -->

| # | Item | Impacto | Quem decide | Prazo |
|---|------|---------|-------------|-------|
| 1 | [Decisão pendente] | [O que bloqueia] | [Responsável] | [Data] |
