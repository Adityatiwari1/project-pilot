---
name: project-pilot
description: >
  Project Pilot command reference. Shows all available commands and what they do.
  Activate when user says "project pilot help", "what commands does project pilot have",
  "pilot help", or is unsure which command to run.
---

# Project Pilot — Command Reference

| Command | When to use |
|---|---|
| `/pilot-architect` | Starting a brand-new project — full 15-question interview, generates CLAUDE.md + PROJECT_MEMORY.md + CODEBASE_GRAPH.md |
| `/pilot-init` | Existing codebase with no CLAUDE.md — scans project, asks 3 questions, generates all 3 files |
| `/pilot-memory` | Restore session context from PROJECT_MEMORY.md, or create one with 5 quick questions |
| `/graph` | Generate or update CODEBASE_GRAPH.md — the codebase map Claude uses for fast file search |

## Which one do I need?

```
New project, starting from scratch?
  → /pilot-architect

Existing codebase, never set up project-pilot before?
  → /pilot-init

Returning to a project that already has PROJECT_MEMORY.md?
  → /pilot-memory

Codebase structure changed and graph is stale?
  → /graph
```
