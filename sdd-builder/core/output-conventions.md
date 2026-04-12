# Output Conventions — SDD Builder

Convenções de nomes, paths, formatos e padrões de saída.

---

## Paths de Output

| Artefato | Path |
|----------|------|
| PRD | `.ai/product/prd.md` |
| Design Técnico | `.ai/product/design.md` |
| Tasks | `.ai/product/tasks/NNN-nome-da-task.md` |

---

## Convenção de Numeração de Tasks

Tasks são numeradas sequencialmente com 3 dígitos, zero-padded:

```
001-setup-projeto.md
002-modelo-de-dados.md
003-autenticacao-usuario.md
```

Sequência de ordenação recomendada:
1. Setup e configuração de ambiente
2. Fundação de dados (modelos, migrations)
3. Camada de domínio (serviços, regras de negócio)
4. Camada de API (endpoints, contratos)
5. Camada de frontend (componentes, páginas)
6. Integrações externas
7. Observabilidade e logging
8. Testes de integração / end-to-end

---

## Nomenclatura de Tasks

Formato: `NNN-verbo-substantivo.md`

- Começar com verbo no infinitivo
- Usar kebab-case
- Ser específico, não genérico

Bons exemplos:
```
001-criar-schema-usuario.md
002-implementar-registro-email.md
003-implementar-login-jwt.md
004-criar-endpoint-perfil.md
```

Maus exemplos:
```
001-backend.md          ← muito genérico
002-auth.md             ← sem verbo, vago
003-usuario-coisas.md   ← não descreve a ação
```

---

## Formato de Datas

Usar ISO 8601: `YYYY-MM-DD`

---

## Marcadores de Status

| Marcador | Significado |
|----------|------------|
| `[ASSUMIDO: ...]` | Premissa não confirmada pelo usuário |
| `[EM ABERTO: ...]` | Decisão pendente de clarificação |
| `[DEPENDE: ...]` | Dependência de outra task ou decisão |
| `[RISCO: ...]` | Risco identificado que requer atenção |

---

## Formato de Seções de Documento

### Títulos
- Usar `##` para seções principais numeradas
- Usar `###` para subseções
- Nunca pular nível hierárquico

### Listas
- Listas curtas (< 5 itens): traço `-`
- Listas de steps numerados: `1.`, `2.`, `3.`
- Tabelas para comparações ou mapeamentos

### Código e Schemas
- Usar code blocks com linguagem especificada
- SQL para schemas de banco
- TypeScript para tipos e interfaces
- JSON para exemplos de payload

---

## Confirmação de Output

Após salvar cada artefato, exibir:

```
✅ [Artefato] salvo em [path]

Próximo passo: [próximo comando]
```

Exemplo:
```
✅ PRD salvo em .ai/product/prd.md

Próximo passo: /sdd-builder design @.ai/product/prd.md
```
