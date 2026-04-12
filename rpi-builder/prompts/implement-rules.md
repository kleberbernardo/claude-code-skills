# Implement Rules — rpi-builder

Como executar implementações com disciplina, consistência e previsibilidade.

---

## Princípio Central

**Seguir o plano. Ponto.**

O plan foi construído com cuidado a partir de research real. Se parecer errado durante a implementação, parar e reportar — não improvisar.

---

## Antes de Escrever Qualquer Código

### 1. Ler o plano por completo

Não começar a implementar após ler apenas os primeiros passos. Ler tudo. Entender a sequência completa antes de escrever uma linha.

### 2. Verificar o estado atual dos arquivos

Para cada arquivo a **modificar**: ler o arquivo atual antes de editar.

Por que? O estado pode ter mudado desde o research. Editar com base no estado antigo gera conflitos.

### 3. Identificar onde parar em sessões anteriores (se for retomada)

Se a task está `in-progress`:
1. Quais passos do plan já foram executados?
2. O código correspondente existe?
3. Os testes desses passos passam?
4. Continuar a partir do próximo passo não concluído

---

## Durante a Implementação

### Seguir os passos em ordem

Os passos do plan têm dependências implícitas. Executar na sequência definida.

Exceção aceitável: passos completamente independentes podem ser feitos em paralelo — mas verificar se realmente são independentes.

### Consistência com o projeto

Ao escrever código novo:
- Usar a mesma convenção de nomes do módulo adjacente
- Usar os mesmos imports (não duplicar o que já está importado)
- Usar o mesmo estilo de error handling do projeto
- Usar os mesmos tipos de retorno (Promise vs sync, etc.)

Se não souber qual é o padrão: grep pelo módulo mais similar e copiar a estrutura.

### Mudanças mínimas em arquivos existentes

Ao modificar um arquivo:
- Adicionar apenas o que o plan especifica
- Não reorganizar imports existentes
- Não reformatar código não relacionado
- Não corrigir typos em linhas não tocadas
- Não adicionar comentários a código não modificado

### Tipos e contratos

Usar exatamente os tipos definidos no plan (copiados do Design).

Não criar aliases, wrappers ou versões "melhoradas" desses tipos.

---

## Tratamento de Erros na Implementação

### Quando o código não compila após uma mudança

1. Ler o erro completo
2. Entender a causa raiz
3. Corrigir apenas o que causou o erro
4. Não refatorar para "resolver de vez"

### Quando um teste falha

1. Ler a mensagem de falha completa
2. Identificar: é falha na implementação ou no teste?
3. Se na implementação: corrigir a implementação
4. Se no teste (spec errado): corrigir o teste mas verificar se o comportamento está correto
5. Nunca deletar ou pular um teste que falha

### Quando o plano parece inconsistente durante a implementação

Exemplos:
- O plano diz para importar `X` mas `X` não existe
- O plano diz para usar o campo `Y` mas o tipo não tem esse campo
- O plano diz para seguir o padrão do arquivo `Z` mas o arquivo não usa esse padrão

**Ação:** Parar. Não improvisar. Reportar exatamente o problema:

```
⚠️ Inconsistência encontrada durante implementação

Passo [N] do plano instrui: [o que o plano diz]
Realidade encontrada: [o que existe de fato]
Arquivo: src/[path]:[linha]

Preciso de instrução antes de continuar.
```

---

## O que Nunca Fazer Durante a Implementação

| Proibido | Por quê |
|----------|---------|
| Refatorar código adjacente | Cria risco desnecessário fora do escopo |
| Renomear variáveis existentes | Quebra contratos e rastreabilidade |
| "Aproveitar" para melhorar outra parte | Expande escopo sem plan |
| Adicionar feature não especificada | Viola o princípio de mudanças mínimas |
| Criar abstração para "facilitar" | Overengineering não solicitado |
| Modificar testes existentes para passar | Esconde falhas reais |
| Ignorar critério de conclusão que falha | Entrega task incompleta como completa |

---

## Validando a Conclusão

Só declarar a task concluída quando:

1. Todos os arquivos da seção 6 e 7 do plan foram criados/modificados
2. Todos os testes da seção 12 foram implementados
3. Todos os testes passam (zero falhas)
4. Todos os critérios da seção 13 são verdadeiros
5. Nenhum arquivo fora da seção 6/7 foi alterado (verificar com git diff)

---

## Relatório de Implementação

Ao concluir, reportar de forma objetiva:

```
✅ Task [NNN] — [Nome] implementada

Arquivos criados:
  src/[path]/[arquivo] — [o que faz]

Arquivos modificados:
  src/[path]/[arquivo] — [o que mudou]

Testes: [N] criados, [N/N] passando

Critérios de conclusão: [N/N] verificados

Status: done
```

Se não concluiu:

```
⚠️ Task [NNN] — implementação pausada

Concluído:
  ✅ Passos 1–3

Bloqueio:
  Passo 4: [descrição exata do problema]

Status: in-progress

Próxima ação necessária: [o que precisa ser resolvido]
```
