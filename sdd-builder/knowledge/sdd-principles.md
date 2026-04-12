# SDD Principles — Spec-Driven Development

Os princípios fundamentais que guiam todas as decisões da skill `sdd-builder`.

---

## O que é Spec-Driven Development

SDD é uma abordagem onde a especificação completa precede a implementação.

**Sequência obrigatória:**
```
Ideia → Entrevista → PRD → Design Técnico → Tasks → Execução
```

Nenhuma etapa pode ser pulada. Cada etapa depende da anterior.

---

## Por que SDD

### O custo da ambiguidade

Ambiguidade descoberta durante a implementação custa 10x mais do que ambiguidade resolvida na especificação.

Um requisito vago gera:
- Implementação errada
- Retrabalho ao descobrir o requisito real
- Testes que testam a coisa errada
- Deploy de algo que não resolve o problema

### O custo de over-specification

Especificação excessiva também é problema:
- Documentos que ninguém lê
- Detalhes que mudam antes da implementação
- Tempo desperdiçado em decisões que deveriam ser do implementador

**Equilíbrio:** especificar o "o quê" e o "por quê" com precisão. Deixar o "como" para o implementador, exceto quando há restrição técnica explícita.

---

## Os Três Documentos

### PRD — O Contrato de Produto

O PRD responde: **o que o sistema deve fazer e por quê.**

- Define o problema, o público, os fluxos, as regras
- Não define como implementar tecnicamente
- É o contrato entre produto e engenharia
- Mudanças no PRD podem mudar tudo abaixo

### Design — O Contrato Técnico

O Design responde: **como o sistema vai funcionar internamente.**

- Define arquitetura, módulos, dados, contratos
- Derivado do PRD — não inventa além do PRD
- É o contrato entre arquitetura e implementação
- Mudanças no Design afetam tasks, não o PRD

### Tasks — O Contrato de Execução

As Tasks respondem: **o que exatamente o implementador deve fazer.**

- Derivadas do PRD + Design — não inventam além
- São executáveis de forma isolada e independente
- São verificáveis através dos critérios de conclusão
- Mudanças nas Tasks não afetam PRD ou Design

---

## Princípios de Qualidade da Spec

### 1. Clareza sobre completude

Um documento curto e claro é melhor do que um documento longo e confuso.
Se a resposta exige dedução do leitor, a spec está incompleta.

### 2. Verificabilidade

Todo critério de aceite, regra de negócio e critério de conclusão deve ser:
- Binário (verdadeiro ou falso)
- Observável sem interpretação
- Independente de quem verifica

### 3. Rastreabilidade

Cada elemento do Design deve rastrear para um requisito do PRD.
Cada task deve rastrear para um módulo do Design.

Se não há rastreamento, não há base para o elemento existir.

### 4. Premissas explícitas

Toda informação assumida (não confirmada) deve ser marcada.
Premissas ocultas são a principal fonte de retrabalho.

### 5. Escopo fechado

Todo documento deve ter um "Fora de Escopo" explícito.
O que não está listado como incluído, está excluído.

---

## O que SDD não é

- Não é burocracia — documentos são ferramentas, não fins em si mesmos
- Não é Big Design Up Front — a spec é suficiente para executar, não perfeita
- Não é imutável — pode ser revisada, mas revisões são conscientes e rastreadas
- Não é garantia — boa spec reduz risco; não elimina

---

## Compatibilidade com RPI

Tasks geradas pelo SDD Builder são otimizadas para execução por agentes RPI (Remote Planning and Implementation).

Um agente RPI pode executar uma task SDD sem:
- Fazer perguntas ao usuário
- Consultar documentos externos além dos listados na task
- Tomar decisões de arquitetura não especificadas
- Inventar escopo não descrito

Isso é alcançado porque cada task inclui:
- Contexto de por que existe
- Contratos copiados literalmente
- Passos acionáveis
- Critérios verificáveis
- Edge cases mapeados
