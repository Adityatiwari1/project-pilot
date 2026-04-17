<div align="center">
  <img src="assets/project-pilot.png" alt="Project Pilot" width="180" />

  # Project Pilot  Claude Code Plugin

  > Full-stack project architect · session memory manager · codebase navigator

  [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
</div>

---

Project Pilot handles the work that happens *before* you write code and *between* every session  architecture decisions, CLAUDE.md generation, session memory, and a live codebase graph that makes file search fast and token-cheap.

---

## ✨ Commands

### `/pilot-architect`  new projects
Trigger: *"I want to build X"*, *"help me plan my app"*, *"what stack should I use"*

- Runs a **15-question interview** in two rounds (vision → technical depth)
- Recommends tech stack, team size, folder structure, and best practices
- Generates `CLAUDE.md` with sub-agent routing rules and 15 token optimisation rules
- Generates `PROJECT_MEMORY.md` for session continuity
- **Auto-runs `/graph`** immediately after to map the new project

### `/pilot-memory`  session restore + memory management
Trigger: *"where were we"*, *"continue"*, *"new session"*, or `PROJECT_MEMORY.md` found

- Reads `PROJECT_MEMORY.md` and delivers a **6-line context summary** instantly
- Logs decisions, new dependencies, and schema changes mid-session
- Patches only changed sections at session end  no full rewrites
- Updates `CODEBASE_GRAPH.md` immediately whenever a new file, route, model, or dependency is added
- If no `PROJECT_MEMORY.md` exists: runs **5-question create flow** instead

### `/pilot-init`  existing codebases
Trigger: `/pilot-init`, *"init my project"*, *"setup claude.md for existing project"*

- Scans the codebase to auto-detect stack, framework, ORM, auth, infra, and test setup
- Asks only **3 targeted questions** (what scanning can't answer)
- Generates `CLAUDE.md` + `PROJECT_MEMORY.md` from real project state
- **Auto-runs `/graph`** after to produce a full codebase map

### `/graph`  codebase map
Trigger: `/graph`, *"map codebase"*, *"graph my project"*  or **auto-runs after `/pilot-architect` and `/pilot-init`**

- Deep-scans the working directory (up to 4 levels, skips build/cache dirs)
- Generates `CODEBASE_GRAPH.md` with:
  - Annotated directory map
  - Module dependency relationships
  - Full API / route surface table
  - Data layer (models, migrations)
  - Key dependencies with versions
  - Environment variable reference
  - Navigation quick-reference ("where do I add X?")
- **Claude reads this before touching any source file**  never globs directories when the graph exists
- Updates incrementally mid-session whenever files, routes, models, or dependencies change

### `/project-pilot`  help
Lists all commands and which one to use for your situation.

---

## 📦 Installation

### Step 1  Add this marketplace to Claude Code

```bash
/plugin marketplace add Adityatiwari1/project-pilot
```

### Step 2  Install the plugin

```bash
/plugin install project-pilot@project-pilot
```

### Step 3  Reload plugins

```bash
/reload-plugins
```

---

## 🚀 Quick Start

| Situation | Command |
|---|---|
| Starting a new project | `/pilot-architect` |
| Returning to existing work | `/pilot-memory` |
| Existing codebase, no setup | `/pilot-init` |
| Map an existing codebase | `/graph` |
| Need help choosing | `/project-pilot` |

---

## 🗂️ Repo Structure

```
project-pilot/
├── assets/
│   └── project-pilot.png              ← Plugin logo
├── .claude-plugin/
│   ├── plugin.json                    ← Plugin manifest
│   └── marketplace.json               ← Marketplace catalog
├── skills/
│   ├── pilot-architect/
│   │   └── SKILL.md                   ← /pilot-architect command
│   ├── pilot-init/
│   │   └── SKILL.md                   ← /pilot-init command
│   ├── pilot-memory/
│   │   └── SKILL.md                   ← /pilot-memory command
│   ├── graph/
│   │   └── SKILL.md                   ← /graph command
│   └── project-pilot/
│       ├── SKILL.md                   ← /project-pilot help index
│       └── references/
│           └── claude-md-template.md
├── LICENSE
└── README.md
```

---

## 🤖 Model Routing (built into every generated CLAUDE.md)

| Task | Model |
|---|---|
| Task decomposition, planning, routing | `claude-haiku-4-5` |
| Code, tests, bug fixes, standard work | `claude-sonnet-4-6` |
| Architecture, deep debugging, security audits | `claude-opus-4-7` |

---

## ⚡ Token Optimisation

Every `CLAUDE.md` this plugin generates includes 15 hardcoded rules. Key ones:

- **Graph-first file search**  Claude reads `CODEBASE_GRAPH.md` before touching any source file
- Diffs over full rewrites
- Haiku for cheap ops (planning, routing, summarising)
- Sub-agent context capping  agents get only the sections they need
- Selective file reads  never load files irrelevant to the current task

---

## 📄 License

MIT
