# RPI Principles — rpi-builder

Os princípios que fundamentam o método Research → Plan → Implement.

---

## Por que RPI existe

### O problema do agente impulsivo

Agentes (humanos ou AI) tendem a:
1. Receber uma task
2. Começar a implementar imediatamente
3. Descobrir problemas no meio da implementação
4. Improvisar soluções que não estavam no escopo
5. Entregar algo que funciona mas diverge da arquitetura

O resultado: código que passa nos testes mas que o time não consegue manter.

### A solução: separar entendimento de execução

RPI força a separação:
- **Research:** entender o que existe antes de decidir o que fazer
- **Plan:** decidir o que fazer antes de fazer
- **Implement:** fazer exatamente o que foi decidido

---

## Os Três Modos e seus Contratos

### Research = Fotografia do estado atual

O research não opina. Não decide. Não sugere.

Ele descreve o mundo como ele é:
- Quais arquivos existem
- O que eles fazem
- Quais padrões o projeto usa
- Quais riscos existem no estado atual
- O que vai precisar mudar

Um research bem feito torna o plan óbvio.
Um research superficial leva a um plan cheio de suposições.

### Plan = Contrato de implementação

O plan não é uma sugestão. É um contrato.

Ele responde: "exactamente o que vai ser feito?"

Características de um bom plan:
- Qualquer engenheiro que o leia sabe exatamente o que fazer
- Não há decisões de design a tomar durante a implementação
- O que está no escopo está explícito
- O que está fora do escopo está explícito
- Os testes são específicos, não genéricos

### Implement = Execução fiel do contrato

O implement não improvisa. Não melhora. Não expande.

Ele executa o plano e, se o plano parecer errado, para e reporta.

A disciplina aqui é o que torna o processo previsível:
- O result é o que foi especificado
- Nada mais, nada menos
- Rastreável até o plan, até o research, até a task

---

## Por que não pular etapas

### Pular o research

Sem research, o plan é baseado em suposições.

O plan assume que o arquivo X existe — mas ele foi renomeado.
O plan assume que o padrão Y é usado — mas o projeto usa Z.
O plan assume que a dependência W está disponível — mas foi removida.

Resultado: implementação que quebra por razões evitáveis.

### Pular o plan

Sem plan, a implementação é guiada pela intuição do momento.

A intuição pode ser boa, mas:
- Não é rastreável
- Não pode ser retomada por outro agente
- Não pode ser revisada antes de executar
- Não define claramente o que está fora do escopo

Resultado: código funcionando mas com escopo expandido, padrões inconsistentes e difícil de manter.

---

## Previsibilidade como valor

O método RPI é mais lento em casos simples. Mas:

1. **Em projetos reais, não existem casos simples.** Sempre há contexto, padrões e dependências que só o research revela.

2. **Sessões interrompidas são a norma, não a exceção.** Um research + plan salvo permite retomar exatamente onde parou.

3. **A velocidade percebida aumenta com o tempo.** Uma task com research e plan bem feitos é implementada sem surpresas. Uma task sem pesquisa e plan pode levar 3x mais tempo por causa das surpresas.

---

## SDD + RPI = ciclo completo

| Fase | Ferramenta | Produto |
|------|-----------|---------|
| Descoberta | `/sdd-builder spec` | `prd.md` |
| Arquitetura | `/sdd-builder design` | `design.md` |
| Quebra em tasks | `/sdd-builder tasks` | `tasks/*.md` |
| Pesquisa | `/rpi-builder research` | `research/*.md` |
| Planejamento | `/rpi-builder plan` | `plans/*.md` |
| Execução | `/rpi-builder implement` | código funcionando |

O SDD garante que **o que** fazer está claro.
O RPI garante que **como** fazer é disciplinado.

---

## Disciplina operacional

O princípio mais importante: **o que não está no plan, não é implementado.**

Não porque seja errado melhorar as coisas.
Mas porque mudanças não planejadas:
- Não foram revisadas
- Não têm testes definidos
- Não têm critérios de conclusão
- Podem introduzir regressões inesperadas
- Tornam o histórico de trabalho impossível de rastrear

Melhorias fora do escopo devem virar novas tasks no SDD.
