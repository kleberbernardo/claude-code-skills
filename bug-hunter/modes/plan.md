# Modo PLAN — bug-hunter

Transformar a causa raiz confirmada em um plano de correção preciso, antes de tocar no código.

---

## Objetivo

Descrever **onde** corrigir, **o quê** mudar e **por quê** — com detalhe suficiente para a correção ser aplicada sem improviso — e obter a aprovação do usuário.

---

## Pré-requisito (gate)

`RootCause: confirmed` no arquivo do bug. Se não estiver:

```
A causa raiz do bug NNN ainda não foi confirmada.
Rode /bug-hunter analyze NNN e confirme a análise antes de planejar.
```

Parar.

---

## Fluxo de Execução

```
1. Ler .ai/debug/NNN-*.md completo (compreensão + causa raiz)
2. Reabrir o código tocado pela causa raiz e confirmar o estado atual
3. Definir a abordagem da correção (a mínima que resolve a causa raiz)
4. Detalhar cada ponto de mudança: arquivo, trecho, mudança, porquê
5. Mapear impacto e efeitos colaterais
6. Definir a estratégia de teste
7. Escrever a seção "Plano de Correção" no arquivo do bug
8. Apresentar e aguardar aprovação do usuário
```

---

## Passo 3: Abordagem da correção

- Escolher a correção que ataca a **causa raiz**, não o sintoma.
- Preferir a **mudança mínima** que resolve o defeito de forma correta.
- Se houver mais de uma abordagem viável, apresentar a recomendada e citar a alternativa com o trade-off — não as duas como iguais.
- Se a correção correta for grande/arriscada, dizer isso explicitamente; não esconder o custo.

---

## Passo 4: Detalhamento dos pontos de mudança

Para **cada** arquivo a tocar, seguindo `templates/fix-plan-template.md`:

- **Arquivo:** path completo
- **Trecho atual (antes):** o código exato que será alterado, com `arquivo:linha`
- **Mudança proposta:** o que entra no lugar (ou descrição precisa, se não for literal)
- **Por quê:** ligação direta com a causa raiz — qual divergência isso elimina

Listar também, quando aplicável:
- Arquivos que **NÃO** devem ser tocados (e por quê), para conter o escopo.

---

## Passo 5: Impacto e efeitos colaterais

- Quem mais chama/usa o código que será alterado (`Grep` nas referências).
- Comportamentos adjacentes que dependem do trecho atual — a correção os afeta?
- Riscos de regressão e como o plano os mitiga.

---

## Passo 6: Estratégia de teste

Definir como o novo comportamento será protegido. Ver `prompts/fix-rules.md`.

- **Teste que falha hoje:** qual teste (novo ou existente) reproduz o bug e falha antes da correção.
- **Teste que protege depois:** o que deve passar após a correção.
- Tipo: unitário, integração, e2e — conforme o padrão do projeto.
- Se o projeto não tem testes para a área: propor o mínimo viável que cubra o caso do bug.
- Casos de borda relacionados à causa raiz que merecem teste.

---

## Passo 7: Escrever o plano

Adicionar a seção "Plano de Correção" ao `.ai/debug/NNN-*.md` (não sobrescrever a análise).

Manter `Plan: not-started` até a aprovação.

---

## Passo 8: Apresentar e aguardar aprovação

Apresentar o plano no chat: abordagem, pontos de mudança (arquivo/trecho/porquê), impacto e estratégia de teste.

Encerrar com:

```
Esse é o plano de correção. Posso aplicar?

- Se SIM, executo com /bug-hunter fix NNN (correção + testes).
- Se quiser ajustar algo (abordagem, escopo, testes), me diga antes.
```

### Aprovado
```
Plan: done
Status: planned
```
```
✅ Plano de correção registrado em .ai/debug/NNN-slug.md

Próximo passo: /bug-hunter fix NNN
```

### Ajustes pedidos
Revisar o plano conforme o feedback e reapresentar. `Plan` permanece `not-started`.

---

## Checklist de Qualidade do Plano

- [ ] A correção ataca a causa raiz confirmada, não um sintoma
- [ ] Cada ponto de mudança tem arquivo, trecho atual e o porquê
- [ ] A mudança é a mínima necessária para corrigir corretamente
- [ ] Impacto e efeitos colaterais foram mapeados com evidência
- [ ] Há um teste que falha antes e passa depois definido
- [ ] Arquivos fora de escopo estão marcados como "não tocar"
- [ ] O usuário aprovou antes de marcar Plan: done
