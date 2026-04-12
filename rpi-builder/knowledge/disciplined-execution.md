# Disciplined Execution — rpi-builder

Como executar tarefas com consistência profissional em projetos reais.

---

## O que é execução disciplinada

Execução disciplinada não é execução lenta. É execução **previsível**.

Um agente com execução disciplinada:
- Entrega exatamente o que foi especificado
- Não introduz surpresas
- Pode ser interrompido e retomado sem perda
- Deixa o projeto em estado mais limpo do que encontrou (dentro do escopo)
- Não cria dívida técnica não planejada

---

## Respeitando o Código Existente

### O projeto tem história

Decisões que parecem "erradas" geralmente têm motivo:
- A estrutura estranha foi feita para suportar um caso de uso específico
- O padrão inconsistente era o padrão antigo, ainda não migrado
- O comentário "TODO: refatorar" existe há 2 anos e ninguém tocou por uma razão

**Regra:** Entender antes de julgar. Nunca alterar o que não foi pedido.

### Consistência > Perfeição

Um arquivo que segue o padrão "errado" do projeto é melhor do que um arquivo que introduz um padrão "certo" isolado.

Consistência permite que outros engenheiros entendam e mantenham o código.
Perfeição isolada cria ilhas de estilo que fragmentam o projeto.

---

## Gerenciando Sessões Interrompidas

### Por que sessões são interrompidas

Em projetos reais, o trabalho raramente termina em uma sessão:
- Timeout da sessão
- Bloqueio por dependência externa
- Descoberta de problema que requer input do usuário
- Prioridade mudou

### Como o RPI resolve isso

Cada fase produz um artefato persistido:
- Research salvo → pode ser lido por qualquer sessão futura
- Plan salvo → contrato que não precisa ser reconstruído
- Status atualizado → sinaliza onde parou

### Ao retomar uma sessão

1. Executar `/rpi-builder status` para ver o estado atual
2. Executar `/rpi-builder next` para saber o próximo passo
3. Ler o plan antes de começar a implementar (mesmo que pareça óbvio)
4. Verificar o estado atual dos arquivos (podem ter mudado)
5. Continuar a partir do passo correto

---

## Lidar com Projetos sem Estrutura Definida

Se o projeto não tem `prd.md` ou `design.md`:

1. O modo `research` ainda funciona — analisa o código existente
2. O modo `plan` funciona com o research apenas — registrar a ausência como lacuna
3. O modo `implement` funciona com o plan

**Porém:** sem SDD, o escopo é definido apenas pela task. A task se torna o único contrato. Ser ainda mais conservador no escopo.

---

## Ambiente Multi-agente

Quando vários agentes ou desenvolvedores trabalham no mesmo projeto:

### Evitar colisões

Antes de implementar, verificar:
- O arquivo que vou modificar foi tocado recentemente?
- Existe branch ou trabalho em andamento nas mesmas áreas?

### Deixar estado claro

Atualizar o status da task imediatamente ao final de cada fase.
Status desatualizado causa confusão para outros agentes.

### Comunicar bloqueios

Se uma inconsistência bloqueia a implementação, reportar claramente:
- O que está bloqueando
- Onde está a inconsistência
- O que precisa ser resolvido

Nunca deixar a task `in-progress` sem registro do bloqueio.

---

## Qualidade sem Gold Plating

### O que incluir sempre

- Testes do que foi implementado (conforme o plan)
- Tratamento de erros definido no plan
- Tipos e validações do contrato

### O que não incluir

- Logging adicional não especificado
- Cache não especificado
- Métricas não especificadas
- Abstrações "para facilitar futuras extensões"
- Comentários extensos em código simples

### O critério

"Isso está no plan?" → Sim: incluir. Não: não incluir.

---

## Quando Escalar para o Usuário

Escalar (parar e reportar) quando:

1. O plano contradiz o design técnico
2. Um arquivo crítico não existe onde o plan assume
3. O tipo real é incompatível com o tipo do contrato
4. A implementação de um passo causaria regressão em código existente
5. Uma decisão de negócio (não técnica) é necessária para continuar

**Não** escalar quando:
- A solução é clara e está no plano
- É uma decisão técnica menor dentro do escopo
- O erro de compilação tem causa óbvia e solução direta
