# Skill: Secure Defaults

**Propósito:** Definir configurações seguras e hardening para o projeto.

---

## Entradas Esperadas

- [ ] Stack tecnológica identificada
- [ ] Ambiente alvo (dev/staging/prod)

---

## Saídas

- `secure_config.md` - Configurações recomendadas
- `hardening_checklist.md` - Checklist de hardening

---

## Procedimento

1. **Identificar componentes** - Frameworks, servers, DBs
2. **Listar defaults inseguros** - O que mudar
3. **Definir configurações** - Valores seguros
4. **Priorizar** - Por risco de exposição

---

## HTTP Security Headers

```
Strict-Transport-Security: max-age=31536000; includeSubDomains
Content-Security-Policy: default-src 'self'
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Referrer-Policy: strict-origin-when-cross-origin
```

---

## Cookies Seguros

```
Set-Cookie: session=abc; Secure; HttpOnly; SameSite=Strict; Path=/; Max-Age=3600
```

---

## CORS Seguro

```javascript
// Restritivo: origin: ['https://app.example.com'], credentials: true
// EVITAR: origin: '*' ou credentials + origin: '*'
```

---

## Database

| DB | Configuração Segura |
|----|---------------------|
| PostgreSQL | `bind: 127.0.0.1`, scram-sha-256 |
| MySQL | `bind-address=127.0.0.1`, `local-infile=0` |
| MongoDB | `authorization: enabled`, `bindIp: 127.0.0.1` |

---

## Frameworks

### Node/Express
```javascript
app.use(helmet());
app.use(rateLimit({ windowMs: 15*60*1000, max: 100 }));
app.disable('x-powered-by');
```

### Python/Django
```python
DEBUG = False
SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
```

### Java/Spring
```yaml
server.error.include-stacktrace: never
```

---

## Logging Seguro

| Logar | NÃO Logar |
|-------|-----------|
| Auth attempts | Passwords |
| Access denied | Tokens/keys |
| Validation errors | PII/credit cards |

---

## Checklist de Hardening

### Servidor Web
- [ ] TLS 1.2+ | Ciphers modernos | Headers configurados | Version oculta

### Aplicação
- [ ] Debug off | Stack traces off | Rate limit on | Sessions seguras

### Banco de Dados
- [ ] Acesso local only | Least privilege | Backups encrypted

---

## Ambientes

| Config | Dev | Staging | Prod |
|--------|-----|---------|------|
| Debug | On | Off | Off |
| HTTPS | - | Sim | Sim |
| Rate limit | Off | Relaxado | Estrito |

---

## Critérios de Qualidade

- Nenhum default inseguro em prod
- Rollback documentado
- Testado antes de deploy

---

## Token Budget

- **Sugerido:** 3K tokens | **Máximo:** 6K tokens

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Stack desconhecida | Focar em headers HTTP |
| Legacy | Propor alternativas |

---

## Priorização

1. TLS/HTTPS → 2. Headers → 3. Cookies → 4. Rate limit → 5. Logging → 6. DB
