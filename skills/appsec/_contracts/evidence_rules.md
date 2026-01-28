# Evidence Rules

Como coletar e documentar evidências sem vazar segredos.

---

## Princípios

1. **Nunca logar secrets** - Tokens, senhas, chaves = REDACT
2. **Sanitizar antes de salvar** - Não confie em filtros posteriores
3. **Mínimo necessário** - Capture apenas o que prova o finding
4. **Reproduzível** - Evidência deve permitir reprodução controlada

---

## O Que Sanitizar

### Sempre Redact
```
Passwords      → [REDACTED_PASSWORD]
API Keys       → [REDACTED_API_KEY]
Tokens         → [REDACTED_TOKEN]
Session IDs    → [REDACTED_SESSION]
Private Keys   → [REDACTED_PRIVATE_KEY]
```

### Mascarar Parcialmente
```
Emails         → u***@domain.com
IPs (internos) → 10.***.***.42
UUIDs          → abc12***-****-****-****-***def78
Credit Cards   → ****-****-****-1234
```

### Manter Intacto
```
HTTP Methods, Status Codes, Headers públicos
Paths de arquivos (sem dados sensíveis)
Mensagens de erro genéricas
Timestamps
```

---

## Formatos de Evidência

### Screenshot
- Usar blur/redact em áreas sensíveis
- Incluir timestamp visível
- Nomear: `F-XXX_description_YYYYMMDD.png`

### Request/Response
```http
POST /api/login HTTP/1.1
Host: [TARGET_HOST]
Content-Type: application/json

{"user":"test","pass":"[REDACTED]"}

---
HTTP/1.1 200 OK
Set-Cookie: session=[REDACTED_SESSION]

{"status":"ok","user_id":123}
```

### Code Snippet
```python
# Arquivo: auth/login.py, linha 42
# Finding: F-001 - Hardcoded credential
password = "[REDACTED]"  # VULN: credential in source
```

### Log Extract
```
[2024-01-15 10:30:45] ERROR: SQL syntax error near '[REDACTED_INPUT]'
[2024-01-15 10:30:45] Query: SELECT * FROM users WHERE id=[REDACTED]
```

---

## Template de Evidência

```markdown
## Evidência: F-XXX

**Coletada em:** YYYY-MM-DD HH:MM
**Ambiente:** dev|staging|prod
**Método de coleta:** manual|automatizado

### Pré-condições
- Usuário autenticado como: [role]
- Endpoint: [path]

### Reprodução
1. Passo 1
2. Passo 2
3. Passo 3

### Captura
[Screenshot ou snippet sanitizado]

### Resultado Esperado vs Observado
- Esperado: X
- Observado: Y

### Notas
[Observações adicionais]
```

---

## Armazenamento

- Evidências em diretório separado: `evidence/F-XXX/`
- Não commitar em repo público
- Criptografar se contiver dados sensíveis
- Reter apenas pelo período necessário

---

## Checklist Pré-Commit

- [ ] Nenhum secret em plaintext
- [ ] IPs internos mascarados
- [ ] Emails/PII mascarados
- [ ] Tokens/sessions redacted
- [ ] Arquivo nomeado corretamente
