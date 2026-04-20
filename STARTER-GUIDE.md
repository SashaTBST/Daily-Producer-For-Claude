# PRODUCER AI — STARTER GUIDE
**Version:** 3.2
**Type:** Distributable — no personal information
**For:** Someone setting up this system for the first time

---

## WHAT THIS IS

An AI operating system built on top of Claude Code. One command runs your entire day.

It manages multiple projects simultaneously — writing, content, games, business — builds project-specific AI partners, and learns how you work over time. You describe what you're doing. It structures, tracks, and drives it forward.

This guide walks you through the full setup, first-time configuration, and daily use.

---

## PART 1 — SYSTEM SETUP

Do this once. Takes about 30–60 minutes. Steps 5 (Node.js), 6 (Git + Python), and GitHub setup within Step 6 are optional — skip them if you're using the VS Code extension only and don't need the CLI, scripting tools, or remote backup.

---

### STEP 1 — DOWNLOAD OBSIDIAN

Obsidian is your workspace. The AI system lives here. All files are plain Markdown — no lock-in, no proprietary format. Think of it as a second brain with a folder structure.

Download: **obsidian.md**

**Set up your vault:**
- Open Obsidian and create a new vault
- Name it whatever you like — this becomes your workspace root
- Default location: `Documents/[YourVaultName]/`

**Syncing your vault (optional but recommended):**
- **Obsidian Sync** — paid, easiest, works across devices automatically
- **GitHub** — free, requires basic Git knowledge, more control
- **Skip for now** — fine to start locally, add sync later

---

### STEP 2 — UNPACK THIS CONFIG

This file you're reading came inside a folder called `Producer AI - Portable Config`. Inside that folder is a `Vault/` directory containing the AI system.

**Copy the contents of `Vault/` into the root of your Obsidian vault:**

```
Your vault root/
├── _AI/              ← copy this in (brain — workflows, templates, system)
├── .claude/          ← copy this in (skills and rules — loaded automatically)
└── CLAUDE.md         ← copy this in (system entry point)
```

Do not rename any files or folders. The system uses exact paths to find everything.

**What each folder contains:**
- `_AI/` — config, workflows, templates, memory, system files
- `.claude/skills/` — 16 specialist skills the AI can activate (slash commands). Add your own IP-specific skills on top.
- `.claude/rules/` — 8 rule files loaded automatically in every session (pipeline, communication, file routing, system changes, self-improvement, context window, commands standard, build protocol)

Everything else in your vault is yours — your projects, your notes, your content.

---

### STEP 3 — DOWNLOAD VISUAL STUDIO CODE

VS Code is where you run the AI. It's a code editor — you don't need to write any code, but it's the best interface for Claude Code.

Download: **code.visualstudio.com**

Once installed, open your Obsidian vault folder in VS Code:
- File → Open Folder → select your vault folder

---

### STEP 4 — INSTALL CLAUDE CODE

Claude Code is the AI CLI that powers this system. It runs inside VS Code.

**Install the Claude Code extension:**
- In VS Code, open the Extensions panel (Ctrl+Shift+X / Cmd+Shift+X)
- Search for **Claude Code**
- Install it

**Alternatively:** Claude Code can also be installed as a CLI tool via terminal. Full instructions at: **claude.ai/code**

> **Note on alternatives:** You could use Codex (OpenAI) or ChatGPT in a similar setup, but this system was built and tested on Claude. The skill files, config, and behaviour are tuned for Claude specifically. Other models may work but are not supported.

---

### STEP 5 — INSTALL NODE.JS (required for all users)

Claude Code requires Node.js to run — this applies whether you use the VS Code extension or the CLI. Do not skip this step.

If you want to check if Node.js is already installed or install it for the CLI:

**Check if Node.js is already installed:**
```
node --version
npm --version
```

If both return version numbers — you're good.

If not:
- Download from **nodejs.org** — choose the LTS (Long Term Support) version
- Run the installer, accept defaults
- After installing, restart your terminal and verify with `node --version`

**Install Claude Code via npm (CLI method):**
```
npm install -g @anthropic-ai/claude-code
```

Then run:
```
claude
```

This opens the Claude Code CLI directly in your terminal.

> **Extension vs CLI:** Both work. The VS Code extension integrates directly with your editor. The CLI is useful if you prefer terminal workflows or need to run Claude Code outside VS Code.

---

### STEP 6 — INSTALL DEVELOPMENT TOOLS (Git + Python — optional, developers only)

These tools are not required to use the core AI system. Skip this step if you are a writer, creative, or business user — the system works fully without them. Install if you want to version-control your vault with GitHub, run automation scripts, or use the Ralph loop.

---

**GIT**

Git is used by Claude Code to track file changes and run the Ralph loop workflow. You may already have it.

Check:
```
git --version
```

If it returns a version number — you're good. If not:

- **Windows:** Download from **git-scm.com** → Git for Windows. Run the installer, accept all defaults.
- **Mac:** Run `git --version` in Terminal — Mac will prompt you to install developer tools automatically. Or install via Homebrew: `brew install git`
- **Linux:** `sudo apt install git` (Ubuntu/Debian) or `sudo dnf install git` (Fedora)

**Configure your identity (required for commits):**
```
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

After installing, open VS Code in your vault folder. If it prompts you to initialise a repository — do it.

**GitHub setup (for remote backup or collaboration):**

1. Create a free account at **github.com**
2. Create a new repository (private recommended for personal vaults)
3. In VS Code terminal:
```
git init
git add .
git commit -m "chore: initial vault setup"
git remote add origin https://github.com/your-username/your-repo.git
git push -u origin main
```
4. For authentication: use the browser sign-in flow that appears, or create a Personal Access Token at **github.com/settings/tokens**.

> Keep your vault private. Never commit files containing API keys, passwords, or personal data.

---

**PYTHON**

Python is needed if you want to run automation scripts, build tools that call Claude's API directly, or use any `.py` scripts in your workflow. Not required for the core AI system.

Check:
```
python --version
```
or
```
python3 --version
```

If it returns Python 3.8 or higher — you're good. If not:

- **Windows:**
  1. Download from **python.org** → Downloads → Windows → Latest stable release
  2. Run the installer — **check "Add Python to PATH"** before clicking Install (this is critical — easy to miss)
  3. After installing, restart your terminal and verify with `python --version`

- **Mac:**
  Option A (recommended): Install via Homebrew — `brew install python3`
  Option B: Download from **python.org** → Downloads → macOS → Latest stable release
  After install: `python3 --version` to verify

- **Linux:** Python 3 is usually pre-installed. Verify with `python3 --version`. If missing: `sudo apt install python3` (Ubuntu/Debian) or `sudo dnf install python3` (Fedora)

**Verify pip is working (Python's package installer):**
```
pip --version
```
or
```
pip3 --version
```

If it returns a version — you're good. Pip lets you install Python libraries when needed.

> Python and Git are independent — you can install one without the other.

---

### STEP 7 — VERIFY YOUR SETUP

Before running `/daily` for the first time, confirm everything is in place:

**Check 1 — VS Code can see your vault:**
Open VS Code. The file explorer on the left should show your vault folder with `_AI/`, `.claude/`, and `CLAUDE.md` visible.

**Check 2 — Claude Code is installed:**
Open the Claude Code panel in VS Code (look for the Claude icon in the sidebar, or press the Claude Code keyboard shortcut). If you see a chat interface — it's working.

If using CLI instead: open a terminal in VS Code (Ctrl+` / Cmd+`) and type:
```
claude --version
```

**Check 3 — Skills are loaded:**
In the Claude Code panel or CLI, type `/` — you should see a list of available commands including `/daily`, `/strategy`, `/writer`, and the others.

If commands don't appear, check that `.claude/skills/` exists in your vault root and contains skill folders (each with a `SKILL.md` inside).

**Check 4 — Rules are loading:**
Rules load automatically from `.claude/rules/`. No action needed — they load with every session.

**Check 5 — Hooks are ready:**
Open `.claude/settings.json` in VS Code to confirm the file exists and has content. Hooks activate on the next Claude Code restart.

**Restart Claude Code to activate hooks:**
- In VS Code: close and reopen the Claude Code panel, or reload the window (Ctrl+Shift+P → "Developer: Reload Window")
- In CLI: exit (`/exit` or Ctrl+C) and relaunch with `claude`

Once all checks pass — proceed to First Run.

---

### STEP 8 — FIRST RUN

Once VS Code is open in your vault folder and Claude Code is working:

Open the Claude Code panel (or terminal) and type exactly:

```
/daily
```

The system will scan your vault, check for any open items, and propose the first action. There is no automatic wizard — just describe what you're working on and it routes you from there.

`/daily` is all you need every session from that point forward.

---

### STEP 9 — HOOKS (OPTIONAL)

Hooks automate behaviour at key system events. They're already included in the config you copied in (`.claude/settings.json`), but they need to be activated once.

Restart Claude Code — hooks load automatically when the session starts.

**What the hooks do:**
- **PreToolUse (Bash)** — Before any `git` command, injects guardrails: destructive ops require confirmation, no `--force` to main, conventional commit format enforced
- **PreToolUse (Write/Edit)** — Before writing or editing a Live file, fires a pipeline gate: requires an explicit live-gate phrase from the operator
- **PreCompact** — Before context compaction, prompts the AI to save session state to `_AI/MEMORY/` before the window closes
- **Stop** — After each response, logs the session timestamp to `_AI/MEMORY/session-log.txt`
- **UserPromptSubmit** — Injects the current date into every prompt so the AI always knows what day it is

If you skip this step, nothing breaks — hooks are enhancement, not requirement.

---

## PART 2 — FIRST-TIME CONFIGURATION

The first time you run `/daily`, the system will walk you through a short setup to configure itself around you. Here is what to expect.

---

### PHASE 1 — WHO YOU ARE

The system will ask for your basic information:

- **Your name** — how it refers to you
- **Your primary role** — e.g. Creative Director, Developer, Founder, Writer, Project Manager
- **A one-line description of what you do**

Keep this short. You're not writing a bio — you're telling the system how to orient itself toward your work.

---

### PHASE 2 — YOUR BUSINESS OR MAIN CONTEXT

Next it asks about the primary context you work in:

- **Business name** (if applicable)
- **What kind of work it does** — e.g. software studio, content agency, indie game company
- **Your role within it**

This creates the top level of your folder hierarchy. If you're a solo operator, this can be your personal brand or your own name.

---

### PHASE 3 — YOUR FIRST PROJECT (START SMALL)

You don't need to describe everything at once. Start with one project.

> "I'm working on [describe it simply]. It's a [type of project]."

The system will ask a few short questions, then:
- Create the right folder structure for the project type
- Build a basic Project Assistant Config (the AI's briefing document for that project)
- Start tracking it from the next session

**As the basics are defined, the system will ask more specific questions over time** — what stage the project is at, what success looks like, what content or deliverables it produces. It builds context gradually. You don't have to get everything right on day one.

---

### PHASE 4 — ADDING MORE PROJECTS LATER

Once the first project is set up, you can add more at any time:

```
/daily
```

Then just say: *"I want to add a new project. It's [describe it]."*

The system routes it into the right folder structure, builds its config, and starts tracking it alongside everything else.

---

## PART 3 — DAILY USE

```
/daily
```

One command. Every session. That is the entire daily workflow.

**What it does automatically:**
1. Scans your vault for new or unprocessed files
2. Checks the improvements log for pending changes
3. Audits all your active projects — flags missing configs, open actions, stale files
4. Reports findings
5. Proposes the first specific action

**You respond:** confirm, redirect, paste a voice note, or describe what you want to work on.

Every response ends with a proposed next step. You never have to decide what to do from scratch — just confirm or change direction.

---

## PART 4 — HOW THE SYSTEM IS ORGANISED

---

### THE FOLDER HIERARCHY

Your vault is organised around commercial and personal hierarchy. The system uses this to know where things belong and how to route new files.

```
[Name] - Business/              → A business you run or work in
[Name] - Brand/                 → A brand under that business
[Name] - Brand Project/         → A specific project under a brand
[Name] - Person/                → Personal: lifestyle, goals, daily notes
[Name] - Hobby/                 → Creative or personal projects (non-commercial)
```

**Inside each project folder, three subfolders are created automatically:**

```
[Project] - Brand Project/
├── [Project] - AI/             → Assistant Configs (the AI's briefing docs)
├── [Project] - PRDs/           → Product Requirements Documents
└── [Project] - Source of Truth/  → World Bible, Content Strategy, GDD, etc.
```

**Example:**

```
Acme Studios - Business/
├── Acme Studios - AI/                    ← business-level strategy config
├── Acme Games - Brand/
│   ├── Acme Games - AI/
│   └── Project Zeta - Brand Project/
│       ├── Project Zeta - AI/
│       ├── Project Zeta - PRDs/
│       └── Project Zeta - Source of Truth/
└── Acme Content - Brand/

Sasha - Person/
└── Daily Notes/
```

You don't create this manually. When you describe a project, the system routes it into the right structure automatically.

---

### THE FILE PIPELINE

Every piece of working output follows one path. No exceptions.

```
STAGING → PRELIVE → LIVE
```

| Stage | Name format | What it is |
|-------|-------------|------------|
| Staging | `[Project] - [Type]-Staging.md` | Working draft. Create freely. Never auto-promoted. |
| Prelive | `[Project] - [Type]-Prelive.md` | Ready for review. Replaced with each push from staging. |
| Live | `[Project] - [Type]-Live.md` | Authoritative version. Strict gate — explicit approval only. |

Nothing goes live without an explicit request. The AI will never auto-promote.

---

### THE 7-PHASE DEVELOPMENT PROCESS

Every project follows the same phases — writing, software, content, business. No jumping ahead.

| Phase | What happens | Output |
|-------|-------------|--------|
| 1. Idea | The starting concept | `[Name] - Idea.md` |
| Grilling | Challenge the idea before committing — `/grill-me` | In session |
| 2. Research | Cache external knowledge for the sprint | `[Name] - Research.md` (temp) |
| 3. Prototype | Draft, test, or UI sketch | `[Name] - Prototype-Staging.md` |
| 4. PRD | Document the destination — `/prd` | `[Name] - PRD-Staging.md` |
| 5. Plan | Vertical-slice breakdown — `/prd-to-plan` | `./plans/[name].md` |
| 6. Execute | Build, write, produce — ralph loop | Feature-Staging.md |
| 7. QA | Agent creates QA plan, human reviews — `/triage-issue` | `[Name] - QA.md` |

**Research files are temporary.** Scoped to the sprint, deleted after. The PRD is the permanent spec.

---

### THE SKILLS

Each skill is a specialist the AI becomes when working on a certain type of task. `/daily` activates them automatically based on what you're working on — you rarely invoke them directly.

**Operating skills:**

| Skill | What it does |
|-------|-------------|
| `/daily` | Operating intelligence — runs every session, audits, routes, drives forward |
| `/ai-producer` | Producer logic — tasks, timelines, quality gates, improvement cycle |

**Development process skills:**

| Skill | What it does | Phase |
|-------|-------------|-------|
| `/grill-me` | Stress-test a plan before committing — relentless design-tree interview | Pre-PRD |
| `/prd` | Write a Product Requirements Document | Phase 4 |
| `/prd-to-plan` | Break a PRD into vertical-slice phases → `./plans/[name].md` | Phase 5 |
| `/prd-to-issues` | Break a PRD into GitHub Issues (GitHub-enabled projects only) | Phase 5 |
| `/tdd` | Test-driven development — red-green-refactor via vertical slices | Phase 6 |
| `/triage-issue` | Classify and scope an issue before implementation — assigns phase, HITL/AFK, acceptance criteria | Phase 7 |
| `/write-a-skill` | Create a new skill using the standard format | Any |

**Architecture and quality skills:**

| Skill | What it does |
|-------|-------------|
| `/improve-codebase-architecture` | Audit and improve architecture for AI-navigability — identifies shallow modules, naming fog, tangled layers |
| `/ubiquitous-language` | Establish and maintain shared vocabulary across all skills and documents |
| `/git-guardrails` | Safe git practices — conventional commits, branch safety, dangerous operation confirmation |

**Project type skills:**

| Skill | What it does | When it activates |
|-------|-------------|-------------------|
| `/strategy` | Growth strategist — goals, acquisition channels, content direction, revenue. Operates at Business, Brand, and Brand Project level. | Any entity with a growth goal |
| `/writer` | Writing partner for novels, scripts, long-form creative content | Writing projects |
| `/content` | Content pipeline — detection and creation. V1 skill, retained for existing projects. `/strategy` supersedes for new entities. | Legacy content projects |
| `/game-maker` | Game design tools — briefs, GDDs, studio workflow | Game projects |

**Each skill has three layers:**
1. **The Skill itself** — base behaviour, lives in `.claude/skills/[name]/SKILL.md`
2. **Project Assistant Config** — how that skill applies to your specific project, in `[Project] - AI/`
3. **Source of Truth** — the canonical reference for the project (Strategy doc, World Bible, GDD), in `[Project] - Source of Truth/`

The first time you work on a project with a skill, the AI builds the Assistant Config by asking a short question set. After that it knows your project without being re-briefed.

---

### AUTONOMOUS EXECUTION (RALPH LOOP)

Once a project has a PRD and a plan file, you can run the Ralph loop — autonomous execution without prompting each step.

The loop: READ plan → PICK next task → DO it → VERIFY → COMMIT → LOG → repeat.

```
/daily
→ "Run the ralph loop on [project]."
```

It stops when: all tasks are complete, a human decision is required (HITL task), or a blocker arises it can't resolve autonomously.

Full spec: `_AI/WORKFLOWS/ralph-loop.md`

---

## PART 5 — THE SKILLS BUILDER

The 16 built-in skills cover the most common disciplines. If you have a recurring type of work that doesn't fit:

```
/new-skill [describe the role you want]
```

Or just say: *"I want to create a new skill for [role]. Here's what it needs to do."*

The system will:
1. Ask you to describe the role, its responsibilities, and how you want it to communicate
2. Build a new skill file at `.claude/skills/[name]/SKILL.md` (with `REFERENCE.md` if over 100 lines)
3. Wire it into the session start checks so it runs automatically when relevant

New skills inherit the same self-improvement logic, pipeline awareness, and session behaviour. They're not built from scratch — they're adapted from what already works.

Once a skill exists, build its first Project Config:

```
/build-config [skill-name] [project-name]
```

---

## USEFUL COMMANDS

```
/daily                              → Start every session — always begin here
/audit                              → Check all projects for gaps, stale files, open actions
/grill-me                           → Stress-test a plan before committing
/prd                                → Write a PRD — gates the build
/prd-to-plan                        → Break a PRD into phased vertical slices
/prd-to-issues                      → Break a PRD into GitHub Issues (GitHub-enabled projects)
/tdd                                → Test-driven development loop
/triage-issue [description]         → Classify and scope an issue before starting
/build-config strategy [entity]     → Build a Strategy Assistant Config
/build-config [skill] [project]     → Build a project-specific AI partner for any skill
/strategy [entity]                  → Run a strategy session — goals, acquisition, content
/new-skill [type]                   → Create a new skill for a new discipline
/vault-check                        → Weekly cleanup — naming, stale files, broken links
/save-session                       → Save state before closing a long session
/file-route [description]           → Check where a new file should go before creating it
/improve                            → Review staged improvements in the log
```

---

## IF SOMETHING GOES WRONG

| Problem | What to do |
|---------|-----------|
| File landed in the wrong place | `/file-route [description]` — find where it should be |
| Something seems stale or broken | `/vault-check` |
| Lost context between sessions | `/daily` — it reads the last session state automatically |
| Something promoted to live by mistake | Edit in `-Staging` → push to Prelive → push to Live |
| A command doesn't work | Check `.claude/skills/daily/SKILL.md` — the skill may need wiring up first |
| System feels off or isn't behaving as expected | `/daily` — then describe the problem. The self-improvement system will flag it. |

---

## LIFECYCLE — HOW THIS SYSTEM EVOLVES

This config is designed to be merged with other AI systems of similar structure throughout its life. It is not a fixed snapshot.

**What to expect over time:**
- New skills will be added as disciplines are formalised
- Existing skills will be updated as better patterns emerge
- Terminology, thinking models, and best practices will be updated — often by merging with other high-quality AI configs
- The core structure (Staging → Prelive → Live, 7-phase process, skill format) remains stable. What improves is what lives inside it.

**Contributing back:**
If you improve this system — better skill, better workflow, better rule — the intent is that those improvements flow back into the shared portable base. Keep IP out of what you share. Behaviour, process, and structure are shareable. Project content is not.

**This will happen multiple times.** Plan for it. The system is a living config, not a final product.

---

## FURTHER READING

| File | What it covers |
|------|---------------|
| `_AI/SYSTEM/system-guide.md` | Full system logic — hierarchy, file flow, session behaviour |
| `_AI/daily-ai-config.md` | Master config — commands, operating rules, project registry |
| `_AI/SYSTEM/system-architecture.md` | Five-level architecture reference — skills, configs, sources of truth |
| `_AI/WORKFLOWS/file-routing.md` | Where every type of file belongs and how to route it |
| `_AI/WORKFLOWS/ralph-loop.md` | Autonomous execution loop — when and how to use it |
| `_AI/SYSTEM/vault-maintenance-checker.md` | Weekly maintenance protocol |

---

*Producer AI Portable Config v3.2 | Distributable | No personal data*
*Start here → `/daily`*
