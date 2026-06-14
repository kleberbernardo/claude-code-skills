# Regras de Análise de Causa Raiz — bug-hunter

Como investigar um defeito com rigor, em vez de tentar consertar no escuro.

---

## Mentalidade

- **Você é um detetive, não um chutador.** Cada conclusão precisa de evidência no código.
- **O primeiro lugar onde dói raramente é a causa.** Um `NullPointerException` na linha 200 costuma nascer de um valor que ficou nulo na linha 40.
- **Reproduza antes de teorizar.** Se você não sabe como disparar o bug, ainda não entende o bug.

---

## Roteiro de investigação

### 1. Estabelecer o esperado vs o observado
- Tenha uma definição **concreta** do resultado correto (da spec/URL/regra).
- Tenha o resultado observado em um caso específico.
- A diferença entre os dois é o que você precisa explicar.

### 2. Partir da melhor pista
- **Com stack trace:** vá ao frame mais profundo dentro do código do projeto (ignore frames de libs até cruzar a fronteira). Leia esse `arquivo:linha`. Suba a pilha reconstruindo como cada argumento chegou ali.
- **Sem stack trace:** comece no ponto de entrada do fluxo (rota, handler, evento, comando) e siga o dado com `Grep`/`Read` até onde ele se corrompe.

### 3. Bissecção do fluxo
- Encontre um ponto no meio do caminho onde você consegue afirmar se o estado ainda está correto.
- Correto até aqui → o defeito está depois. Errado já aqui → está antes.
- Repita, estreitando o intervalo, até a **linha exata de divergência**.

### 4. Isolar a causa raiz
Pergunte "por quê?" em cadeia até não haver um "porquê" anterior dentro do código:
- O total está errado → *por quê?* porque a taxa veio 0 → *por quê?* porque o config não carregou → *por quê?* porque a env var tem outro nome → **causa raiz**.
- Pare quando o próximo "porquê" sair do código (decisão de design, dado externo) — aí a causa raiz é a fronteira.

### 5. "Funcionava antes" → mudar para o histórico
- `git log -L` ou `git blame` no trecho da divergência.
- Identifique o commit que introduziu a mudança de comportamento.
- Isso frequentemente entrega a causa raiz de graça.

### 6. Falsear hipóteses
- Liste as causas plausíveis.
- Para cada uma, busque evidência que a **confirme ou descarte**.
- Registre as descartadas com o motivo — isso evita o usuário sugerir o que você já eliminou.

---

## Tipos de causa raiz e onde olhar

| Sintoma | Causas comuns | Onde olhar |
|---------|---------------|------------|
| Valor errado | cálculo, conversão de tipo, arredondamento, fuso horário | a função que produz o valor |
| Nulo/undefined | inicialização ausente, retorno não tratado, race | a origem do valor, não onde estoura |
| Estado intermitente | concorrência, cache, ordem de execução, efeito colateral | timing, estado compartilhado |
| Funciona local, falha em prod | env/config, dados reais, latência, versão de dep | configuração e diferenças de ambiente |
| Off-by-one / limite | índices, paginação, ranges inclusivos/exclusivos | a condição de borda |
| Regressão | mudança recente | git blame do trecho |

---

## Disciplina de evidência

- Toda linha do passo a passo cita `arquivo:linha`.
- Diferencie o que você **verificou** (li o código, rodei, vi o log) do que você **infere**. Marque inferência como tal.
- Se não conseguir reproduzir nem provar, **diga isso** — não maquie incerteza como certeza. Peça o que falta (`core/clarification-rules.md`).

---

## Erros a evitar

- ❌ Corrigir o ponto onde o erro **aparece** em vez de onde ele **nasce**.
- ❌ Parar na primeira hipótese plausível sem falsear as outras.
- ❌ Afirmar a causa sem ter entendido como o bug é disparado.
- ❌ Confundir "o teste passa agora" com "entendi por que quebrava".
- ❌ Ampliar a investigação para outros defeitos e perder o foco do bug atual.
