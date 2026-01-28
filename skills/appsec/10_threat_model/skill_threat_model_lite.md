# Skill: Threat Model Lite

**Propósito:** Threat modeling rápido usando STRIDE simplificado para identificar ameaças prioritárias.

---

## Entradas Esperadas

- [ ] Intake summary
- [ ] Scope definition
- [ ] Diagrama de arquitetura (opcional)
- [ ] Lista de atores/roles

---

## Saídas

- `threat_model.md` - Documento de ameaças identificadas
- `threats_prioritized.yaml` - Lista priorizada para análise

---

## Procedimento

1. **Identificar atores** - Quem interage com o sistema
2. **Mapear fluxos de dados** - Entrada, processamento, saída
3. **Aplicar STRIDE** - Por componente/fluxo crítico
4. **Priorizar ameaças** - Por likelihood × impact
5. **Definir mitigações** - Existentes e necessárias
6. **Gerar output** - Documento estruturado

---

## STRIDE Simplificado

| Categoria | Pergunta-Chave | Exemplo |
|-----------|----------------|---------|
| **S**poofing | Alguém pode fingir ser outro? | Forjar token de autenticação |
| **T**ampering | Dados podem ser alterados? | Modificar preço no carrinho |
| **R**epudiation | Ações podem ser negadas? | Sem logs de transações |
| **I**nfo Disclosure | Dados vazam indevidamente? | Stack trace com senhas |
| **D**enial of Service | Serviço pode ser derrubado? | Endpoint sem rate limit |
| **E**levation | Privilégios podem ser escalados? | User vira admin |

---

## Template de Threat Model

```markdown
# Threat Model: [Projeto]

**Data:** YYYY-MM-DD
**Versão:** 1.0

## Atores
| Actor | Trust Level | Capabilities |
|-------|-------------|--------------|
| Anonymous | None | HTTP requests |
| User | Low | Authenticated actions |
| Admin | High | Full access |

## Fluxos Críticos
1. **Login** - User → API → DB
2. **Payment** - User → API → Payment Gateway
3. **Data Export** - Admin → API → Storage

## Ameaças Identificadas

### T-001: [Título]
- **STRIDE:** Spoofing
- **Componente:** Auth module
- **Descrição:** [breve]
- **Likelihood:** Likely
- **Impact:** Major
- **Risco:** High
- **Mitigação Existente:** [se houver]
- **Mitigação Necessária:** [recomendação]

### T-002: [Título]
...

## Priorização

| ID | Título | Risco | Prioridade |
|----|--------|-------|------------|
| T-001 | ... | High | 1 |
| T-002 | ... | Medium | 2 |
```

---

## Output YAML

```yaml
# threats_prioritized.yaml
threats:
  - id: T-001
    title: "[título]"
    stride: spoofing
    component: auth
    risk: high
    priority: 1
    requires_skill:
      - skill_authz_authn_checks.md

  - id: T-002
    title: "[título]"
    stride: injection
    component: api
    risk: medium
    priority: 2
    requires_skill:
      - skill_injection_checks.md
```

---

## Critérios de Qualidade

- Todos os fluxos críticos cobertos
- Pelo menos 1 ameaça por categoria STRIDE relevante
- Priorização baseada em risco, não feeling
- Mitigações são actionable

---

## Token Budget

- **Sugerido:** 4K tokens
- **Máximo:** 8K tokens
- **Reduzir:** Focar em top 5 ameaças, tabelas em vez de prosa

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Sem diagrama | Inferir do código/routes |
| Atores indefinidos | Assumir: anon, user, admin |
| Sistema complexo | Dividir por módulo |
| Tempo curto | STRIDE só em auth + data |

---

## Quick STRIDE Checklist

Para cada componente crítico:
- [ ] S: Auth pode ser bypassada?
- [ ] T: Inputs são validados?
- [ ] R: Ações são logadas?
- [ ] I: Erros expõem dados?
- [ ] D: Rate limits existem?
- [ ] E: Roles são enforçados?
