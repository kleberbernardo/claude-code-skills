<!-- Seção adicionada por /bug-hunter plan ao arquivo .ai/debug/NNN-*.md -->
<!-- NÃO sobrescrever as seções de Compreensão e Análise da Causa Raiz. -->

---

## 5. Plano de Correção
<!-- Gerado por /bug-hunter plan — Data: YYYY-MM-DD -->

### Abordagem
<!-- A correção escolhida, ligada à causa raiz. A mínima que resolve corretamente. -->
<!-- Se houver alternativa, citar a recomendada + trade-off da outra. -->

### Pontos de mudança

#### [arquivo 1] — `src/[caminho]/[arquivo]`

**Trecho atual (antes):** `arquivo:linha`
```[linguagem]
[código exato que será alterado]
```

**Mudança proposta:**
```[linguagem]
[código que entra no lugar — ou descrição precisa]
```

**Por quê:** <!-- qual divergência da causa raiz isso elimina -->

<!-- Repetir o bloco acima para cada arquivo a tocar. -->

### Não tocar
<!-- Arquivos/áreas que ficam fora do escopo desta correção, e por quê. -->
- `src/[...]` — [motivo para não alterar]

### Impacto e efeitos colaterais
| Quem usa o código tocado | Como é afetado | Risco |
|--------------------------|----------------|-------|
| `src/[...]` | [chamada/dependência] | [alto/médio/baixo] |

### Estratégia de teste
- **Teste que falha hoje:** <!-- novo ou existente; reproduz o bug -->
- **Teste que protege depois:** <!-- o que deve passar após a correção -->
- **Tipo:** <!-- unitário / integração / e2e — conforme padrão do projeto -->
- **Casos de borda a cobrir:** <!-- relacionados à causa raiz -->
- **Arquivo de teste:** `tests/[...]` <!-- seguindo a convenção do projeto -->

### Critérios de conclusão
- [ ] [comportamento correto X verificável]
- [ ] [teste que falhava agora passa]
- [ ] [nenhuma regressão na suíte relevante]
