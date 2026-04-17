# CLAUDE.md Template

Use this template to generate the user's CLAUDE.md. Replace all `{{ }}` placeholders with actual values from the interview. Remove any sections that don't apply. Do not leave placeholder text in the final output.

---

```markdown
# CLAUDE.md — {{ PROJECT_NAME }}

> This file is read by Claude at the start of every session. Keep it up to date.
> Last updated: {{ DATE }}

---

## 🧭 Project Overview

**What we're building:** {{ ONE_SENTENCE_DESCRIPTION }}
**Problem it solves:** {{ PROBLEM_STATEMENT }}
**Target users:** {{ USER_PERSONA }}
**Current phase:** {{ PHASE — e.g. "MVP development", "Post-launch iteration", "Scaling" }}

---

## 🎯 Current Focus

<!-- UPDATE THIS every session — Claude reads this first -->
**Active sprint goal:** {{ CURRENT_SPRINT_GOAL }}
**Working on right now:** {{ CURRENT_TASK }}
**Blocked by:** {{ BLOCKERS_OR_NONE }}

---

## 🏗️ Tech Stack

| Layer | Choice | Notes |
|---|---|---|
| Frontend | {{ FRONTEND_FRAMEWORK }} | {{ REASON }} |
| Styling | {{ CSS_SOLUTION }} | |
| State | {{ STATE_MANAGEMENT }} | |
| Backend | {{ BACKEND_FRAMEWORK }} | |
| API Style | {{ REST / GraphQL / tRPC }} | |
| Database | {{ PRIMARY_DB }} | |
| Cache | {{ CACHE_OR_NONE }} | |
| Auth | {{ AUTH_SOLUTION }} | |
| Hosting | {{ HOSTING }} | |
| CI/CD | {{ CICD_OR_NONE }} | |
| Monitoring | {{ MONITORING_OR_NONE }} | |

---

## 📁 Project Structure

```
{{ PROJECT_NAME }}/
├── {{ FOLDER }}/ — {{ PURPOSE }}
├── {{ FOLDER }}/ — {{ PURPOSE }}
├── {{ FOLDER }}/ — {{ PURPOSE }}
└── ...
```

**Key files:**
- `{{ PATH }}` — {{ WHAT_IT_DOES }}
- `{{ PATH }}` — {{ WHAT_IT_DOES }}

---

## 👥 Team

| Role | Person | Owns |
|---|---|---|
| {{ ROLE }} | {{ NAME_OR_TBD }} | {{ DOMAIN }} |

---

## ✅ Coding Standards & Best Practices

- Language: **{{ LANGUAGE }}** — {{ TypeScript strict mode / Python type hints / etc. }}
- Formatter: {{ PRETTIER / BLACK / etc. }} — config at `{{ PATH }}`
- Linter: {{ ESLINT / RUFF / etc. }}
- Testing: {{ JEST / PYTEST / VITEST / etc. }} — aim for {{ COVERAGE_TARGET }}% coverage on core logic
- Commit style: {{ CONVENTIONAL COMMITS / etc. }}
- Branch strategy: {{ GITFLOW / TRUNK / etc. }}
- PR review: {{ REQUIRED / SOLO }}

**Naming conventions:**
- Components: PascalCase
- Functions/variables: camelCase
- DB columns: snake_case
- Env vars: SCREAMING_SNAKE_CASE
- {{ ANY_DOMAIN_SPECIFIC_CONVENTIONS }}

---

## 🧠 Domain Glossary

<!-- Short aliases so Claude never needs re-explanation -->
| Term | Meaning |
|---|---|
| {{ TERM }} | {{ DEFINITION }} |
| {{ TERM }} | {{ DEFINITION }} |

---

## 🚫 Out of Scope (v1)

The following are explicitly NOT being built in v1. Do not suggest, implement, or scaffold these:
- {{ OUT_OF_SCOPE_1 }}
- {{ OUT_OF_SCOPE_2 }}
- {{ OUT_OF_SCOPE_3 }}

---

## 🤖 Sub-Agent & Model Routing Rules

**Claude must always follow this multi-agent strategy:**

### Model Selection — which model to use for what

| Task Type | Model | When to use |
|---|---|---|
| Task decomposition | `claude-haiku-4-5` | Breaking a large request into subtasks, planning steps, generating checklists, routing work to sub-agents |
| Standard implementation | `claude-sonnet-4-5` | Writing code, fixing bugs, writing tests, reviewing PRs, standard completions |
| Complex problem solving | `claude-opus-4-5` | Architecture decisions, debugging hard issues, security audits, optimisation problems, anything requiring deep multi-step reasoning |

### Sub-Agent Rules

1. **Always decompose before executing.** For any task with > 2 distinct parts, spawn a Haiku sub-agent first to break it into a step list, then execute each step with Sonnet.
2. **Parallelise independent tasks.** If steps don't depend on each other (e.g. writing tests + writing docs), run them as parallel sub-agents — never sequentially when parallel is possible.
3. **Escalate to Opus explicitly.** Only invoke Opus when the task cannot be solved by Sonnet after one retry, or when the user explicitly asks for deep analysis. State why you're escalating.
4. **Sub-agent handoff format.** When spawning a sub-agent, always pass: (a) the relevant section of CLAUDE.md, (b) the specific task, (c) the expected output format. Never pass the full codebase.
5. **One sub-agent per concern.** Don't build a monolithic sub-agent. Split by: frontend / backend / tests / docs / infra.
6. **Report back concisely.** Sub-agents return results as structured diffs or bullet summaries — not prose explanations.

### Example Task Routing

```
User: "Build the user authentication flow"

→ Haiku: decompose into [DB schema, API routes, middleware, frontend form, tests]
→ Sonnet (parallel): API routes + DB schema
→ Sonnet (parallel): Frontend form + middleware  
→ Sonnet: Tests (after above complete)
→ Opus (if needed): Security review of the full auth flow
```

---

## ⚡ Claude Token Optimisation Rules

**Claude must follow these in every response:**

1. **Read only what's needed.** Never load or summarise files not relevant to the current task.
2. **Respond with diffs/patches**, not full file rewrites, unless explicitly asked for the full file.
3. **Bullet answers over prose.** Keep responses concise — no padding, no restating the question.
4. **Ask before building.** For any task estimated > 30 min, ask 1–2 clarifying questions first.
5. **Use `// CONTEXT:` comments** in code to explain non-obvious decisions inline.
6. **Reference this file** for stack and conventions — do not ask the user to re-explain them.
7. **Batch related changes.** Group edits to the same file in one response.
8. **Flag scope creep.** If a request would touch > 3 files or introduce a new dependency, say so before proceeding.
9. **No hallucinated features.** Only implement what's described in this file or explicitly requested.
10. **Check `## Current Focus`** before starting any session to pick up exactly where we left off.
11. **Use Haiku for cheap ops.** Routing, planning, summarising, and classification tasks must use Haiku — never burn Sonnet/Opus on these.
12. **Cap sub-agent context.** Each sub-agent receives only the CLAUDE.md sections + files it needs — never the full project context.
13. **Avoid redundant reads.** If a file was already read this session, reference it from memory — don't re-read unless it may have changed.
14. **Short-circuit on confidence.** If the answer is obvious from CLAUDE.md + current context, respond immediately — don't spawn agents or read files unnecessarily.

---

## 🔐 Security & Compliance

- Auth strategy: {{ AUTH_NOTES }}
- Sensitive data handling: {{ NOTES_OR_NONE }}
- Compliance requirements: {{ GDPR / HIPAA / NONE / etc. }}
- Secret management: {{ ENV_FILES / VAULT / etc. }} — never commit secrets

---

## 🌍 Environment Setup

```bash
# Clone and install
{{ SETUP_COMMANDS }}

# Environment variables required
{{ ENV_VAR_1 }}={{ DESCRIPTION }}
{{ ENV_VAR_2 }}={{ DESCRIPTION }}

# Run dev server
{{ DEV_COMMAND }}
```

---

## 📌 Key Decisions Log

<!-- Log major architectural decisions here with rationale -->
| Date | Decision | Reason | Alternatives considered |
|---|---|---|---|
| {{ DATE }} | {{ DECISION }} | {{ REASON }} | {{ ALTERNATIVES }} |

---

## 📎 Reference Links

- Repo: {{ REPO_URL }}
- Docs: {{ DOCS_URL_OR_NONE }}
- Design: {{ FIGMA_OR_NONE }}
- Project board: {{ JIRA_NOTION_LINEAR_OR_NONE }}

---

*This file is the single source of truth for this project. Update it when decisions change.*
*Companion file: `PROJECT_MEMORY.md` — updated each session by the project-memory skill.*
```
