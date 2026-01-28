# Skill: Secrets Review

**Propósito:** Detectar e orientar gestão de secrets, prevenir vazamentos.

---

## Entradas Esperadas

- [ ] Código fonte
- [ ] Arquivos de configuração
- [ ] Histórico git (se disponível)
- [ ] Scope definition

---

## Saídas

- `secrets_findings.md` - Secrets encontrados
- `secrets_recommendations.md` - Orientações de gestão

---

## Procedimento

1. **Escanear código** - Buscar padrões de secrets
2. **Verificar configs** - .env, config files
3. **Checar git history** - Commits antigos
4. **Identificar false positives** - Exemplos, placeholders
5. **Classificar severidade** - Por tipo e exposição
6. **Recomendar rotação** - Para secrets expostos
7. **Propor gestão** - Vault, env vars, etc.

---

## Padrões de Detecção

| Tipo | Pattern (Regex simplificado) |
|------|------------------------------|
| AWS Key | `AKIA[0-9A-Z]{16}` |
| AWS Secret | `[A-Za-z0-9/+=]{40}` após AWS context |
| GitHub Token | `ghp_[A-Za-z0-9]{36}` |
| Slack Token | `xox[baprs]-[A-Za-z0-9-]+` |
| Private Key | `-----BEGIN.*PRIVATE KEY-----` |
| JWT | `eyJ[A-Za-z0-9-_]+\.eyJ[A-Za-z0-9-_]+\.[A-Za-z0-9-_]+` |
| Generic API Key | `api[_-]?key.*[=:]["']?[A-Za-z0-9]{20,}` |
| Password | `password.*[=:]["']?.+` |
| Connection String | `(mysql|postgres|mongodb)://.*:.*@` |

---

## Locais a Verificar

| Local | Risco |
|-------|-------|
| Código fonte | Alto |
| .env (commitado) | Crítico |
| .env.example | Médio (se contém valores reais) |
| Config files | Alto |
| Docker/compose files | Alto |
| CI/CD configs | Alto |
| Git history | Crítico |
| Logs | Médio |
| Comments | Médio |

---

## Template de Finding

```markdown
## S-XXX: [Tipo de Secret]

**Severidade:** Critical|High|Medium|Low
**Arquivo:** path/to/file
**Linha:** XX
**Exposição:** code|config|history|logs

### Secret Encontrado
```
[REDACTED - tipo: AWS_KEY]
```

### Contexto
[Como está sendo usado]

### Risco
[O que pode ser acessado com este secret]

### Ação Imediata
1. Rotacionar o secret
2. Remover do código
3. Adicionar ao .gitignore
4. Limpar do histórico (se necessário)
```

---

## Classificação de Severidade

| Severidade | Critério |
|------------|----------|
| Critical | Credenciais de produção expostas |
| High | API keys com acesso amplo |
| Medium | Secrets de dev/test em código |
| Low | Placeholders que parecem reais |
| Info | Padrões suspeitos mas não confirmados |

---

## Recomendações de Gestão

### Por Ambiente

| Ambiente | Método Recomendado |
|----------|---------------------|
| Development | .env local + .env.example |
| CI/CD | Secrets do pipeline (GitHub Actions, etc) |
| Staging/Prod | Vault, AWS Secrets Manager, etc |

### Boas Práticas

1. **Nunca commitar secrets** - Usar .gitignore
2. **Rotacionar regularmente** - 90 dias para críticos
3. **Princípio do menor privilégio** - Escopo mínimo
4. **Audit logs** - Registrar uso de secrets
5. **Separação de ambientes** - Secrets diferentes por env

---

## Git History Check

```bash
# Buscar em todo histórico (opcional)
git log -p | grep -E "(password|secret|key|token)"

# Listar arquivos sensíveis removidos
git log --diff-filter=D --name-only | grep -E "(\.env|secret|key)"
```

---

## Critérios de Qualidade

- Nenhum secret em plaintext no output
- Falsos positivos identificados
- Recomendações de rotação claras
- Gestão de secrets proposta

---

## Token Budget

- **Sugerido:** 3K tokens
- **Máximo:** 6K tokens
- **Reduzir:** Agrupar por tipo, listar só críticos

---

## Fallbacks

| Situação | Ação |
|----------|------|
| Sem acesso ao git history | Notar limitação, focar em código atual |
| Muitos falsos positivos | Refinar patterns, pedir confirmação |
| Secret real encontrado | PARAR, alertar, não logar |
| Dúvida se é real | Tratar como real até confirmar |

---

## False Positives Comuns

| Pattern | Por que é FP |
|---------|--------------|
| `password123` | Placeholder óbvio |
| `YOUR_API_KEY` | Template |
| `sk_test_*` | Test key explícito |
| Strings em base64 | Pode ser dado, não secret |
