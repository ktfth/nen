# Skill: Code Review

**Propósito:** Revisão de código segura, objetiva e focada em vulnerabilidades.

---

## Entradas Esperadas

- [ ] Scope definition
- [ ] Attack surface map
- [ ] Arquivos/diretórios a revisar
- [ ] Threat model (opcional)

---

## Saídas

- `code_review_findings.md` - Findings estruturados
- `findings.yaml` - Export para tracking

---

## Procedimento

1. **Priorizar arquivos** - Por exposição e sensibilidade
2. **Revisar auth/authz** - Controles de acesso
3. **Revisar input handling** - Validação e sanitização
4. **Revisar output encoding** - XSS prevention
5. **Revisar queries** - SQL/NoSQL injection
6. **Revisar crypto** - Uso correto de criptografia
7. **Revisar error handling** - Info disclosure
8. **Documentar findings** - Formato padronizado
9. **Classificar severidade** - Por risk_scale.md

---

## Checklist por Linguagem

### Geral (Todas)
- [ ] Input validation presente
- [ ] Output encoding aplicado
- [ ] Queries parametrizadas
- [ ] Erros não expõem dados
- [ ] Logs não contêm secrets

### JavaScript/Node
- [ ] eval(), Function() não usados com input
- [ ] child_process com input sanitizado
- [ ] Path traversal em file operations
- [ ] Prototype pollution prevenido

### Python
- [ ] pickle/yaml.load não usados com untrusted data
- [ ] subprocess com shell=False
- [ ] Template injection em Jinja/Django
- [ ] SQL raw queries parametrizadas

### Java
- [ ] Deserialization segura
- [ ] XXE prevenido em XML parsers
- [ ] Path traversal em File operations
- [ ] Prepared statements usados

### Go
- [ ] Template injection em html/template
- [ ] Path traversal em file serving
- [ ] SQL injection em database/sql

---

## Template de Finding

```markdown
## F-XXX: [Título Curto]

**Severidade:** Critical|High|Medium|Low|Info
**Categoria:** [CWE-XXX ou OWASP category]
**Arquivo:** path/to/file.ext
**Linha:** XX-YY

### Descrição
[1-2 sentenças descrevendo o problema]

### Código Vulnerável
```lang
// Trecho de código com problema
```

### Impacto
[O que um atacante pode fazer]

### Recomendação
```lang
// Código corrigido ou padrão seguro
```

### Referências
- CWE-XXX: [link]
- OWASP: [referência]
```

---

## Output YAML

```yaml
# findings.yaml
findings:
  - id: F-001
    title: "SQL Injection in user search"
    severity: high
    cwe: CWE-89
    file: src/api/users.py
    line: 42
    status: open

  - id: F-002
    title: "XSS in comment display"
    severity: medium
    cwe: CWE-79
    file: src/views/comments.js
    line: 156
    status: open
```

---

## Priorização de Arquivos

| Prioridade | Tipo de Arquivo |
|------------|-----------------|
| 1 | Auth, login, session |
| 2 | API handlers, controllers |
| 3 | Database queries, models |
| 4 | File upload/download |
| 5 | Admin functions |
| 6 | User input forms |
| 7 | External integrations |
| 8 | Utilities, helpers |

---

## Critérios de Qualidade

- Findings são reproduzíveis
- Severidade justificada
- Recomendações são implementáveis
- Falsos positivos marcados

---

## Token Budget

- **Sugerido:** 6K tokens
- **Máximo:** 12K tokens
- **Reduzir:** Focar em top 10 arquivos, agrupar findings similares

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Arquivo muito grande | Dividir por função |
| Linguagem desconhecida | Focar em padrões genéricos |
| Framework proprietário | Buscar docs, inferir padrões |
| Sem tempo para full review | Priorizar auth + input |

---

## Anti-Patterns Rápidos

| Pattern | Risco |
|---------|-------|
| `query = "SELECT * FROM x WHERE id=" + input` | SQLi |
| `innerHTML = userInput` | XSS |
| `eval(userInput)` | RCE |
| `os.system(cmd)` | Command Injection |
| `open(userPath)` | Path Traversal |
| `password = "hardcoded"` | Credential Leak |
