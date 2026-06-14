# Regras de Correção — bug-hunter

Como aplicar a correção com disciplina e blindá-la com testes.

---

## Princípios

1. **Corrija a causa, não o sintoma.** Se a análise apontou a origem, a correção mora lá — não no ponto onde o erro aparece.
2. **Mudança mínima.** A menor alteração que resolve corretamente. Nada de refatorar "já que estou aqui".
3. **Sem escopo extra.** Outros problemas notados ficam registrados como observação, não viram correção neste bug.
4. **Consistência.** Siga os padrões do projeto: estilo, nomes, tratamento de erro, estrutura.

---

## Testes: o coração de uma correção confiável

Uma correção sem teste é uma esperança. O teste transforma "acho que consertei" em "provei que consertei e protegi contra a volta".

### Ciclo ideal (red → green)
1. Escreva um teste que reproduz o bug com o caso real.
2. Rode-o e **confirme que falha** (red) — prova que o teste captura o defeito.
3. Aplique a correção.
4. Rode-o e **confirme que passa** (green).
5. Rode a suíte relevante — sem regressão.

### Se já existe teste para a área
- Veja se um teste existente *deveria* ter pego o bug e não pegou — talvez ele precise de um caso a mais.
- Adicione o caso que faltava em vez de criar um arquivo paralelo, quando fizer sentido na convenção do projeto.

### Se o projeto não tem testes na área
- Proponha o mínimo viável que cubra o caso do bug (no plano).
- Siga a ferramenta/estrutura de teste já usada em outras partes do projeto.
- Não introduza um framework de teste novo sem o usuário pedir.

### Casos de borda
- Cubra os limites diretamente relacionados à causa raiz (vazio, zero, nulo, máximo, fronteira de range).
- Não tente cobrir o universo — cubra o que a causa raiz expôs.

---

## Regras de ouro dos testes

- **Nunca enfraqueça um teste para ele passar.** Se um teste precisa mudar, é porque o comportamento correto mudou — e isso deve estar explícito no plano.
- **Nunca remova um teste que falha** para "destravar". Entenda por que falha.
- **Nunca declare "passa" sem ter rodado** (ou sem justificar por que não deu para rodar e como validou de outra forma).

---

## Quando algo no plano não bate na hora de corrigir

- Código mudou desde o plano → **pare** e reporte; não improvise sobre plano desatualizado.
- A correção planejada revela um efeito colateral não previsto → **pare**, registre, e reavalie o plano com o usuário.
- A correção "correta" é maior do que o plano assumiu → não force a versão pequena e errada; reabra o plano.

---

## Registro

Ao terminar, registre no arquivo do bug (`Correção Aplicada`):
- Arquivos alterados e o que mudou.
- Testes criados/ajustados e o resultado (falhava → passa).
- Saída da suíte (N/N).
- Qualquer decisão tomada durante a execução.
