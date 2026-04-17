---
name: pilot-init
description: >
  Initialize CLAUDE.md, PROJECT_MEMORY.md, and CODEBASE_GRAPH.md for an existing project.
  Activate when user says "pilot-init", "init my existing project", "setup claude.md for my codebase",
  "existing project setup", or runs /pilot-init. Scans the codebase automatically — no full interview.
  Asks only 3 questions. Always runs graph generation after file creation.
---

# Pilot Init — Existing Project Setup

Scan the existing codebase and generate CLAUDE.md + PROJECT_MEMORY.md + CODEBASE_GRAPH.md.
No full interview. Scan first, ask only what scanning cannot determine.

---

## Phase 1 — Codebase Scan

Scan the working directory to auto-detect:

| Signal | What to look for |
|---|---|
| Language / runtime | `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `*.csproj`, `pom.xml` |
| Frontend framework | `next.config.*`, `vite.config.*`, `angular.json`, `svelte.config.*`, `nuxt.config.*` |
| Backend framework | `express`, `fastapi`, `django`, `rails`, `gin`, `nestjs`, `hono` in deps |
| Database | ORM configs, migration folders, `prisma/`, `drizzle.config.*`, `alembic/` |
| Auth | `next-auth`, `clerk`, `supabase`, `passport`, `lucia` in deps |
| Infra / deploy | `Dockerfile`, `fly.toml`, `railway.toml`, `vercel.json`, `.github/workflows/` |
| Test setup | `jest.config.*`, `vitest.config.*`, `pytest.ini`, `*.test.*` presence |
| Monorepo | `pnpm-workspace.yaml`, `turbo.json`, `nx.json`, `lerna.json` |

After scan, show detected stack for confirmation:

```
🔍 Detected:
  Runtime: Node 20 / TypeScript
  Frontend: Next.js 14 (App Router)
  Styling: Tailwind CSS
  Backend: Next.js API routes
  DB: Prisma + PostgreSQL
  Auth: NextAuth v5
  Deploy: Vercel (vercel.json found)
  Tests: Vitest

Correct? Reply yes or correct anything before I generate files.
```

---

## Phase 2 — Minimal Interview (3 questions only)

Ask only what scanning cannot determine:

1. What's the core purpose of this project? (one sentence)
2. Who's working on it — solo or team? If team, rough size?
3. What's the current phase? (early prototype / active development / maintenance / pre-launch)

---

## Phase 3 — Generate CLAUDE.md

Populate from scan results + interview answers. Skip undetermined sections — mark `TBD`.

Always include:
- Detected stack (with versions where found)
- Existing folder structure (top 2 levels, annotated)
- Sub-Agent & Model Routing Rules:

```markdown
## 🤖 Sub-Agent & Model Routing Rules

| Task Type | Model | When to use |
|---|---|---|
| Task decomposition | claude-haiku-4-5 | Breaking requests into subtasks, planning, routing, checklists |
| Standard implementation | claude-sonnet-4-6 | Writing code, fixing bugs, tests, reviews, standard completions |
| Complex problem solving | claude-opus-4-7 | Architecture decisions, deep debugging, security audits, hard reasoning |

**Sub-agent rules:**
1. Decompose before executing. For any task with > 2 distinct parts, use Haiku to break it into steps first.
2. Parallelise independent tasks. Never run sequentially when parallel is possible.
3. Escalate to Opus only when Sonnet fails after one retry, or user explicitly asks. Always state why.
4. Each sub-agent receives only relevant CLAUDE.md sections + needed files — never the full codebase.
5. One sub-agent per concern: frontend / backend / tests / docs / infra.
6. Sub-agents return diffs or bullet summaries — never prose.
```

- Token Optimisation Rules:

```markdown
## ⚡ Claude Token Optimisation Rules

1. **Use CODEBASE_GRAPH.md for all file search.** Before reading any file or scanning any directory, check CODEBASE_GRAPH.md first. Use the Directory Map and Navigation Quick-Reference to locate files. Only read the source file once the exact path is known. Never glob or list directories when the graph exists.
2. Read only what's needed. Never load files irrelevant to the current task.
3. Respond with diffs/patches, not full file rewrites, unless explicitly asked.
4. Bullet answers over prose. No padding, no restating the question.
5. Ask before building. For any task > 30 min estimated, ask 1–2 clarifying questions first.
6. Use `// CONTEXT:` comments in code for non-obvious decisions — reduces re-explanation.
7. Reference CLAUDE.md for stack and conventions — never ask the user to repeat them.
8. Batch related changes. Group edits to the same file in one response.
9. Flag scope creep. If a request touches > 3 files or adds a dependency, say so first.
10. No hallucinated features. Only implement what's in this file or explicitly requested.
11. Check `## Current Focus` first every session.
12. Use Haiku for cheap ops. Routing, planning, summarising must use Haiku — not Sonnet/Opus.
13. Cap sub-agent context. Pass only needed sections, not the full project.
14. Avoid redundant reads. If a file was read this session, don't re-read unless it changed.
15. Short-circuit on confidence. If the answer is obvious from context, respond immediately.
```

---

## Phase 4 — Generate PROJECT_MEMORY.md

```markdown
# PROJECT_MEMORY.md — {{ PROJECT_NAME }}

> Read this at the start of every session. Update it at the end.
> Last updated: {{ DATE }} | Session #1

## 🔭 Project Snapshot
**Name:** {{ PROJECT_NAME }}
**One-liner:** {{ DESCRIPTION }}
**Stack:** {{ STACK_SUMMARY }}
**Phase:** {{ PHASE_FROM_INTERVIEW }}
**Started:** Unknown (existing project)
**Target launch:** TBD

## 🏁 Last Session ({{ DATE }})
**What we did:**
- CLAUDE.md and PROJECT_MEMORY.md initialised from existing codebase via /pilot-init
- CODEBASE_GRAPH.md generated

## ✅ Completed Milestones
| Date | Milestone | Notes |
|---|---|---|
| {{ DATE }} | Initial project setup | Existing project — detected via scan |

## 🔧 In Progress
| Task | Owner | Status | Notes |
|---|---|---|---|
| TBD | TBD | TBD | Fill in next session |

## 📌 Decisions Log
| Date | Decision | Reason | Impact |
|---|---|---|---|
| {{ DATE }} | {{ STACK_SUMMARY }} | Detected from existing codebase | Full project |

## 🚧 Blockers
None

## 🗺️ Next Session
**Top 3 tasks:**
1. TBD
2. TBD
3. TBD

## 🧩 Architecture Changes
None yet.

## 📦 Dependencies Added
None yet.

## 📋 Session History
| # | Date | Summary |
|---|---|---|
| 1 | {{ DATE }} | Existing project initialised. CLAUDE.md + PROJECT_MEMORY.md + CODEBASE_GRAPH.md generated. |
```

---

## Phase 5 — Generate CODEBASE_GRAPH.md

Run immediately after writing CLAUDE.md and PROJECT_MEMORY.md. Tell the user: *"Generating codebase graph..."*

Walk the working directory. Collect:

**Structure layer** — all dirs up to 4 levels deep, skip `node_modules`, `.git`, `dist`, `build`, `.next`, `__pycache__`, `venv`, `.turbo`, `coverage`. File counts per dir. Entry points: `main.*`, `index.*`, `app.*`, `server.*`.

**Dependency layer** — parse manifest files. Separate runtime / dev / peer deps.

**Module relationship layer** — static import analysis on top 2 levels. API route surface. DB models/schemas/migrations.

**Config layer** — env vars from `.env.example` (never `.env`). Build/deploy configs. Test setup.

Write `CODEBASE_GRAPH.md`:

```markdown
## 🤖 Claude Usage Instructions
When working in this codebase:
1. Read CODEBASE_GRAPH.md FIRST — use it to locate files before reading them.
2. Do NOT read entire directories. Use the Directory Map to identify the exact file.
3. Use the Navigation Quick-Reference to find where to add new code.
4. Re-read this file (not source files) when asked "where is X" or "how does Y work at high level".
5. Regenerate with `/graph` only after major structural refactors.

# CODEBASE_GRAPH.md — {{ PROJECT_NAME }}
> Auto-generated by project-pilot /pilot-init on {{ DATE }}
> Reference this file instead of re-reading source files for navigation and context.

## 📁 Directory Map
## 🚪 Entry Points
## 🔗 Module Dependency Map
## 🛣️ API / Route Surface
## 🗄️ Data Layer
## 📦 Key Dependencies
## 🌍 Environment Variables
## ⚙️ Build & Deploy
## 🧭 Navigation Quick-Reference
```

Populate each section from the scan.

---

## Phase 6 — Handoff

1. Confirm all 3 files written: `CLAUDE.md`, `PROJECT_MEMORY.md`, `CODEBASE_GRAPH.md`
2. Show 3 suggested next tasks based on detected project state
3. Tell the user: *"Paste CLAUDE.md at the start of every Claude Code session to restore context. CODEBASE_GRAPH.md will be used automatically for fast file search."*
