# Princípios de Depuração — bug-hunter

Os fundamentos que sustentam o método deste skill. Leia quando precisar lembrar *por que* o fluxo é como é.

---

## 1. Sintoma não é causa

O lugar onde o programa quebra quase nunca é o lugar onde o defeito nasceu. Um erro é o fim de uma cadeia causal; depurar é caminhar essa cadeia de trás para frente até a origem. Corrigir o sintoma esconde o bug; corrigir a causa o elimina.

> Regra dos "5 porquês": pergunte "por quê?" até o próximo porquê sair do código.

## 2. Hipótese exige evidência

Depurar é ciência aplicada: formule uma hipótese e tente **falseá-la**. Uma hipótese que você não tentou refutar é só um palpite. Toda afirmação sobre a causa aponta para um `arquivo:linha` que você leu — não para uma intuição.

## 3. Reproduzir é entender

Se você não sabe disparar o bug de forma confiável, você ainda não o entendeu. A reprodução define o gatilho, e o gatilho define onde começar a investigar. Sem reprodução, qualquer correção é fé.

## 4. Bissecção vence força bruta

Não leia o sistema inteiro. Encontre um ponto médio no fluxo onde dá para decidir "o estado aqui ainda está correto?". A resposta corta o espaço de busca pela metade. Repita. É assim que `git bisect` acha o commit culpado e é assim que se acha a linha culpada.

## 5. "Funcionava antes" é um presente

Quando algo regrediu, o histórico tem a resposta. `git blame`/`git log -L` no trecho da divergência entrega o commit que mudou o comportamento — muitas vezes a causa raiz em uma linha de diff.

## 6. A correção mínima é a correção segura

Quanto menor o diff, menor a superfície de regressão e mais fácil revisar e reverter. Resista a refatorar "de passagem": cada linha extra é risco extra que não foi pedido. Outros problemas viram observações registradas, não correções acopladas.

## 7. Um bug sem teste volta

O teste é o que distingue "consertei" de "achei que consertei". O ciclo red→green prova duas coisas: que o teste captura o defeito (falha antes) e que a correção funciona (passa depois). E o teste fica de guarda contra a reincidência. Enfraquecer um teste para ele passar é apagar o alarme em vez de apagar o incêndio.

## 8. Não adivinhe o comportamento correto

A pior forma de errar é corrigir o código para um "correto" que você imaginou. Quando o esperado é ambíguo, a fonte da verdade é a spec, a doc ou a URL — peça e leia. Um bug bem corrigido contra a expectativa errada continua um bug.

## 9. Confirmação humana fecha o loop

A análise da causa raiz é apresentada e **confirmada** antes de virar plano; o plano é **aprovado** antes de virar código. Esses gates existem porque o usuário tem contexto que o código não mostra — e porque é barato corrigir uma análise no chat e caro corrigir um fix errado já aplicado.

---

## Anti-padrões clássicos

- **Shotgun debugging:** mudar várias coisas ao acaso esperando que uma funcione. Sem hipótese, sem aprendizado.
- **Tratar o `catch`:** silenciar a exceção onde ela aparece em vez de evitar a condição que a gera.
- **"Funciona na minha máquina":** ignorar que ambiente/config/dados são parte do sistema.
- **Confundir correlação com causa:** "mexi aqui e parou de dar erro" não prova que aqui era a causa.
- **Cobertura teatral:** escrever um teste que passa com e sem a correção — ele não protege nada.
