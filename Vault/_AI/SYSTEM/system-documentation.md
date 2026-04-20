# SYSTEM DOCUMENTATION
**Version:** 1.0
**Status:** Live — update when system changes
**Part of session start check:** Yes — surfaced if version falls behind current system state

This document is the complete reference for how this AI system works. Read it to understand every component, why it exists, how it connects, and what it produces. It is updated whenever the system changes.

---

## WHAT THIS SYSTEM IS

An AI operating system built on top of an Obsidian vault and powered by Claude Code. One command (`/daily`) runs the full system every session. It manages multiple simultaneous projects across disciplines, builds project-specific AI behaviour, and improves itself over time.

---

## THE 5-LEVEL HIERARCHY

The system has five levels. Operating context flows down (L0 → L1 → L2 → L3). Output flows up (Staging → Prelive → Live). Learning flows to L5.

```
L0  Master System     _AI/daily-ai-config.md + SYSTEM/ + WORKFLOWS/ + TEMPLATES/ + MEMORY/
L1  Skills            .claude/skills/[name]/SKILL.md
L2  Project Configs   [ProjectFolder]/[Project] - AI/[SkillType] Assistant Config.md
L3  Source of Truth   [ProjectFolder]/[Project] - [Type].md
L4  Working Files     [Name]-Staging.md → [Name]-Prelive.md → [Name].md (live)
L5  Memory            _AI/MEMORY/improvements-log.md | session-state files
```

---

## LEVEL 0 — MASTER SYSTEM

The operating system itself. No project data. No personal content.

### `_AI/daily-ai-config.md`
Master config. Loaded every session. Contains: identity and role orientations, operating protocol, session start order, skills registry (Section 04), active project registry (Section 08). Edit Section 08 to register your projects.

### `_AI/SYSTEM/` — System protocols and utilities

| File | Purpose |
|---|---|
| `system-architecture.md` | Canonical spec: hierarchy, naming conventions, information flow |
| `system-guide.md` | Operating model: how files communicate, how sessions flow |
| `system-documentation.md` | This file — full system reference |
| `new-files-checker.md` | Zone 1–5 scan protocol. Runs at every session start |
| `vault-maintenance-checker.md` | Weekly structural audit: naming, stale files, broken references |
| `session-starter.md` | Automated session start sequence |
| `self-improvement-framework.md` | Full 3-level improvement specification |
| `context-window-protocol.md` | Context window save/resume procedure |
| `log-check.md` | Single-pass log sweep: improvements, open items, pipeline blockers |
| `daily-run.md` | Daily run reference |

### `_AI/WORKFLOWS/` — Process flows (loaded by skills on demand)

| File | Purpose |
|---|---|
| `file-routing.md` | Full routing matrix — file type → correct location + naming |
| `content-creation.md` | Content pipeline process |
| `project-status.md` | Project status reporting flow |
| `business-review.md` | Business review process |
| `daily-brief.md` | Daily brief format |
| `ralph-loop.md` | Autonomous execution loop (PRD → plan → execute → commit → repeat) |

### `_AI/TEMPLATES/` — Blank starters

| Template | Creates |
|---|---|
| `staging-template.md` | Any new staging file |
| `daily-note-generic.md` | Daily note structure |
| `writing-assistant-config-template.md` | Blank Writing Assistant Config |
| `content-assistant-config-template.md` | Blank Content Assistant Config |
| `game-assistant-config-template.md` | Blank Game Assistant Config |
| `producer-assistant-config-template.md` | Blank Producer Assistant Config |
| `strategy-assistant-config-template.md` | Blank Strategy Assistant Config |
| `prd-template.md` | Blank PRD structure |

### `_AI/MEMORY/`

| File | Purpose |
|---|---|
| `improvements-log.md` | Append-only. Every system change tagged by source. Reviewed via `/improve` |
| `session-state-[date].md` | Context window saves — used to resume sessions, then superseded |

---

## LEVEL 1 — SKILLS

Each skill is a role-specific behavior agent. Defines HOW to operate in that role. No personal data. No project content.

**Location:** `.claude/skills/[name]/SKILL.md` + optional `REFERENCE.md` (if SKILL.md > 100 lines)
**Called via:** `/[name]` slash command in Claude Code

### All skills

| Skill | Role | What it produces |
|---|---|---|
| `/daily` | Operating intelligence | Session report, project audit, NEXT MOVE block |
| `/writer` | Editorial writing partner | Chapter drafts, scene reviews, world bible entries |
| `/content` | Content pipeline | Post drafts, articles, content opportunity detection |
| `/game-maker` | Game tool operator | Game briefs, GDDs, studio workflow role outputs |
| `/ai-producer` | Self-improving producer | Production plans, improvement flags, cross-project oversight |
| `/strategy` | Growth strategist | Strategy docs, acquisition frameworks, content strategies |
| `/prd` | PRD author | Product Requirements Documents via 7-question protocol |
| `/grill-me` | Stress-tester | Gaps and wrong assumptions surfaced before building |
| `/prd-to-plan` | Plan builder | Phased implementation plans in `plans/` |
| `/prd-to-issues` | Issues builder | GitHub issues from PRDs as vertical slices |
| `/tdd` | TDD practitioner | Red-green-refactor test cycles |
| `/write-a-skill` | Skill creator | New `.claude/skills/[name]/SKILL.md` files via 7-question protocol |
| `/improve-codebase-architecture` | Architecture auditor | Architecture report, refactor recommendations |
| `/triage-issue` | Issue classifier | Scoped issue with phase, HITL/AFK, dependencies, acceptance criteria |
| `/ubiquitous-language` | Vocabulary keeper | Shared terminology definitions, naming audit reports |
| `/git-guardrails` | Git safety enforcer | Conventional commits, branch naming, destructive operation blocks |
| `/edit-article` | Article editor | Restructured, tightened prose with section-level notes |
| `/design-an-interface` | Interface designer | Multiple interface designs via parallel sub-agents |
| `/qa` | QA session runner | Bug reports as GitHub issues or Markdown staging entries |
| `/request-refactor-plan` | Refactor planner | Refactor plan as GitHub issue or Markdown staging entry |
| `/bookkeeping` | AUS bookkeeping (generic framework) | Sole trader income/expense records, accountant handoff export |
| `/lifestyle-plan` | Health and lifestyle planning (generic framework) | Eating plans, exercise progressions, weekly schedules |

**Note on personal skills:** `/bookkeeping` and `/lifestyle-plan` are generic frameworks. On first use, they run an intake/setup session to collect your personal data. That personal data lives in your project files — not in the skill itself. The skill is reusable; your data is yours.

**Skill file format:**
```
.claude/skills/[name]/
├── SKILL.md       ← core behavior, YAML frontmatter, under 100 lines
└── REFERENCE.md   ← overflow: extended protocols, edge cases (only if > 100 lines)
```

---

## LEVEL 2 — PROJECT ASSISTANT CONFIGS

The bridge between a skill's generic behavior and a specific project.

**Location:** `[ProjectFolder]/[Project] - AI/[SkillType] Assistant Config.md`
**Contains:** Project-specific voice, tone, rules, constraints, config creation answers
**Does NOT contain:** Generic behavior rules (stay in L1) or project facts (stay in L3)
**Created by:** `/build-config [skill] [project]`
**Updated by:** Skill appends new rules with per-addition operator approval

---

## LEVEL 3 — SOURCE OF TRUTH

Project facts. Not behavior — what the project IS.

| Project type | Source of Truth |
|---|---|
| Novel / IP | `[IP] - World Bible.md` |
| Content / Brand | `[Brand] - ContentStrategy.md` |
| Game project | `[Project] - GDD-Live.md` |
| Business / Client | Strategy doc or equivalent |

**Rule:** Never modified during a session without explicit operator instruction.

---

## LEVEL 4 — WORKING FILES

### The pipeline
```
[Name]-Staging.md  →  [Name]-Prelive.md  →  [Name].md (live, no suffix)
```

| Stage | Rules |
|---|---|
| `-Staging.md` | Primary work environment. All drafts here. Never deleted without instruction. |
| `-Prelive.md` | Awaiting review only. Never edited in place. |
| No suffix (live) | Authoritative version. Only updated via explicit live-gate phrase. |
| `-Planning.md` | Permanent editable reference document. NOT a pipeline file. No destination. Never promoted. Updated directly any session. The name refers to purpose, not state — it is not a live file. |

**Hard pipeline rules:**
- Never write directly to live without: "push to live" / "make it live" / "promote to live"
- Never skip staging for new working content
- Live is always the source of truth
- Data flows one direction: Staging → Prelive → Live. Never backward automatically.

---

## LEVEL 5 — MEMORY AND FEEDBACK

| File | What it does |
|---|---|
| `_AI/MEMORY/improvements-log.md` | Append-only. Every change tagged `[SYSTEM]` / `[SKILL: name]` / `[IP: name]` / `[TEMPLATE]` |
| `_AI/MEMORY/session-state-[date].md` | Context window saves: completed work, open items, next moves, decisions made |

### Self-improvement — 3 levels

| Level | Trigger | What happens |
|---|---|---|
| L1 Micro | Always active | Real-time adjustments. No logging. |
| L2 Session flag | Same correction 2×, bad output, command misuse | Flags pattern → `improvements-log.md` |
| L3 Config change | Approved L2, 3+ session pattern, `/improve` | Proposes edit via staging pipeline |

---

## SESSION START FLOW

Every `/daily` runs this automatically:

```
1. Load _AI/daily-ai-config.md
2. Run new-files-checker.md:
   Zone 1 — Daily notes: unprocessed files
   Zone 2 — Project files: new/stale/pending
   Zone 3 — _AI system: staged improvements, rule integrity, active plans, path resolution
   Zone 4 — Unlinked content
3. Check improvements-log for STATUS: Staged entries
4. Run Project Audit (per daily/REFERENCE.md protocol)
5. Weekly check — Monday or 7+ days since last check: prompt /vault-check
6. Report. Propose first action. NEXT MOVE block.
```

**First-run path resolution:** On your first session, the system detects that settings.json uses relative paths and offers to resolve them to absolute paths for your machine. Approve when prompted — this is a one-time setup step that optimises the system for your environment.

---

## HOW PROCESSES ARE CALLED

| Process | Command | Logic location |
|---|---|---|
| Session start | `/daily` | `daily-ai-config.md` + `daily/SKILL.md` |
| Project work | `/writer`, `/game-maker`, etc. | `skills/[name]/SKILL.md` + L2 Config |
| Config creation | `/build-config [skill] [project]` | Config creation protocol inside each skill |
| New skill | `/write-a-skill` | `write-a-skill/SKILL.md` → 7-question protocol |
| PRD | `/prd` | `prd/SKILL.md` |
| Planning | `/prd-to-plan` | `prd-to-plan/SKILL.md` → writes to `plans/` |
| Weekly audit | `/vault-check` | `_AI/SYSTEM/vault-maintenance-checker.md` |
| File routing | `/file-route [description]` | `_AI/WORKFLOWS/file-routing.md` |
| Improvement review | `/improve` | `_AI/MEMORY/improvements-log.md` |
| Session save | `/save-session` | `_AI/SYSTEM/context-window-protocol.md` |

---

## HOOKS — AUTOMATION LAYER

Configured in `.claude/settings.json`. Fire automatically.

| Hook | When | What it does |
|---|---|---|
| PreToolUse (Bash) | Any Bash call | Triggers git safety check on git commands only |
| PreToolUse (Write/Edit) | Writing to `-Live` file path | Pipeline quality gate — warns before touching a live file |
| Stop | After each response | Logs session timestamp |
| UserPromptSubmit | Every prompt | Injects current date into context |
| PreCompact | Before context compaction | Saves session state |

---

## NAMING CONVENTIONS

```
PIPELINE FILES:
  [Project] - [Type]-Staging.md      actively in pipeline
  [Project] - [Type]-Prelive.md      pending review
  [Project] - [Type].md              live / canonical (no suffix = live)

REFERENCE FILES (no pipeline, never promoted):
  [Project] - [Type]-Planning.md     permanent editable reference doc
                                     NOT a live file — name refers to purpose, not state

CONFIG FILES:
  [Project] - AI/[SkillType] Assistant Config.md

LOG FILES (append-only):
  [Brand]-[Type]Log.md

SYSTEM FILES:
  _AI/daily-ai-config.md             master config
  .claude/skills/[name]/SKILL.md     skill
  _AI/WORKFLOWS/[name].md            workflow
  _AI/TEMPLATES/[name]-template.md   template
  _AI/SYSTEM/[name].md               system utility
  _AI/MEMORY/[name].md               memory/log
```

---

## ADDING YOUR FIRST PROJECT

1. Register in `_AI/daily-ai-config.md` Section 08 (follow the existing format)
2. Create project folder
3. Run `/build-config [skill] [project]` for each skill relevant to your project → creates L2 config
4. Create or flag a Source of Truth file

---

## BUILD PROTOCOL — WHEN IT FIRES

Before any new artifact or significant scope expansion, the build protocol runs 6 phases:

```
GATHER → RESEARCH → PROTOTYPE → GRILL → STAGE → PRELIVE/LIVE
```

Each phase requires explicit operator approval before advancing. No skipping. No auto-advancing.

**Personal requests (lifestyle plan, bookkeeping, personal budget, etc.):**
The system builds the skill and template first, then adds your personal data. Skills are reusable by anyone — personal data belongs to you. System first, your content on top.

---

## ISOLATION RULES

Every level can be removed without breaking the system:
- Remove a skill → projects keep configs and source of truth. Skill just isn't invoked.
- Remove a Project Config → skill falls back to generic behavior.
- Remove Source of Truth → skill loses consistency check. Flags the gap.
- Remove project from registry → becomes inactive. Files remain. Re-register to restore.
- Remove daily config → skills still run standalone. Master protocol lost, core behavior intact.

---

*System Documentation v1.0 | Update when system changes | Surfaced at session start*
