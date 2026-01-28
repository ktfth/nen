# Skill: Dependency Review

**Propósito:** Revisar dependências, gerar SBOM leve, identificar CVEs e riscos de licença.

---

## Entradas Esperadas

- [ ] Arquivos de dependências (package.json, requirements.txt, etc.)
- [ ] Scope definition
- [ ] Lock files quando disponíveis

---

## Saídas

- `dependency_report.md` - Relatório de dependências
- `sbom_lite.yaml` - SBOM simplificado
- `vulnerable_deps.yaml` - CVEs encontrados

---

## Procedimento

1. **Listar dependências** - Diretas e transitivas (se possível)
2. **Identificar versões** - Pinned vs ranges
3. **Verificar CVEs** - Em bases públicas
4. **Verificar licenças** - Compatibilidade e riscos
5. **Avaliar manutenção** - Última atualização, stars, issues
6. **Classificar risco** - Por severidade de CVE + criticidade de uso
7. **Recomendar atualizações** - Com breaking changes notes

---

## Fontes de CVE

| Fonte | Uso |
|-------|-----|
| NVD | Base principal |
| GitHub Advisory | Específico por ecossistema |
| Snyk DB | Alternativa |
| OSV | Open Source Vulnerabilities |

---

## Template de Report

```markdown
# Dependency Review: [Projeto]

**Data:** YYYY-MM-DD
**Total deps:** XX diretas, YY transitivas

## Sumário de Risco

| Risco | Quantidade |
|-------|------------|
| Critical CVE | X |
| High CVE | Y |
| Medium CVE | Z |
| Licença restritiva | W |
| Abandonadas | V |

## Dependências Críticas

### [nome-da-dep]
- **Versão atual:** X.Y.Z
- **Última versão:** A.B.C
- **CVEs:** CVE-XXXX-YYYY (High)
- **Licença:** MIT
- **Usado em:** auth, api
- **Ação:** Atualizar para A.B.C

## Licenças

| Licença | Deps | Risco |
|---------|------|-------|
| MIT | 45 | Baixo |
| Apache-2.0 | 12 | Baixo |
| GPL-3.0 | 2 | Alto (copyleft) |
| Unknown | 1 | Verificar |

## Dependências Abandonadas

| Dep | Último commit | Stars | Risco |
|-----|---------------|-------|-------|
| old-lib | 2020-01-01 | 50 | Médio |
```

---

## SBOM Lite YAML

```yaml
# sbom_lite.yaml
sbom:
  format: "sbom-lite-1.0"
  project: "[nome]"
  generated: "YYYY-MM-DD"

dependencies:
  direct:
    - name: express
      version: "4.18.2"
      license: MIT
      cves: []

    - name: lodash
      version: "4.17.21"
      license: MIT
      cves:
        - id: CVE-2021-23337
          severity: high
          fixed_in: "4.17.21"

  transitive:
    - name: debug
      version: "4.3.4"
      parent: express
      license: MIT
```

---

## Vulnerable Deps YAML

```yaml
# vulnerable_deps.yaml
vulnerabilities:
  - package: lodash
    version: "4.17.15"
    cve: CVE-2021-23337
    severity: high
    cvss: 7.2
    title: "Command Injection"
    fixed_in: "4.17.21"
    recommendation: "npm update lodash"

  - package: axios
    version: "0.21.0"
    cve: CVE-2021-3749
    severity: medium
    cvss: 5.3
    title: "ReDoS"
    fixed_in: "0.21.2"
    recommendation: "npm update axios"
```

---

## Licenças de Risco

| Licença | Risco | Motivo |
|---------|-------|--------|
| GPL-2.0/3.0 | Alto | Copyleft forte |
| AGPL-3.0 | Crítico | Copyleft para SaaS |
| SSPL | Alto | Restrições de uso |
| Proprietary | Crítico | Termos específicos |
| Unknown | Médio | Verificar manualmente |

---

## Critérios de Qualidade

- Todas as deps diretas catalogadas
- CVEs críticos/altos identificados
- Licenças mapeadas
- Recomendações são versionadas

---

## Token Budget

- **Sugerido:** 4K tokens
- **Máximo:** 8K tokens
- **Reduzir:** Focar em deps diretas, listar só CVEs High+

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Sem lock file | Alertar sobre versões flutuantes |
| CVE DB indisponível | Documentar, recomendar scan posterior |
| Muitas deps | Priorizar por criticidade de uso |
| Licença não identificada | Marcar para revisão manual |

---

## Comandos Úteis (Opcional)

```bash
# Node
npm audit --json

# Python
pip-audit --format json

# Go
govulncheck ./...

# Java
mvn dependency-check:check
```
