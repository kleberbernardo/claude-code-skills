# Modelo de Severidade

Critérios para classificação de severidade e confiança de cada finding.

---

## Severidades

### 🔴 Critical (CVSS 9.0–10.0)

**Critérios (qualquer um):**
- RCE (Remote Code Execution) confirmado ou com exploit direto
- Acesso total ao banco de dados de produção
- Secret/credencial ativa confirmada e com acesso significativo
- Auth bypass total sem interação do usuário
- Acesso a dados de todos os usuários (IDOR em escala)
- Bucket/storage público com dados sensíveis confirmados

**Resposta esperada:** Correção em horas. Escalar imediatamente.

---

### 🟠 High (CVSS 7.0–8.9)

**Critérios (qualquer um):**
- JWT sem verificação de algoritmo (exploração viável)
- Sem rate limiting em endpoint de login (brute force trivial)
- IDOR confirmado em dados individuais de usuário
- XSS stored em contexto de produção
- SSRF com acesso a rede interna
- SQL injection em tabela de baixo impacto
- Secrets em repositório (sem confirmação se ainda ativo)
- Docker roda como root + socket montado

**Resposta esperada:** Corrigir em 1-2 semanas.

---

### 🟡 Medium (CVSS 4.0–6.9)

**Critérios:**
- Missing security headers (X-Frame-Options, CSP, HSTS)
- Rate limiting ausente em endpoints não-críticos
- Informações de stack em respostas de erro
- Dependência com CVE de exploração complexa
- CORS permissivo sem credentials
- Session sem HttpOnly mas sem XSS confirmado
- Logs com dados sensíveis (PII não-crítico)

**Resposta esperada:** Incluir no próximo sprint de segurança.

---

### 🔵 Low (CVSS 0.1–3.9)

**Critérios:**
- Melhorias de hardening sem risco direto
- Dependências desatualizadas sem CVE ativo
- Boas práticas não seguidas com impacto mínimo
- Informações de versão expostas
- Comentários com dados não-sensíveis

**Resposta esperada:** Backlog com prioridade normal.

---

### ⚪ Informational

**Critérios:**
- Sugestões de arquitetura de segurança
- Ferramentas recomendadas
- Boas práticas que melhorariam a postura mas sem risco mensurável
- Cobertura de auditoria incompleta (o que não foi verificado)

---

## Confiança

### High Confidence
- Evidência direta no código (linha específica, valor concreto)
- Vulnerabilidade reproduzível com comando ou payload específico
- Sem dependência de configuração de runtime desconhecida

### Medium Confidence
- Padrão de código que geralmente indica vulnerabilidade
- Dependência de configuração que não foi possível verificar
- Comportamento provável mas não 100% confirmado

### Low Confidence
- Suspeita baseada em padrão genérico
- Requer investigação adicional
- Pode ser falso positivo dependendo do contexto

---

## Ajustes de Severidade

### Elevar Severidade quando:
- Sistema está em produção com dados reais
- Sistema processa dados financeiros ou de saúde
- Dados de crianças ou grupos vulneráveis envolvidos
- Regulamentação aplicável (LGPD, PCI-DSS, HIPAA)
- Alta visibilidade pública (muitos usuários)

### Reduzir Severidade quando:
- Sistema ainda em desenvolvimento (sem usuários reais)
- Acesso requer autenticação prévia com múltiplos fatores
- Mitigação compensatória confirmada
- Dado exposto é público por natureza

---

## CVSS Simplificado

Para calcular CVSS aproximado:

| Fator | Baixo | Médio | Alto |
|-------|-------|-------|------|
| Attack Vector | Local | Adjacent | Network |
| Attack Complexity | High | Low | Low |
| Privileges Required | High | Low | None |
| User Interaction | Required | - | None |
| Confidentiality Impact | None | Low | High |
| Integrity Impact | None | Low | High |
| Availability Impact | None | Low | High |

Use calculadora CVSS oficial para score exato.
