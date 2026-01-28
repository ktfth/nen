# Skill: Intake

**Propósito:** Coletar informações mínimas do projeto e autodetectar contexto técnico.

---

## Entradas Esperadas

- [ ] Nome/identificador do projeto
- [ ] URL do repositório (opcional)
- [ ] Objetivo da análise (review, audit, PR check)
- [ ] Prazo/urgência (se houver)
- [ ] Contato técnico responsável

---

## Saídas

- `intake_summary.md` - Resumo estruturado do projeto
- `detected_stack.yaml` - Stack técnica autodetectada

---

## Procedimento

1. **Perguntar o básico** - Nome, objetivo, prazo
2. **Autodetectar stack** - Ler package.json, requirements.txt, go.mod, etc.
3. **Identificar entry points** - main files, routes, endpoints
4. **Mapear estrutura** - Diretórios principais e convenções
5. **Listar dependências externas** - APIs, DBs, serviços
6. **Documentar lacunas** - O que não foi possível detectar
7. **Gerar summary** - Compilar em formato padrão

---

## Autodetecção por Arquivo

| Arquivo | Indica |
|---------|--------|
| package.json | Node.js |
| requirements.txt, Pipfile | Python |
| go.mod | Go |
| Cargo.toml | Rust |
| pom.xml, build.gradle | Java |
| Gemfile | Ruby |
| composer.json | PHP |
| Dockerfile | Container |
| .env.example | Env vars usadas |

---

## Template de Output

```yaml
# intake_summary.yaml
project:
  name: "[NOME]"
  objective: "[review|audit|pr_check]"
  deadline: "[YYYY-MM-DD ou N/A]"
  contact: "[nome/email]"

stack:
  languages: []
  frameworks: []
  databases: []
  external_services: []

structure:
  entry_points: []
  auth_module: "[path ou unknown]"
  api_routes: "[path ou unknown]"

gaps:
  - "[dado faltante 1]"
  - "[dado faltante 2]"

next_skill: "skill_scope_guardrails.md"
```

---

## Critérios de Qualidade

- Stack detectada com 80%+ de confiança
- Entry points identificados
- Nenhuma suposição não documentada
- Gaps claramente listados

---

## Token Budget

- **Sugerido:** 2K tokens
- **Máximo:** 4K tokens
- **Reduzir:** Focar em detecção automática, perguntas só para gaps

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Sem package manager | Buscar imports nos arquivos fonte |
| Repo vazio | Pedir upload de arquivos chave |
| Estrutura não padrão | Pedir descrição manual |
| Múltiplas linguagens | Perguntar qual é o foco |

---

## Perguntas Mínimas

Se autodetecção falhar, perguntar:

1. "Qual a linguagem/framework principal?"
2. "Onde está o código de autenticação?"
3. "Quais endpoints são públicos?"
