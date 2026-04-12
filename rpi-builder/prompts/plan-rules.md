# Plan Rules — rpi-builder

Como construir planos técnicos que funcionam como contratos executáveis.

---

## Princípio Central

**O plan é o contrato. O implementador segue o contrato sem questionar.**

Se o implementador precisar tomar uma decisão de design para seguir o plan, o plan está incompleto.

---

## O que um Plano Precisa Resolver

### 1. Onde vai o código

Todo arquivo mencionado no plano deve ter:
- Path completo a partir da raiz
- Status: novo ou modificação
- Para modificações: exatamente o que muda (não "adicionar lógica")

### 2. Como vai ser feito

Cada passo deve responder:
- Qual arquivo?
- Qual função/classe/componente?
- Qual é a assinatura exata?
- O que acontece internamente?
- Quais casos de erro são tratados e como?

### 3. Quais contratos serão usados

Os contratos do Design devem aparecer **copiados literalmente** no plan.

O implementador não deve precisar abrir o design.md.

### 4. O que não fazer

A seção "Itens que não devem ser alterados" previne dois problemas comuns:
- Scope creep: implementador "melhora" coisas fora do escopo
- Over-engineering: implementador adiciona abstrações não pedidas

---

## Passos Bem Escritos

### Formato recomendado

```
N. [Verbo + Objeto]: `src/[path]/[arquivo]`
   Criar a função `[nome](param: Tipo): ReturnType` que:
   - [Faz X com Y]
   - Valida [condição]: se inválido, lança `[TipoErro]('[mensagem exata]')`
   - [Chama função Z com argumento W]
   - Retorna `[tipo e estrutura exata]`
```

### Bons verbos de abertura

- "Criar arquivo `[path]` com..."
- "Adicionar função `[nome]` em `[path]`..."
- "Modificar `[função]` em `[path]` para..."
- "Exportar `[tipo]` de `[path]`..."
- "Registrar rota `[método] [path]` em `[arquivo]`..."

### Maus verbos de abertura

- "Implementar a lógica de..."
- "Garantir que..."
- "Certificar-se de que..."
- "Tratar adequadamente..."

---

## Estratégia de Testes: Como Especificar

### Unitário — formato mínimo completo

```
| validateEmail | email válido | "user@email.com" | retorna true |
| validateEmail | sem @ | "useremail.com" | lança ValidationError("Invalid email") |
| validateEmail | domínio inválido | "user@" | lança ValidationError("Invalid email") |
```

### Integração — formato mínimo completo

```
| Criar usuário | banco vazio | POST /users { email, password, name } | 201 + { id, email, name, createdAt } (sem passwordHash) |
| Email duplicado | usuário com email X existe | POST /users { email: X } | 409 + { error: "Email already in use" } |
| Sem auth | - | POST /users sem Bearer token | 401 + { error: "Unauthorized" } |
```

---

## Tratando Lacunas do Research

Se o research registrou lacunas na seção 13, o plan deve endereçar cada uma:

| Lacuna do research | Como o plan resolve |
|-------------------|---------------------|
| "Constraint de unicidade não existe no schema" | Adicionar passo N: criar constraint UNIQUE no schema antes de usar no service |
| "Não ficou claro qual erro lançar" | Definir explicitamente no passo correspondente |
| "Padrão de auth não encontrado" | Copiar o padrão do design.md e definir como implementar |

Se uma lacuna **não pode ser resolvida no plano** (ex: depende de decisão de produto):
- Registrar em "Observações Finais"
- Não bloquear o plan por isso — tomar a decisão mais conservadora e documentar

---

## Seção "Itens que Não Devem Ser Alterados"

Esta seção é obrigatória e deve ser específica.

Ruim: "Não alterar o que não for necessário"
Bom:
```
- `src/auth/session.ts` — não modificar, apenas importar `getServerSession`
- `src/db/schema.ts` — não alterar tabelas existentes, apenas adicionar a nova
- Testes existentes em `tests/auth/` — não modificar nem mover
- `package.json` — não adicionar dependências além de [X] se necessário
```

---

## Alinhamento com Research

Antes de finalizar o plan, verificar:

1. Cada arquivo na seção 6/7 do plan existe no research?
   - Se não: o research está desatualizado ou o plan adicionou algo novo
2. Cada risco do research tem tratamento no plan?
   - Se não: o plan está incompleto
3. Os padrões identificados no research estão sendo seguidos?
   - Se não: justificar a diferença

---

## Quando o Plan Detecta Inconsistência

Se ao consolidar a informação for encontrada contradição entre documentos:

### PRD contradiz Design
- **Ação:** Parar. Reportar ao usuário. Não escolher um lado sozinho.
- **Mensagem:** "Encontrei inconsistência entre PRD e Design antes de gerar o plan. [Descrever exatamente a contradição]. Qual deve prevalecer?"

### Task inclui algo não no Design
- **Ação:** Registrar em "Observações Finais" como risco. Implementar apenas o que está no Design.
- **Não:** Implementar o que está na task mas não no Design

### Research encontrou padrão diferente do Design
- **Ação:** Seguir o Design (a verdade técnica). Documentar que o código existente difere.
- **Nota em "Observações":** "O módulo X usa padrão diferente do Design. Esta task seguirá o Design. Pode ser necessário alinhar o módulo X em task separada."
