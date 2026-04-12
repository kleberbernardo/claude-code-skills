# Task Rules — SDD Builder

Regras para criação de tasks executáveis no modo `tasks`.

---

## Princípio Central

**Uma task deve poder ser executada por um agente sem fazer nenhuma pergunta.**

Se um agente precisar adivinhar algo para executar a task, a task está incompleta.

---

## O que faz uma boa task

### 1. Objetivo Verificável

Ruim: "Implementar autenticação"
Bom: "Criar endpoint POST /auth/register que persiste usuário no banco e retorna JWT"

O objetivo deve ser verificável: quando está pronto, você consegue checar se foi feito.

### 2. Contexto que Explica o Porquê

O executor precisa entender o propósito para tomar decisões locais corretas.

Sempre conectar ao requisito do PRD e à decisão do Design que motivam a task.

### 3. Escopo Fechado

A task tem fronteiras claras: o que faz e o que explicitamente não faz.

Sem fronteiras, o executor pode expandir ou reduzir o escopo involuntariamente.

### 4. Passos Acionáveis

Ruim: "Implementar a lógica de negócio do módulo de autenticação"
Bom: "Criar função `validatePassword(input: string, hash: string): boolean` em `src/auth/password.ts` usando bcrypt.compare"

Cada passo deve ter: verbo + objeto + detalhes suficientes para execução.

### 5. Critérios de Conclusão Testáveis

Cada critério é um teste que pode ser executado para confirmar que a task está pronta.

Ruim: "A funcionalidade funciona corretamente"
Bom: "POST /auth/register com email duplicado retorna status 409 com corpo `{ error: 'Email already in use' }`"

---

## Regras de Sequenciamento

### Vertical First

Preferir tasks que entregam uma feature completa (dados → domínio → API → UI) a tasks horizontais (todos os schemas, depois todos os services, etc.)

Exceção: fundação técnica (setup, migrations base) deve ser horizontal e vir primeiro.

### Dependências Explícitas

Se task B precisa que task A esteja pronta, declarar explicitamente.
Nunca assumir ordem sem declarar dependência.

### Tamanho Ideal

- Muito pequena (< 30 min): considerar agrupar com task relacionada
- Ideal: 1–3 horas de execução focada
- Muito grande (> 4 horas): quebrar em sub-tasks

---

## O que Não Fazer nas Tasks

| Anti-padrão | Exemplo | Problema |
|------------|---------|---------|
| Escopo vago | "Implementar o módulo de usuário" | Executor não sabe o que incluir |
| Passos abstratos | "Adicionar validação adequada" | O que é "adequada"? |
| Critérios subjetivos | "O código deve ser limpo" | Não verificável |
| Falta de context | Apenas listar o que fazer sem explicar por quê | Executor toma decisões erradas |
| Duplicação | Mesma funcionalidade em duas tasks | Confusão sobre o que foi feito |
| Task gigante | Task que cobre 5 features diferentes | Difícil de rastrear, testar e revisar |

---

## Contratos: Copiar, Não Resumir

Quando uma task usa um tipo ou contrato definido no Design, copiar o conteúdo literal.

Não: "Use o tipo User conforme definido no design"
Sim: (colar o tipo completo na seção 8 da task)

Isso garante que o executor não precise consultar outro documento para implementar.

---

## Testes: Específicos, Não Genéricos

Testes ruins:
- "Testar o happy path"
- "Verificar que funciona"
- "Adicionar testes unitários"

Testes bons:
- "Testar `validatePassword` com hash bcrypt correto → retorna `true`"
- "POST /users com email inválido → status 400, body `{ error: 'Invalid email format' }`"
- "Criar usuário, depois tentar criar com mesmo email → segundo request retorna 409"

---

## Edge Cases: Reais, Não Hipotéticos

Derivar edge cases do PRD (seção 11), não inventar.

Se o PRD não mencionou, não é edge case desta task.

Exceção: edge cases técnicos óbvios (null, empty string, overflow) podem ser adicionados mesmo sem estar no PRD.

---

## Estrutura de Arquivos

Sempre listar com path completo a partir da raiz:

```
src/modules/auth/register.service.ts  [novo]
src/modules/auth/register.handler.ts  [novo]
src/db/schema/users.ts                [modificar]
tests/auth/register.test.ts           [novo]
```

Nunca:
```
service de auth  [novo]
handler          [modificar]
```
