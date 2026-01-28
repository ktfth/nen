# Skill: DAST Safety

**Propósito:** Executar checks DAST seguros, sem exploração agressiva.

---

## Entradas Esperadas

- [ ] Scope definition com autorização para testes ativos
- [ ] Attack surface map
- [ ] Lista de endpoints
- [ ] Ambiente de teste (nunca produção sem autorização explícita)

---

## Saídas

- `dast_findings.md` - Findings de testes dinâmicos
- `dast_evidence/` - Evidências coletadas

---

## Procedimento

1. **Confirmar autorização** - Verificar scope e nível permitido
2. **Verificar ambiente** - Confirmar que é dev/staging
3. **Testar headers** - Security headers básicos
4. **Testar auth flows** - Login, logout, session
5. **Testar input handling** - Sem payloads destrutivos
6. **Verificar errors** - Info disclosure
7. **Documentar findings** - Com evidência sanitizada
8. **Respeitar rate limits** - Não causar DoS

---

## Testes Seguros Permitidos

| Teste | Seguro | Perigoso |
|-------|--------|----------|
| Verificar headers | ✓ | - |
| Login com credenciais válidas | ✓ | - |
| Verificar session handling | ✓ | - |
| Input com caracteres especiais | ✓ | - |
| SQLi payload básico (`'`) | ✓ | Payload destrutivo |
| XSS payload refletido | ✓ | Stored sem cleanup |
| Fuzzing leve | ✓ | Fuzzing massivo |
| Verificar rate limit | ✓ | Testar até DoS |

---

## Checks de Security Headers

```http
# Verificar presença de:
Strict-Transport-Security: max-age=31536000
Content-Security-Policy: default-src 'self'
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 0 (ou CSP)
Referrer-Policy: strict-origin-when-cross-origin
```

---

## Checks de Session

| Verificação | Como Testar |
|-------------|-------------|
| Cookie Secure flag | Inspecionar Set-Cookie |
| Cookie HttpOnly | Inspecionar Set-Cookie |
| Cookie SameSite | Inspecionar Set-Cookie |
| Session fixation | Verificar se renova após login |
| Session timeout | Verificar expiração |

---

## Input Validation Tests (Seguros)

### SQL Injection Probe
```
' (aspas simples)
" (aspas duplas)
1 OR 1=1
1' OR '1'='1
```
Observar: Error messages, comportamento diferente

### XSS Probe
```
<script>alert(1)</script>
"><img src=x onerror=alert(1)>
javascript:alert(1)
```
Observar: Encoding, reflection

### Path Traversal Probe
```
../../../etc/passwd
..\..\..\..\windows\win.ini
....//....//....//etc/passwd
```
Observar: Error messages, conteúdo

---

## Template de Finding DAST

```markdown
## DAST-XXX: [Título]

**Severidade:** Medium
**Endpoint:** POST /api/search
**Tipo:** Reflected XSS

### Request
```http
POST /api/search HTTP/1.1
Host: [TARGET]
Content-Type: application/json

{"q":"<script>alert(1)</script>"}
```

### Response
```http
HTTP/1.1 200 OK
Content-Type: application/json

{"results":[],"query":"<script>alert(1)</script>"}
```

### Evidência
Payload refletido sem encoding na resposta.

### Impacto
XSS em API JSON - impacto se consumido por frontend sem sanitização.

### Recomendação
Sanitizar output mesmo em JSON responses.
```

---

## Rate Limiting

| Contexto | Limite Sugerido |
|----------|-----------------|
| Dev | 10 req/segundo |
| Staging | 5 req/segundo |
| Produção (se autorizado) | 1 req/segundo |

---

## Critérios de Qualidade

- Nenhum teste em produção sem autorização
- Rate limits respeitados
- Evidências sanitizadas
- Findings reproduzíveis

---

## Token Budget

- **Sugerido:** 4K tokens
- **Máximo:** 8K tokens
- **Reduzir:** Focar em endpoints críticos, agrupar resultados

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Sem autorização para testes ativos | Apenas análise passiva |
| Ambiente de produção | Recusar, escalar |
| Rate limit atingido | Pausar, documentar |
| Finding crítico | Parar, comunicar imediatamente |

---

## Checklist Pré-Teste

- [ ] Autorização documentada
- [ ] Ambiente confirmado (não produção)
- [ ] Rate limits definidos
- [ ] Dados de teste (não reais)
- [ ] Rollback plan se necessário

---

## O Que NÃO Fazer

- Explorar vulnerabilidades além de PoC
- Usar payloads destrutivos (DROP, DELETE)
- Exfiltrar dados reais
- Testar sem autorização
- Ignorar rate limits
- Armazenar dados sensíveis coletados
