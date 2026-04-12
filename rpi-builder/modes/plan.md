# Modo PLAN — rpi-builder

Gerar um plano técnico preciso, executável e completo com base na task e no research.

---

## Objetivo

Produzir um documento que responde: **"exatamente o que vai ser feito, como, em que ordem, e como validar?"**

O plan.md é o contrato da implementação. O implementador (humano ou agente) segue o plano sem improvisar.

---

## Resolução do ID

O argumento é sempre um número de task (ex: `001`). Antes de qualquer ação:

```
1. Glob .ai/product/tasks/NNN-*.md
2. Match único → usar e confirmar ao usuário o nome completo encontrado
3. Nenhum match → exibir lista de tasks disponíveis e parar
4. Nenhum ID passado → orientar a usar /rpi-builder next
5. Task com Status: done → avisar e aguardar confirmação antes de refazer
```

---

## Pré-requisitos

1. Resolver o ID para o arquivo da task (ver acima)
2. Verificar que o research correspondente existe: Glob `.ai/product/research/NNN-*.md`
   - Se não existir: interromper com mensagem:
     `Research não encontrado. Execute primeiro: /rpi-builder research 001`
3. Verificar que `.ai/product/prd.md` existe
4. Verificar que `.ai/product/design.md` existe
5. Criar pasta `.ai/product/plans/` se não existir

---

## Fluxo de Execução

```
1. Ler a task
2. Ler o research correspondente
3. Ler o PRD
4. Ler o Design
5. Consolidar em plano técnico
6. Salvar em .ai/product/plans/
7. Atualizar status da task
8. Informar próximo passo
```

---

## Passo 1–4: Leitura e Consolidação

Ler todos os quatro documentos completamente antes de escrever qualquer linha do plan.

**Da task, extrair:**
- Escopo exato (o que faz e o que não faz)
- Arquivos listados
- Contratos e tipos
- Critérios de conclusão
- Testes obrigatórios
- Edge cases

**Do research, extrair:**
- Estado atual do código relevante
- Padrões do projeto a seguir
- Dependências reais identificadas
- Riscos e conflitos identificados
- Lacunas que precisam de decisão

**Do PRD, extrair:**
- Regras de negócio que a implementação deve respeitar
- Critérios de aceite relacionados

**Do Design, extrair:**
- Contratos e tipos definitivos (usar estes, não inventar)
- Fluxo técnico que será implementado
- Módulo e responsabilidades
- Estratégia de autenticação/autorização aplicável

---

## Passo 5: Construção do Plano

Usar `templates/plan-template.md` como estrutura.

### Seção: Estratégia Geral

Descrever em 3–5 linhas qual é a abordagem de implementação e por que.

Não descrever o que vai ser feito (isso fica nos passos). Descrever **como** e **por que** essa abordagem.

Exemplo bom:
> Seguir o padrão handler → service → repository já existente no projeto. O handler faz parsing e autenticação, o service contém a lógica de negócio incluindo a verificação de duplicata, e o repository encapsula o acesso ao banco. Essa estrutura mantém consistência com os módulos users e workspaces existentes.

Exemplo ruim:
> Implementar o endpoint de criação de links com validação e persistência.

### Seção: Passo a Passo da Implementação

Cada passo deve ser **acionável** — o implementador sabe exatamente o que fazer sem precisar decidir nada.

Formato de cada passo:
```
N. [Ação]: [arquivo específico]
   - [detalhe 1]
   - [detalhe 2]
   - [condição ou regra relevante]
```

Passos ruins:
- "Implementar a lógica de validação"
- "Criar os testes necessários"
- "Adicionar autenticação ao endpoint"

Passos bons:
- "Criar função `validateLinkUrl(url: string): void` em `src/lib/validators.ts` que lança `ValidationError` se a string não começar com `http://` ou `https://`"
- "Adicionar guard de autenticação no início do handler usando `const session = await getServerSession(); if (!session) return NextResponse.json({ error: 'Unauthorized' }, { status: 401 })`"

### Seção: Arquivos a Criar / Modificar

Listar com path completo, separando criação de modificação.

Para arquivos a **criar**: descrever o que conterá.
Para arquivos a **modificar**: descrever exatamente o que muda (não o arquivo inteiro).

### Seção: Contratos e Interfaces

Copiar literalmente do Design os tipos e contratos que serão usados.

Não resumir. Não parafrasear. Copiar.

### Seção: Estratégia de Testes

Para cada teste:
- Especificar função/endpoint testado
- Especificar o caso (happy path, erro, edge case)
- Especificar o input
- Especificar o resultado esperado

### Seção: Critérios de Conclusão

Lista de verificações binárias. Cada item é verificável sem interpretação.

### Seção: Itens que não devem ser alterados

Listar explicitamente o que está fora de escopo mas pode parecer relacionado.

Evita que o implementador "aproveite para melhorar" algo que não deve ser tocado.

---

## Passo 6: Validação do Plano

Antes de salvar, verificar:

- [ ] Plano está alinhado com o Design (nenhuma decisão de arquitetura nova)
- [ ] Plano está alinhado com o PRD (nenhuma regra de negócio ignorada)
- [ ] Todos os arquivos do research foram considerados
- [ ] Riscos do research estão endereçados no plano
- [ ] Lacunas do research foram resolvidas ou escaladas
- [ ] Nenhum passo requer decisão do implementador
- [ ] Testes são específicos (não genéricos)
- [ ] "Itens que não devem ser alterados" protege o escopo

---

## Passo 7: Salvar e Atualizar

1. Salvar em `.ai/product/plans/NNN-task-name.md`
2. Atualizar o arquivo da task:
   - `Plan: done`
   - `Status: in-progress`

---

## Passo 8: Confirmar

```
✅ Plano salvo em .ai/product/plans/001-task-name.md

Próximo passo: /rpi-builder implement 001
```

---

## Tratamento de Inconsistências

Se ao consolidar for encontrada inconsistência entre documentos:

| Tipo de inconsistência | Ação |
|-----------------------|------|
| Task diz X, Design diz Y | Registrar em "Observações finais"; usar Design como fonte de verdade técnica |
| Research encontrou padrão diferente do Design | Registrar; seguir Design, anotar necessidade de alinhamento |
| Task inclui algo não no Design | Registrar como risco; não implementar o que não está no Design |
| PRD contradiz Design | Parar; reportar inconsistência ao usuário antes de continuar |
