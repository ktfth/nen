# I/O Schema

Convenções de entrada e saída para todas as skills.

---

## Entradas Padrão

### Contexto de Projeto
```yaml
project:
  name: string
  language: string[]
  framework: string[]
  repo_url: string (opcional)
  branch: string (default: main)
```

### Escopo
```yaml
scope:
  include:
    - path/to/file
    - path/to/dir/**
  exclude:
    - node_modules/**
    - vendor/**
  environments:
    - dev
    - staging
    - production (requer autorização)
```

### Autorização
```yaml
authorization:
  level: read_only | passive_test | active_test
  approved_by: string
  date: YYYY-MM-DD
  restrictions: string[]
```

---

## Saídas Padrão

### Finding
```yaml
finding:
  id: F-001
  title: string (< 80 chars)
  severity: critical | high | medium | low | info
  category: string (OWASP category)
  location:
    file: path/to/file
    line: number (opcional)
    endpoint: string (opcional)
  description: string (< 200 chars)
  evidence: string (sanitizado)
  recommendation: string
  references: url[]
  status: open | fixed | accepted | false_positive
```

### Artefato
```yaml
artifact:
  type: report | checklist | diagram | config
  format: md | json | yaml | mermaid
  path: string
  checksum: sha256 (opcional)
```

---

## Convenções

### Nomenclatura de IDs
- Findings: `F-XXX` (XXX = número sequencial)
- Risks: `R-XXX`
- Actions: `A-XXX`

### Severidade
Ver `risk_scale.md` para definições.

### Sanitização de Evidências
Ver `evidence_rules.md` para regras.

### Referências Externas
- OWASP: usar ID do ASVS/Top10 quando aplicável
- CWE: incluir CWE-ID
- CVE: incluir CVE-ID para deps vulneráveis

---

## Formatos de Output

### Markdown (padrão)
Usar para relatórios, checklists, documentação.

### JSON
Usar para integração com ferramentas, exports estruturados.

### YAML
Usar para configs, definições de escopo.

### Mermaid
Usar para diagramas de fluxo, attack surface maps.

---

## Versionamento

Cada artefato deve incluir:
```yaml
meta:
  version: 1.0
  created: YYYY-MM-DD
  author: claude-appsec
  skill: nome_da_skill
```
