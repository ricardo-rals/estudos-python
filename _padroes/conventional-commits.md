# Conventional Commits

> Especificação oficial: <https://www.conventionalcommits.org/pt-br/v1.0.0/>


---

## Por que adota

- **CHANGELOG vira `git log` formatado** - sem escrever release notes na mão
- **SemVer (Semantic Versioning) automático** - `feat:` -> minor, `fix:` -> patch, `BREAKING CHANGE` -> major
- **Histórico legível** -  você entende o seu próprio commit no futuro
- **Ferramentas (`git-cliff`, `semantic-release`, `commitizen`) leem isso direto**

---

## Estrutura
```
<tipo>(<escopo opcional>)<! se breaking>: <descrição curta, imperativo>

<corpo opcional explicando "por quê", não "o quê">

<rodapé opcional: BREAKING CHANGE / Refs / Closes>
```

**Regras**
- Descrição curta, 72 caracteres ou menos, imperativo ("adiciona", não "adicionei")
- Sem ponto final na primeira linha
- Linha em branco entre primeira linha, corpo e rodapé




| Tipo | Quando usar | Bump SemVer |
|---|---|---|
| `feat` | Nova funcionalidade | **minor** |
| `fix` | Correção de bug | **patch** |
| `docs` | Apenas documentação (README, ADR, comentáriios) | nenhum |
| `style` | Formatação, ponto-vírgula, lint - sem mudar lógica | nenhum |
| `refactor` | Mudança que não adiciona feature e nem corrige bug | nenhum |
| `perf` | Melhora performance | **minor** |
| `build` | Mudança em build system (Dockerfile, pyproject, deps) | nenhum |
| `ci` | Mudança em arquivos de CI(.github/workflows, Jenkingsfile) | nenhum |
| `chore` | Manutenção que não cabe acima(rename de pasta, lockfile) | nenhum |
| `revert` | Reverte commit anterior | conforme commit revertido |

**Breaking change:** adicione `!` antes do `:` ou rodapé `BREAKING CHANGE:` no corpo. -> Bump **major**.

---

## 12 exemplos PT-BR (copia e cola)

```
feat(P01): adiciona endpoint GET /health retornando status 200

Implementa requisito RF03 do P01. Útil para healthcheck do Docker
Compose e para CI verificar se container está pronto.
```

```
fix(P05A): corrige loop infinito no scraper quando paginação retorna 404

Antes: ao bater 404, scraper repetia mesma URL indefinidamente.
Agora: detecta 404, registra no log e move para próxima licitação.

Closes #12
```

```
docs(padroes): adiciona security-checklist-owasp mapeado para FastAPI
```

```
refactor(PFB): extrai lógica de chunking para application/chunker.py

Permite testar unitariamente sem subir DB. Comportamento idêntico.
Ver ADR-0007 para alternativas consideradas.
```

```
perf(P06B): reduz P95 da rota /search de 820ms para 240ms

Aplica DataLoader para evitar N+1 em SQLAlchemy ao carregar acórdãos
relacionados. Benchmark com locust em docs/perf/p06b-baseline.md.
```

```
test(P03): adiciona property-based tests com Hypothesis para parser de datas

Cobre 1000 casos gerados automaticamente. Encontrou edge case de
ano bissexto em fevereiro 29 (corrigido em commit anterior).
```

```
build(P04): atualiza SQLAlchemy de 2.0.30 para 2.0.40

Mudança não-breaking. Inclui correção de bug em async session.
```

```
ci(P08A): habilita cache de pip no GitHub Actions

Reduz tempo de pipeline de 4m20s para 1m45s.
```

```
chore: renomeia pasta `base/` para `fundamentos/` no README

Alinha README com estrutura real do disco. Sem mudança em código.
```

```
feat(T02)!: troca transport stdio para HTTP/SSE como padrão

BREAKING CHANGE: Servidor MCP agora inicia em HTTP por padrão.
Para manter stdio, passar --transport=stdio.
Migration: ajustar config do Claude Desktop conforme docs/mcp/migration.md.
```

```
revert: reverte "feat(P05B): adiciona embedding via Cohere"

Cohere saiu do free tier em 2026-05. Voltando para OpenAI até
T01 entregar Ollama como alternativa local.

Reverts: a3f9c12
```

```
fix(D04)!: troca região OCI de sa-saopaulo-1 para us-ashburn-1

BREAKING CHANGE: Always-Free com 4 OCPUs Ampere ARM só está
disponível em us-ashburn-1 e us-phoenix-1. São Paulo retorna
"out of capacity" há meses (validado 2026-05).

Refs: ADR-0003 (escolha de região OCI)
```

---

## Integração com SemVer e CHANGELOG

```
commits desde última tag    →    git-cliff (free)    →    CHANGELOG.md atualizado
                                                     →    próxima versão SemVer sugerida
```

Comando local (após instalar `git-cliff`):

```powershell
git-cliff --output CHANGELOG.md
```

Em CI, ver [`changelog-keepachangelog.md`](./changelog-keepachangelog.md).

---

## Hooks locais (opcional, recomendado)

Instale [`commitizen`](https://commitizen-tools.github.io/commitizen/) ou [`commitlint`](https://commitlint.js.org/) com pre-commit:

```yaml
# .pre-commit-config.yaml
- repo: https://github.com/commitizen-tools/commitizen
  rev: v3.29.0
  hooks:
    - id: commitizen
      stages: [commit-msg]
```

Tentativa de commit fora do padrão é **bloqueada** — sem cair em `git rebase` infinito depois.

---

## Anti-padrões a evitar

| ❌ | ✅ |
|---|---|
| `commit -m "ajuste"` | `fix(P02): corrige timeout em chamadas paralelas` |
| `commit -m "WIP"` | (não commite WIP em main; use branch) |
| `feat: implementei X` (passado) | `feat: adiciona X` (presente) |
| `feat: Adicionei novo endpoint.` (ponto final, capitalizado) | `feat: adiciona endpoint /search` |
| `fix: bug` (sem escopo nem detalhe) | `fix(P05B): corrige NPE quando query vazia` |
| 3 mudanças não-relacionadas em 1 commit | 3 commits separados |

---

> **Validado em:** 2026-05. Conventional Commits 1.0 estável desde 2019. SemVer também — risco de mudança = baixo.



