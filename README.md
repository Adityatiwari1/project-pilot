# 🚀 Project Pilot — Claude Code Plugin

> Full-stack project architect + session memory manager for Claude Code.

Project Pilot runs a thorough 2-round interview before you write a single line of code, then generates two files that make every Claude Code session faster, cheaper, and context-aware:

- **`CLAUDE.md`** — master rules file with tech stack, team structure, sub-agent routing, model selection, and token optimisation rules
- **`PROJECT_MEMORY.md`** — living session memory that restores full project context instantly on every return session

---

## ✨ What it does

### First session — Architect Mode
- Runs a **15-question interview** across two rounds (vision → technical depth)
- Recommends **tech stack, team size, file structure, and best practices**
- Generates a complete `CLAUDE.md` with:
  - Sub-agent routing rules (Haiku for planning, Sonnet for coding, Opus for hard problems)
  - 14 token optimisation rules to reduce input + output costs
  - Domain glossary, out-of-scope list, decisions log, environment setup
- Generates initial `PROJECT_MEMORY.md`

### Every session after — Memory Mode
- Reads `PROJECT_MEMORY.md` and delivers a **6-line context summary** instantly
- Logs decisions, new dependencies, and architecture changes mid-session
- Writes a structured patch to `PROJECT_MEMORY.md` at session end

---

## 📦 Installation

### Step 1 — Add this marketplace to Claude Code

```bash
/plugin marketplace add Adityatiwari1/project-pilot
```

### Step 2 — Install the plugin

```bash
/plugin install project-pilot@project-pilot
```

### Step 3 — Reload plugins

```bash
/reload-plugins
```

That's it. The skill activates automatically when you say things like:
- *"I want to build X"*
- *"Help me plan my app"*
- *"What stack should I use?"*
- *"Where were we on my project?"*
- *"New session — continue the project"*

---

## 🗂️ Repo Structure

```
project-pilot/
├── .claude-plugin/
│   ├── plugin.json          ← Plugin manifest
│   └── marketplace.json     ← Marketplace catalog
├── skills/
│   └── project-pilot/
│       ├── SKILL.md         ← Full skill (Architect + Memory modes)
│       └── references/
│           └── claude-md-template.md  ← CLAUDE.md template
├── LICENSE
└── README.md
```

---

## 🤖 Model Routing (built into every generated CLAUDE.md)

| Task | Model |
|---|---|
| Task decomposition, planning, routing | `claude-haiku-4-5` |
| Code, tests, bug fixes, standard work | `claude-sonnet-4-5` |
| Architecture, deep debugging, security audits | `claude-opus-4-5` |

---

## ⚡ Token Optimisation

Every `CLAUDE.md` this plugin generates includes 14 hardcoded rules to minimise token usage — diffs over rewrites, selective file reads, Haiku for cheap ops, sub-agent context capping, and more.

---

## 📄 License

MIT
