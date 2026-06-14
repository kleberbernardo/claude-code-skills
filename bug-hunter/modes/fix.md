# Modo FIX — bug-hunter

Aplicar a correção exatamente como planejada, com testes, e validar.

---

## Objetivo

Transformar o plano aprovado em código corrigido e testado — com mudança mínima, sem expandir escopo, garantindo que o comportamento correto fique protegido por teste.

---

## Pré-requisito (gate)

`Plan: done` no arquivo do bug. Se não estiver:

```
O bug NNN ainda não tem plano aprovado.
Rode /bug-hunter plan NNN antes de corrigir.
```

Parar.

---

## Fluxo de Execução

```
1. Ler .ai/debug/NNN-*.md completo (causa raiz + plano)
2. Confirmar que o código ainda corresponde ao descrito no plano
3. (Quando viável) escrever/identificar o teste que FALHA hoje e rodá-lo
4. Aplicar a correção mínima conforme o plano
5. Rodar o teste — confirmar que agora PASSA
6. Rodar a suíte relevante — confirmar ausência de regressão
7. Registrar a correção no arquivo do bug
8. Atualizar status e reportar
```

---

## Passo 2: Verificar estado atual

Antes de editar:
- Reabrir cada arquivo que o plano manda tocar.
- Confirmar que o trecho "antes" do plano ainda corresponde ao código atual.
- Se o código mudou desde o plano: **parar** e reportar a divergência — não improvisar sobre um plano desatualizado.

---

## Passo 3–5: Teste primeiro, depois correção

Seguir `prompts/fix-rules.md`.

### Ordem preferida (teste de regressão real)
1. Escrever (ou localizar) o teste que reproduz o bug.
2. Rodá-lo e **confirmar que falha** — isso prova que o teste captura o defeito.
3. Aplicar a correção do plano.
4. Rodar o teste e **confirmar que passa**.

> Se a infraestrutura não permitir rodar o teste isoladamente, descrever explicitamente por que e validar por leitura/execução alternativa — nunca declarar "passa" sem evidência.

### Aplicar a correção
- Apenas o que o plano define. Mudança mínima.
- Seguir os padrões do projeto (estilo, tratamento de erro, nomes).
- Não refatorar código adjacente, não renomear, não "melhorar" o que não causa o bug.
- Não tocar arquivos marcados como "não tocar" no plano.

---

## Passo 6: Suíte e regressão

- Rodar a suíte de testes da área afetada (ou a suíte completa se for rápida).
- Se algum teste pré-existente quebrar: investigar. Pode ser regressão real da correção — nesse caso, parar e reavaliar.
- **Nunca** alterar um teste só para ele passar. O teste protege o comportamento correto; se ele precisa mudar, há uma razão de comportamento, e ela deve estar no plano.

---

## Passo 7: Registrar a correção

Adicionar ao `.ai/debug/NNN-*.md` uma seção "Correção Aplicada":
- Arquivos alterados e o que mudou em cada um.
- Testes criados/ajustados.
- Resultado da suíte (N/N passando).
- Evidência de antes/depois quando houver (teste falhava → passa).

---

## Passo 8: Status e relatório

### Concluído com sucesso
```
Fix: done
Status: fixed
```
```
✅ Bug NNN-slug corrigido

Causa raiz: [uma linha]
Correção:
- src/[...]: [o que mudou]
Testes:
- [arquivo]: falhava antes, passa agora
Suíte: N/N passando

Registrado em .ai/debug/NNN-slug.md
```

### Bloqueado (código divergiu do plano, regressão inesperada)
```
⚠️ Correção pausada

Problema: [divergência ou regressão específica]
Localização: [arquivo:linha ou plano vs código atual]
Ação necessária: [o que decidir antes de continuar]
```
Manter `Status: planned`, `Fix: not-started`.

---

## Checklist Final antes de declarar fixed

- [ ] O código tocado corresponde ao plano (estado verificado)
- [ ] Existe um teste que falhava antes e passa depois (ou justificativa explícita)
- [ ] Apenas os arquivos do plano foram alterados
- [ ] Nenhum teste foi enfraquecido para forçar passagem
- [ ] A suíte relevante passa, sem regressão
- [ ] A correção está registrada no arquivo do bug
- [ ] Status atualizado para fixed
