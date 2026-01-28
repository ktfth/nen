# Skill: SAST Baseline

**Propósito:** Estabelecer baseline de análise estática sem ferramenta específica.

---

## Entradas Esperadas

- [ ] Código fonte
- [ ] Scope definition
- [ ] Attack surface map
- [ ] Linguagem/framework identificados

---

## Saídas

- `sast_findings.md` - Findings de análise estática
- `sast_baseline.yaml` - Métricas e baseline

---

## Procedimento

1. **Identificar sinks críticos** - Funções perigosas
2. **Tracear data flow** - De sources a sinks
3. **Verificar sanitização** - Validação entre source e sink
4. **Catalogar findings** - Por categoria CWE
5. **Priorizar** - Por risco e exploitability
6. **Estabelecer baseline** - Contagem por categoria
7. **Definir threshold** - Para gates futuros

---

## Sinks por Categoria

### Injection (CWE-74)
| Linguagem | Sinks Perigosos |
|-----------|-----------------|
| SQL | `query()`, `execute()`, `raw()` |
| Command | `system()`, `exec()`, `spawn()` |
| LDAP | `search()`, `bind()` |
| XPath | `evaluate()`, `select()` |

### XSS (CWE-79)
| Context | Sinks |
|---------|-------|
| HTML | `innerHTML`, `document.write()` |
| Template | `{{}}` sem escape |
| DOM | `eval()`, `setTimeout(string)` |

### Path Traversal (CWE-22)
| Tipo | Sinks |
|------|-------|
| File read | `open()`, `readFile()`, `include()` |
| File write | `write()`, `writeFile()` |
| Directory | `listdir()`, `readdir()` |

### Deserialization (CWE-502)
| Linguagem | Sinks |
|-----------|-------|
| Java | `ObjectInputStream.readObject()` |
| Python | `pickle.loads()`, `yaml.load()` |
| PHP | `unserialize()` |
| Node | `eval(JSON.parse())` |

---

## Sources (Origens de Dados)

| Categoria | Exemplos |
|-----------|----------|
| HTTP | params, body, headers, cookies |
| File | uploads, configs |
| Database | queries que retornam user data |
| External API | responses de terceiros |
| Environment | env vars controladas por user |

---

## Template de Finding SAST

```markdown
## SAST-XXX: [Título]

**CWE:** CWE-XXX
**Severidade:** High
**Arquivo:** path/to/file
**Linha:** XX

### Source
```
// Onde o dado entra
let input = req.params.id;
```

### Sink
```
// Onde o dado é usado perigosamente
db.query("SELECT * FROM users WHERE id=" + input);
```

### Sanitização
- Presente: Não
- Necessária: Parameterized query

### Exploitability
- Acessível: Sim (endpoint público)
- Complexidade: Baixa
```

---

## Baseline YAML

```yaml
# sast_baseline.yaml
baseline:
  date: "YYYY-MM-DD"
  project: "[nome]"
  files_scanned: XX
  lines_of_code: YYYY

findings_by_category:
  injection:
    sqli: 3
    cmdi: 1
    xss: 5
  auth:
    broken_auth: 2
    broken_access: 1
  crypto:
    weak_crypto: 0
  other:
    info_disclosure: 4

severity_distribution:
  critical: 1
  high: 4
  medium: 6
  low: 8

threshold:
  max_critical: 0
  max_high: 0
  max_medium: 5
  action_on_breach: "block_merge"
```

---

## Data Flow Analysis (Simplificado)

```
SOURCE (user input)
    ↓
TRANSFORM (parsing, decoding)
    ↓
VALIDATE (if any)
    ↓
SANITIZE (if any)
    ↓
SINK (dangerous function)

Se VALIDATE/SANITIZE ausente → FINDING
```

---

## Critérios de Qualidade

- Todos os sinks críticos verificados
- Data flow documentado para cada finding
- Falsos positivos minimizados
- Baseline estabelecido com thresholds

---

## Token Budget

- **Sugerido:** 4K tokens
- **Máximo:** 8K tokens
- **Reduzir:** Focar em High+ severity, agrupar por categoria

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Codebase grande | Amostrar arquivos críticos |
| Framework obscuro | Buscar docs de sinks |
| Data flow complexo | Documentar incerteza |
| Sem ferramenta SAST | Análise manual focada |

---

## Checklist Rápido

Por arquivo crítico:
- [ ] Inputs do usuário identificados
- [ ] Sinks de injection verificados
- [ ] Sinks de XSS verificados
- [ ] Sinks de file ops verificados
- [ ] Sanitização presente onde necessária
