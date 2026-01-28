# Skill: Handoff

**Propósito:** Transferir resultados para engenharia/ops com contexto necessário.

---

## Entradas Esperadas

- [ ] Relatório final
- [ ] Findings pendentes
- [ ] Plano de correção

---

## Saídas

- `handoff_package.md` - Pacote de handoff
- `action_items.md` - Ações por equipe

---

## Procedimento

1. **Preparar pacote** - Compilar artefatos
2. **Mapear responsáveis** - Por finding/ação
3. **Criar tickets** - Se solicitado
4. **Documentar acordos** - Próximos passos

---

## Handoff Package Template

```markdown
# Handoff Package

**Projeto:** [nome] | **Data:** YYYY-MM-DD
**De:** Security | **Para:** Engineering/DevOps

## Documentos
- security_report.md, findings.yaml, fix_plan.md

## Ações Críticas
| # | Ação | Responsável | SLA |
|---|------|-------------|-----|
| 1 | Fix SQLi F-001 | Backend | 3d |
| 2 | Add CSP | DevOps | 7d |

## Contatos
Security: [email] | Engineering: [email]
```

---

## Action Items por Equipe

```markdown
## Backend (SLA: 3d)
| Finding | Ação | Arquivo |
|---------|------|---------|
| F-001 | Parameterize query | auth.py:42 |

## Frontend (SLA: 14d)
| Finding | Ação | Arquivo |
|---------|------|---------|
| F-002 | Sanitize output | Comment.jsx:23 |

## DevOps (SLA: 7d)
| Finding | Ação | Config |
|---------|------|--------|
| F-004 | Security headers | nginx.conf |
```

---

## Template de Ticket

```markdown
# [F-XXX] [Título]

**Tipo:** Security Fix | **SLA:** X dias

## Descrição
[vulnerabilidade]

## Localização
Arquivo: `path/to/file`, Linha: XX

## Correção Sugerida
[código]

## Aceite
- [ ] Vuln não reproduzível
- [ ] Testes adicionados
- [ ] Code review ok
```

---

## Reunião de Handoff

**Agenda:** Overview (5min) → Findings (15min) → PoCs (10min) → Plan (10min) → Q&A (10min)

**Participantes:** Security Lead, Tech Leads, DevOps Lead

---

## Acordos

```markdown
## Responsabilidades
- Engineering: Fixes | Security: Validação | DevOps: Deploy

## Cronograma
| Milestone | Data | Owner |
|-----------|------|-------|
| Critical fixes | [data] | Backend |
| Validação | [data] | Security |

## Comunicação
Canal: [slack] | Escalação: [processo]
```

---

## Critérios de Qualidade

- Todas as ações têm owner
- SLAs realistas
- Próximos passos claros

---

## Token Budget

- **Sugerido:** 4K tokens | **Máximo:** 8K tokens

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Equipe indisponível | Async handoff |
| Sem ticket system | Documento + email |
| Dúvidas técnicas | Sessão de pairing |

---

## Pós-Handoff

- [ ] Check semanal em críticos
- [ ] Validação quando fix ready
- [ ] Report final pós-correção

| Métrica | Target |
|---------|--------|
| Críticos em SLA | 100% |
| Reopen rate | < 10% |
