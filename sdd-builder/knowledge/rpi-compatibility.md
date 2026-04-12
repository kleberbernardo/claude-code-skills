# RPI Compatibility — SDD Builder

Como preparar specs para execução por agentes RPI (Remote Planning and Implementation).

---

## O que é um agente RPI

Um agente RPI é um sistema autônomo de implementação que:
- Recebe uma task como entrada
- Lê os arquivos listados
- Implementa o que foi especificado
- Valida com os critérios de conclusão
- Não faz perguntas interativas durante a execução

Para que um agente RPI funcione bem, a task deve ser **auto-suficiente**.

---

## Checklist de Compatibilidade RPI por Task

### Completude de Informação
- [ ] O objetivo da task está em 1–2 frases sem ambiguidade
- [ ] Todos os arquivos a criar/modificar estão listados com path completo
- [ ] Todos os contratos estão copiados literalmente (não referenciados)
- [ ] Todos os passos de implementação são acionáveis sem inferência
- [ ] As regras de negócio aplicáveis estão na task, não só no PRD

### Verificabilidade
- [ ] Cada critério de conclusão é um teste binário
- [ ] Os testes unitários especificam: função, input, output esperado
- [ ] Os testes de integração especificam: endpoint, payload, status, body
- [ ] A validação manual tem passos numerados e resultado esperado

### Isolamento
- [ ] As dependências de outras tasks estão declaradas explicitamente
- [ ] A task pode ser executada sem rodar outras tasks em paralelo
- [ ] O fora de escopo previne colisão com outras tasks

---

## Padrões de Task Compatíveis com RPI

### Contratos: Inline, não por referência

Incompatível com RPI:
```
Use o tipo User conforme definido no design técnico.
```

Compatível com RPI:
```typescript
// Copiado de design.md — Seção 7
type CreateUserInput = {
  email: string;     // email válido, único no sistema
  password: string;  // mínimo 8 caracteres
  name: string;      // nome completo
}

type CreateUserOutput = {
  id: string;
  email: string;
  name: string;
  createdAt: Date;
}
```

### Passos: Concretos, não abstratos

Incompatível com RPI:
```
3. Implementar a camada de serviço com as validações adequadas
```

Compatível com RPI:
```
3. Criar função `createUser(input: CreateUserInput): Promise<CreateUserOutput>` 
   em `src/modules/users/user.service.ts`:
   - Verificar se email já existe via `db.query.users.findFirst({ where: eq(users.email, input.email) })`
   - Se existir: throw `new ConflictError('Email already in use')`
   - Fazer hash da senha com `bcrypt.hash(input.password, 12)`
   - Persistir com `db.insert(users).values({ ...input, passwordHash }).returning()`
   - Retornar campos públicos (sem passwordHash)
```

### Critérios: Testáveis, não subjetivos

Incompatível com RPI:
```
- [ ] O código está limpo e bem organizado
- [ ] A funcionalidade funciona corretamente
```

Compatível com RPI:
```
- [ ] POST /users com payload válido retorna status 201 e body com `id`, `email`, `name`, `createdAt`
- [ ] POST /users com email já cadastrado retorna status 409 e body `{ error: "Email already in use" }`
- [ ] POST /users com senha < 8 chars retorna status 400 e body `{ error: "Password must be at least 8 characters" }`
- [ ] `passwordHash` não está presente em nenhuma resposta da API
- [ ] Todos os testes em `tests/users/create.test.ts` passam
```

---

## Estrutura de Pasta Ideal para RPI

```
.ai/product/
  prd.md           ← contexto de negócio
  design.md        ← arquitetura técnica
  tasks/
    001-*.md       ← executar em ordem
    002-*.md
    003-*.md
```

O agente RPI deve:
1. Ler `prd.md` para entender contexto de negócio
2. Ler `design.md` para entender arquitetura
3. Executar tasks em ordem numérica
4. Verificar critérios de conclusão de cada task antes de avançar

---

## Sinais de Task Incompatível com RPI

Se você encontrar qualquer um destes padrões, a task precisa ser melhorada:

- "conforme necessário"
- "de forma adequada"
- "seguindo as boas práticas"
- "como definido anteriormente"
- "ver documentação"
- "similar ao X"
- "etc."
- "entre outros"
- Referência a seção sem copiar o conteúdo
- Passo que começa com "Garantir que..."
- Critério que usa "deveria" em vez de "deve"
