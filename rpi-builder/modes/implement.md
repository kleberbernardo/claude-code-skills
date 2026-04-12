# Modo IMPLEMENT — rpi-builder

Executar a implementação com disciplina, seguindo o plano como contrato.

---

## Objetivo

Transformar o plan.md em código funcionando, testado e validado — sem desviar do escopo, sem improvisar arquitetura, sem "melhorar" o que não foi pedido.

---

## Pré-requisitos

1. Verificar que o arquivo do plano existe
2. Derivar o path da task do plano (seção de referências)
3. Verificar que a task existe
4. Verificar que o research existe
5. Verificar que o plano tem as seções obrigatórias
6. Se qualquer pré-requisito falhar: parar e informar

---

## Fluxo de Execução

```
1. Ler o plan.md completamente
2. Ler a task referenciada
3. Ler o research referenciado
4. Inspecionar estado atual do código (retomada de sessão)
5. Implementar passo a passo conforme o plano
6. Implementar testes conforme estratégia do plano
7. Validar os critérios de conclusão
8. Atualizar status da task
9. Reportar resultado
```

---

## Passo 1–3: Leitura Completa

Ler todos os documentos antes de escrever qualquer código.

**Do plan.md:**
- Estratégia geral (entender a abordagem antes de começar)
- Lista completa de arquivos a criar e modificar
- Passo a passo da implementação
- Contratos e interfaces
- Testes obrigatórios
- Critérios de conclusão
- Itens que NÃO devem ser alterados

**Da task:**
- Edge cases (podem conter detalhe não repetido no plano)
- Critérios de conclusão originais (comparar com os do plano)

**Do research:**
- Estado atual do código (confirmar que não mudou desde o research)
- Padrões identificados (seguir consistentemente)

---

## Passo 4: Verificação de Estado Atual

Antes de criar ou modificar qualquer arquivo:

1. Para cada arquivo a **modificar**: ler o arquivo atual
   - Confirmar que o estado corresponde ao que o research descreveu
   - Se o arquivo mudou desde o research: ajustar a implementação para o estado atual, não para o estado do research

2. Para cada arquivo a **criar**: verificar se já existe
   - Se existir: ler e entender por que existe antes de sobrescrever

3. Se a task está `in-progress` (sessão retomada):
   - Verificar o que já foi implementado
   - Identificar o passo correto para continuar
   - Não reimplementar o que já funciona

---

## Passo 5: Implementação

Seguir rigorosamente a ordem do passo a passo do plan.md.

### Regras de Implementação

**Consistência com padrões do projeto:**
- Usar as mesmas convenções de nomeação do projeto
- Usar os mesmos padrões de tratamento de erro
- Usar as mesmas estruturas de imports
- Usar o mesmo estilo de código existente

**Mudanças mínimas necessárias:**
- Modificar apenas o que o plano define
- Não refatorar código adjacente
- Não renomear variáveis existentes
- Não "melhorar" formatação de arquivos não relacionados
- Não adicionar comentários ou tipos em código não tocado

**Contratos e tipos:**
- Usar exatamente os tipos definidos no plano (copiados do Design)
- Não inventar tipos alternativos
- Não adicionar campos não especificados

**Segurança:**
- Validar inputs na camada correta (conforme design)
- Sanitizar outputs de acordo com o contrato
- Aplicar autenticação/autorização conforme a matriz do design
- Nunca expor campos sensíveis não previstos no contrato de resposta

**Quando algo no plano parecer errado:**
- Não improvisar uma solução alternativa
- Pausar e reportar a inconsistência ao usuário
- Aguardar instrução antes de continuar

---

## Passo 6: Testes

Implementar os testes exatamente como especificados na seção 12 do plano.

### Ordem de implementação dos testes

1. Criar o arquivo de teste
2. Implementar os unitários primeiro
3. Implementar os de integração depois
4. Verificar que todos passam antes de declarar conclusão

### Se um teste falhar

1. Ler a mensagem de erro
2. Identificar a causa raiz
3. Corrigir a implementação ou o teste (não remover o teste)
4. Nunca pular testes que falham
5. Nunca alterar um teste para forçar que passe sem corrigir a causa

---

## Passo 7: Validação dos Critérios de Conclusão

Para cada critério na seção 13 do plano:

- Verificar que é verdadeiro
- Se for um teste: confirmar que passa
- Se for comportamento: confirmar via leitura do código ou execução

Não declarar implementação concluída se algum critério falhar.

---

## Passo 8: Atualização de Status

### Se implementação foi concluída com sucesso (todos os critérios ok):
```
Implementation: done
Status: done
```

### Se implementação foi parcialmente concluída (sessão interrompida):
```
Implementation: not-started   (manter — não existe "parcial")
Status: in-progress
```

### Se implementação falhou por inconsistência no plano:
```
Implementation: not-started
Status: in-progress
```
Reportar a inconsistência claramente ao usuário.

---

## Passo 9: Relatório Final

Ao concluir com sucesso:

```
✅ Task 001-task-name implementada

Implementado:
- src/modules/[...]: [o que foi criado]
- src/routes/[...]: [o que foi criado/modificado]
- tests/[...]: [testes criados]

Testes: N/N passando

Critérios de conclusão: N/N verificados

Status atualizado: done
```

Se houver bloqueio:

```
⚠️ Implementação pausada — inconsistência encontrada

Problema: [descrição específica da inconsistência]
Localização: [plan.md seção X vs design.md seção Y]

Ação necessária: [o que o usuário precisa resolver]
```

---

## Checklist Final antes de Declarar Done

- [ ] Todos os arquivos listados no plano foram criados/modificados
- [ ] Nenhum arquivo fora do escopo foi alterado
- [ ] Todos os testes do plano foram implementados e passam
- [ ] Todos os critérios de conclusão do plano são verdadeiros
- [ ] Nenhum tipo ou contrato foi inventado além do plano
- [ ] Status da task foi atualizado corretamente
