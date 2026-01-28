# Skill: Injection Checks

**Propósito:** Verificar SQLi, NoSQLi, Template, Command Injection e SSRF de forma defensiva.

---

## Entradas Esperadas

- [ ] Scope definition
- [ ] Attack surface map
- [ ] Entry points com input de usuário

---

## Saídas

- `injection_findings.md` - Findings de injection
- `injection_vectors.yaml` - Vetores testados

---

## Procedimento

1. **Identificar inputs** - Pontos de entrada
2. **Classificar sinks** - DB, command, template, HTTP
3. **Testar cada tipo** - Probes básicos
4. **Documentar findings** - Com PoC seguro

---

## SQL Injection

### Probes
```sql
' | '' | 1 OR 1=1 | 1 AND 1=2 | 1' AND '1'='1 | 1; SELECT 1--
```

| Sinal | Indica |
|-------|--------|
| Erro SQL | Error-based |
| Comportamento diferente | Boolean-based |
| Delay com SLEEP() | Time-based |

```python
# VULN: query = f"SELECT * FROM users WHERE id = {user_id}"
# SAFE: cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))
```

---

## NoSQL Injection (MongoDB)

```javascript
{"$ne": null} | {"$gt": ""} | {"$regex": ".*"} | {"$where": "1==1"}
```

```javascript
// VULN: db.users.find({username: req.body.username})
// SAFE: if (typeof req.body.username !== 'string') throw Error()
```

---

## Template Injection (SSTI)

| Engine | Probe | Resultado |
|--------|-------|-----------|
| Jinja2/Twig | `{{7*7}}` | 49 |
| Freemarker | `${7*7}` | 49 |
| ERB | `<%= 7*7 %>` | 49 |

```python
# VULN: template = Template(user_input)
# SAFE: Template("Hello {{name}}").render(name=user_input)
```

---

## Command Injection

### Probes
```bash
; id | | id | `id` | $(id) | & id | || id
```

```python
# VULN: os.system("ping " + host)
# SAFE: subprocess.run(["ping", host], shell=False)
```

---

## SSRF

### Probes
```
http://127.0.0.1/ | http://localhost/ | http://[::1]/
http://169.254.169.254/ | file:///etc/passwd
```

### Bypasses
```
http://127.0.0.1.nip.io/ | http://0x7f000001/ | http://127.1/
```

```python
# VULN: requests.get(user_url)
# SAFE: if not is_allowed_url(user_url): raise Error()
```

---

## Template de Finding

```markdown
## INJ-XXX: [Tipo] Injection

**CWE:** CWE-89|78|94|918 | **Severidade:** Critical
**Endpoint:** POST /api/search

### Probe / Response
{"query": "' OR 1=1--"} → Error: SQL syntax error...

### Recomendação
cursor.execute("SELECT * FROM x WHERE y = ?", (input,))
```

---

## Vetores YAML

```yaml
vectors_tested:
  sqli:
    - endpoint: /api/search
      param: query
      probes: ["'", "1 OR 1=1"]
      result: vulnerable
```

---

## Critérios de Qualidade

- Todos os inputs testados
- Probes não-destrutivos
- Findings com PoC reproduzível

---

## Token Budget

- **Sugerido:** 4K tokens | **Máximo:** 8K tokens
- **Reduzir:** Focar em endpoints públicos

---

## Fallbacks

| Situação | Ação |
|----------|------|
| DB desconhecido | Probes genéricos |
| WAF bloqueando | Documentar |
| Muitos endpoints | Priorizar por input |

---

## NÃO Fazer

- Payloads destrutivos (DROP, DELETE)
- Exfiltração de dados reais
- Exploração além de PoC
