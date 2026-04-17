---
name: pilot-memory
description: >
  Session memory manager for Project Pilot. Two behaviours depending on state:
  (1) RESTORE — if PROJECT_MEMORY.md exists, activate when user says "continue", "where were we",
  "new session", "restore context", or starts a session on a project that has PROJECT_MEMORY.md.
  Delivers a 6-line context summary instantly and manages mid-session logging + session-end updates.
  (2) CREATE — if no PROJECT_MEMORY.md exists, activate when user says "memory only", "just create
  memory file", "create project memory", "pilot-memory". Asks 5 quick questions then writes the file.
  Never run the interview if PROJECT_MEMORY.md already exists.
---

# Pilot Memory — Session Context Manager

Two modes. Check for `PROJECT_MEMORY.md` first — that determines which path to take.

---

## On Activation — Check File State

```
Does PROJECT_MEMORY.md exist in the working directory?
  YES → Run: RESTORE PROTOCOL
  NO  → Run: CREATE PROTOCOL
```

---

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
# RESTORE PROTOCOL
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Session Start

1. Read `PROJECT_MEMORY.md` — load only: `## Project Snapshot`, `## Last Session`, `## In Progress`, `## Next Session`
2. Also check if `CODEBASE_GRAPH.md` exists — if missing, note it for user
3. Deliver context in this exact format:

```
📦 Project: {{ NAME }} | Phase: {{ PHASE }}
🎯 Last session: {{ WHAT_WAS_DONE }}
✅ Completed: {{ MILESTONE_COUNT }} milestones
🔧 In progress: {{ CURRENT_TASKS }}
🚧 Blockers: {{ BLOCKERS_OR_NONE }}
📌 Next up: {{ PLANNED_NEXT }}
```

4. If `CODEBASE_GRAPH.md` missing: *"No CODEBASE_GRAPH.md found — run `/graph` for fast file search."*
5. If `## Last Session` is > 7 days old: *"Last session was {{ N }} days ago — confirm current state before we continue?"*
6. Ask: *"Anything change since last session before we continue?"*
7. If yes — update memory immediately before starting work.

---

## Memory Token Rules

- Session start: read only `## Project Snapshot`, `## Last Session`, `## In Progress`, `## Next Session`
- Mid-session log: append to relevant section only — never rewrite the full file
- Session end: output only changed sections as a patch
- "What have we built?" → use `## Completed Milestones` + `## Session History` only
- "What's next?" → use `## Next Session` + `## In Progress` only
- File search: always check `CODEBASE_GRAPH.md` before reading source files or scanning directories

---

## Mid-Session: Log PROJECT_MEMORY.md Immediately When

- A new dependency is added
- Database schema changes
- A major feature is completed
- A tech decision is reversed or changed
- Team size or roles change
- A deadline shifts
- An external integration is added or removed

## Mid-Session: Patch CODEBASE_GRAPH.md Immediately When

Any of these happen — patch immediately, not at session end:

| Event | Section to patch |
|---|---|
| New file or directory created | Directory Map |
| File deleted or renamed/moved | Directory Map |
| New route or API endpoint added | API / Route Surface |
| New DB model or migration created | Data Layer |
| New package installed | Key Dependencies |
| New component/module/lib scaffolded | Directory Map + Navigation Quick-Reference |

**Patch rules:**
- Do NOT regenerate the full graph — edit only the affected section
- Append to top of `CODEBASE_GRAPH.md`: `> Last updated: {{ DATE }} — {{ what changed }}`

---

## Session End Protocol

When user says "done for today", "wrap up", "update memory", or session goes quiet:

1. Diff the session — what was built, decided, changed, or abandoned
2. Update only changed sections in `PROJECT_MEMORY.md`:
   - `## Last Session` — overwrite with today
   - `## Completed Milestones` — append new completions
   - `## In Progress` — update statuses
   - `## Decisions Log` — append decisions made
   - `## Blockers` — add new, remove resolved
   - `## Next Session` — write specific next 3 tasks
   - `## Session History` — append one-line entry
   - Update `Session #` counter in header
3. Verify `CODEBASE_GRAPH.md` reflects all structural changes made this session — patch any missed
4. Show diff summary of what changed in both files
5. Remind: *"PROJECT_MEMORY.md updated for session #{{ N }}. Run `/pilot-memory` at the start of your next session."*

---

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
# CREATE PROTOCOL
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Phase 1 — Quick Context Capture

Ask all 5 questions at once:

```
I'll create your PROJECT_MEMORY.md with 5 quick questions:

1. Project name?
2. One-sentence description of what it does?
3. Current phase? (prototype / active dev / maintenance / pre-launch / launched)
4. What did you most recently work on or complete?
5. What are the top 3 tasks for the next session?
```

Wait for all answers. Do not ask follow-ups.

---

## Phase 2 — Generate PROJECT_MEMORY.md

```markdown
# PROJECT_MEMORY.md — {{ PROJECT_NAME }}

> Read this at the start of every session. Update it at the end.
> Last updated: {{ DATE }} | Session #1

## 🔭 Project Snapshot
**Name:** {{ PROJECT_NAME }}
**One-liner:** {{ DESCRIPTION }}
**Stack:** Unknown
**Phase:** {{ PHASE }}
**Started:** Unknown
**Target launch:** TBD

## 🏁 Last Session ({{ DATE }})
**What we did:**
- {{ ANSWER_4 }}

## ✅ Completed Milestones
| Date | Milestone | Notes |
|---|---|---|
| {{ DATE }} | PROJECT_MEMORY.md initialised | Via /pilot-memory |

## 🔧 In Progress
| Task | Owner | Status | Notes |
|---|---|---|---|
| TBD | TBD | TBD | |

## 📌 Decisions Log
| Date | Decision | Reason | Impact |
|---|---|---|---|

## 🚧 Blockers
None

## 🗺️ Next Session
**Top 3 tasks:**
1. {{ TASK_1 }}
2. {{ TASK_2 }}
3. {{ TASK_3 }}

## 🧩 Architecture Changes
None yet.

## 📦 Dependencies Added
None yet.

## 📋 Session History
| # | Date | Summary |
|---|---|---|
| 1 | {{ DATE }} | PROJECT_MEMORY.md initialised via /pilot-memory. |
```

---

## Phase 3 — Handoff

Confirm file written. Tell the user:
*"PROJECT_MEMORY.md created. Run `/pilot-memory` at the start of each session to restore context. Say 'wrap up' at session end to save it."*

If no `CLAUDE.md` found → suggest: *"Run `/pilot-init` to also generate CLAUDE.md and a codebase graph for this project."*
If no `CODEBASE_GRAPH.md` found → suggest: *"Run `/graph` to generate a codebase map for fast file search."*
