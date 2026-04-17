---
name: pilot-architect
description: >
  Full-stack project architect for new projects. Activate when user wants to start any software
  project, app, API, SaaS, or tool — even if they say "I want to build X", "help me plan my app",
  "what stack should I use", "generate a CLAUDE.md", "let's start a project", "help me architect this",
  or "new project". Runs a thorough 2-round interview (15 questions), recommends tech stack, team
  size, file structure, and best practices, then generates CLAUDE.md + PROJECT_MEMORY.md +
  CODEBASE_GRAPH.md. Never skip the interview.
---

# Pilot Architect — New Project Setup

Run a full architecture interview, then generate CLAUDE.md, PROJECT_MEMORY.md, and CODEBASE_GRAPH.md.
Never generate files before completing both interview rounds and getting user confirmation.

---

## Phase 1 — Interview

Run in two rounds. Never dump all questions at once — keep it conversational.

### Round 1 — Project Vision

1. What are you building? Describe it in plain language.
2. Who are the end users, and what's the core problem it solves?
3. Target platform? (web, mobile, desktop, CLI, API-only, or combination)
4. Have you worked on something similar before, or is this a new domain?
5. Your role — solo developer, team lead, co-founder?
6. Preferred tech stack, or open to recommendations?
7. Timeline? (MVP in weeks, or longer-haul product?)

Wait for answers. Acknowledge them. Then proceed to Round 2.

### Round 2 — Technical Depth

8. Expected scale at launch vs. 12 months? (users, requests/sec, data volume)
9. Do you need auth, payments, real-time features, file uploads, or third-party integrations?
10. Hard constraints? (specific cloud provider, language, existing codebase)
11. Team size and skill levels? (if solo, what are your strongest areas?)
12. Budget for infra, or must stay on free tiers to start?
13. Mobile responsiveness, native mobile, or PWA?
14. Compliance or security requirements? (HIPAA, GDPR, SOC2, etc.)
15. What does "done" look like for v1? What's explicitly out of scope?

After both rounds, **summarise your understanding** and ask user to confirm before generating any file.

---

## Phase 2 — Architecture Decisions

Based on interview answers, decide and justify:

### Tech Stack
- **Frontend**: framework, styling, state management, component library
- **Backend**: runtime, framework, API style (REST / GraphQL / tRPC)
- **Database**: primary DB, cache, search if needed
- **Auth**: Clerk / NextAuth / Supabase Auth / custom JWT
- **Infrastructure**: hosting, CDN, storage, CI/CD
- **Monitoring**: error tracking, logging, analytics

**Rules:**
- Default to boring, proven tech unless scale justifies otherwise
- Prefer integrated platforms (Vercel + Next.js, Supabase, Railway) for solo/small teams
- Always recommend TypeScript over JavaScript for any non-trivial project
- Flag choices that add complexity without proportional value

### Team Structure
- Solo: what to outsource or use managed services for
- 2–5: how to split responsibilities
- 5+: team topology, domain ownership

### File Structure
Generate an opinionated, annotated folder structure matching the chosen stack.

---

## Phase 3 — Generate CLAUDE.md

Use the template at `references/claude-md-template.md`. Fill every section from interview answers.

Always include these two sections verbatim:

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

**Example:**
User: "Build the user authentication flow"
→ Haiku: decompose → [DB schema, API routes, middleware, frontend form, tests]
→ Sonnet (parallel): API routes + DB schema
→ Sonnet (parallel): frontend form + middleware
→ Sonnet: tests (after above complete)
→ Opus (if needed): security review
```

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
**Phase:** MVP Development
**Started:** {{ DATE }}
**Target launch:** {{ LAUNCH_DATE_OR_TBD }}

## 🏁 Last Session ({{ DATE }})
**What we did:**
- Project architecture defined
- CLAUDE.md generated
- PROJECT_MEMORY.md initialised
- CODEBASE_GRAPH.md generated

## ✅ Completed Milestones
| Date | Milestone | Notes |
|---|---|---|
| {{ DATE }} | Project architecture defined | Via /pilot-architect |

## 🔧 In Progress
| Task | Owner | Status | Notes |
|---|---|---|---|
| Initial project setup | {{ LEAD }} | Not started | |

## 📌 Decisions Log
| Date | Decision | Reason | Impact |
|---|---|---|---|
| {{ DATE }} | {{ STACK_CHOICE }} | {{ REASON }} | Full project |

## 🚧 Blockers
None

## 🗺️ Next Session
**Top 3 tasks:**
1. Set up repo and install dependencies
2. Configure environment variables
3. Scaffold folder structure per CLAUDE.md

## 🧩 Architecture Changes
None yet.

## 📦 Dependencies Added
None yet.

## 📋 Session History
| # | Date | Summary |
|---|---|---|
| 1 | {{ DATE }} | Project architected. CLAUDE.md + PROJECT_MEMORY.md + CODEBASE_GRAPH.md generated. |
```

---

## Phase 5 — Generate CODEBASE_GRAPH.md

Run immediately after PROJECT_MEMORY.md. Tell the user: *"Generating codebase graph..."*

At this stage the project is new so the graph will be minimal — scaffold structure only. Walk any files already created. Document:
- Planned folder structure from Phase 2
- Entry points if scaffold exists
- Dependencies from package.json / manifest if created
- Env vars from .env.example if created

Write `CODEBASE_GRAPH.md` with all standard sections. Mark undetermined sections as `TBD — populate after scaffold`.

Prepend:
```markdown
## 🤖 Claude Usage Instructions
When working in this codebase:
1. Read CODEBASE_GRAPH.md FIRST — use it to locate files before reading them.
2. Do NOT read entire directories. Use the Directory Map to identify the exact file.
3. Use the Navigation Quick-Reference to find where to add new code.
4. Re-read this file (not source files) when asked "where is X" or "how does Y work at high level".
5. Update this file with /graph after scaffold is complete.
```

---

## Phase 6 — Handoff

1. Summarise architecture decisions (3–5 bullets)
2. Give next 3 actions to start building immediately
3. Tell the user: *"Paste CLAUDE.md at the start of every Claude Code session."*
4. Tell the user: *"Run `/pilot-memory` at each session start to restore context. Say 'wrap up' at end to save it."*
5. Tell the user: *"Run `/graph` after scaffolding to rebuild the codebase map with real files."*
