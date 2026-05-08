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
L1  Skills            .claude/commands/[name].md
L2  Project Configs   [ProjectFolder]/[Project] - AI/[SkillType] Assistant Config.md
L3  Source of Truth   [ProjectFolder]/[Project] - [Type].md
L4  Working Files     [Name]-Staging.md → [Name]-Prelive.md → [Name].md (live)
L5  Memory            _AI/MEMORY/improvements-log.md | session-state files
```

---

## LEVEL 0 — MASTER SYSTEM

The operating system itself. No project IP. No project content.

### `_AI/daily-ai-config.md`
Master config. Loaded every session. Contains: identity and role orientations, operating protocol, session start order, skills registry (Section 04), active project registry (Section 08), commands reference. The single source of system truth.

### `_AI/SYSTEM/` — System protocols and utilities

| File | Purpose |
|---|---|
| `system-architecture.md` | Canonical spec: hierarchy, naming conventions, information flow, isolation rules |
| `system-guide.md` | Operating model: how files communicate, how sessions flow |
| `system-documentation.md` | This file — full system reference |
| `new-files-checker.md` | Zone 1–5 scan protocol. Runs at every session start |
| `vault-maintenance-checker.md` | Weekly structural audit: naming, stale files, broken refs, portable sync |
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
| `game-brief.md` | Legacy reference — game-brief is now a skill at `.claude/commands/game-brief.md` |
| `daily-brief.md` | Daily brief format |
| `ralph-loop.md` | Autonomous execution loop (PRD → plan → execute → commit → repeat) |

### `_AI/TEMPLATES/` — Blank starters (never filled in here — used by skills when creating new files)

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

### `_AI/MEMORY/` — Persistent system memory

| File | Purpose |
|---|---|
| `improvements-log.md` | Append-only. Every system change tagged by source. Reviewed via `/improve` |
| `session-state-[date].md` | Context window saves — used to resume sessions, then superseded |

### `_AI/System PRDs/` — Infrastructure PRDs
System-level requirements documents. Zero project IP.

### `plans/` — Active system change plans
Any system change affecting 2+ files requires a plan file here with checkbox completion criteria.

---

## LEVEL 1 — SKILLS

Each skill is a role-specific behavior agent. Defines HOW to operate in that role — what to check, what to produce, what limits apply. No IP. No project content.

**Location:** `.claude/commands/[name].md`
**Called via:** `/[name]` slash command in Claude Code

### All skills

| Skill | Role | What it produces |
|---|---|---|
| `/daily` | Operating intelligence | Session report, project audit, NEXT MOVE block |
| `/writer` | Editorial writing partner | Chapter drafts, scene reviews, world bible entries |
| `/content` | Content pipeline | Post drafts, articles, content opportunity detection |
| `/game-maker` | Game tool operator | Game briefs, GDDs, studio workflow role outputs |
| `/game-brief` | Game brief builder | PROMPT.md, brief.json, pipeline.zip for any game project |
| `/ai-producer` | Self-improving producer | Production plans, improvement flags, cross-project oversight |
| `/strategy` | Growth strategist | Strategy docs, acquisition frameworks, content strategies |
| `/prd` | PRD author | Product Requirements Documents via 7-question protocol |
| `/grill-me` | Stress-tester | Gaps and wrong assumptions surfaced before building |
| `/prd-to-plan` | Plan builder | Phased implementation plans in `plans/` |
| `/prd-to-issues` | Issues builder | GitHub issues from PRDs as vertical slices |
| `/tdd` | TDD practitioner | Red-green-refactor test cycles |
| `/write-a-skill` | Skill creator | New `.claude/commands/[name].md` files |
| `/improve-codebase-architecture` | Architecture auditor | Architecture report, refactor recommendations |
| `/triage-issue` | Issue classifier | Scoped issue with phase, HITL/AFK, dependencies, acceptance criteria |
| `/ubiquitous-language` | Vocabulary keeper | Shared terminology definitions, naming audit reports |
| `/git-guardrails` | Git safety enforcer | Conventional commits, branch naming, destructive operation blocks |
| `/edit-article` | Article editor | Restructured, tightened prose with section-level notes |
| `/design-an-interface` | Interface designer | Multiple interface designs via parallel sub-agents |
| `/qa` | QA session runner | Bug reports as GitHub issues or Markdown staging entries |
| `/request-refactor-plan` | Refactor planner | Refactor plan as GitHub issue or Markdown staging entry |
| `/bookkeeping` | AUS bookkeeping | HatchFox income/expense records + TBST D-item deductions |
| `/lifestyle-plan` | Personal health planner | Eating plans, exercise progressions, weekly schedules |

**Skill file format:**
```
.claude/commands/[name].md   ← single file. Active prompt under 100 lines + ## REFERENCE section for overflow.
```

---

## LEVEL 2 — PROJECT ASSISTANT CONFIGS

The bridge between a skill's generic behavior and a specific project. HOW to apply this skill to THIS project.

**Location:** `[ProjectFolder]/[Project] - AI/[SkillType] Assistant Config.md`
**Contains:** Project-specific voice, tone, rules, constraints, open questions, config creation answers
**Does NOT contain:** Generic behavior rules (stay in L1) or project facts (stay in L3)
**Created by:** `/build-config [skill] [project]` — runs the skill's config creation question set
**Updated by:** Skill appends new rules during sessions with per-addition operator approval
**Pipeline gate:** None — skill writes directly with operator approval

---

## LEVEL 3 — SOURCE OF TRUTH

Project facts. Not behavior — what the project IS. Skills read this for consistency checks.

| Project type | Source of Truth |
|---|---|
| Novel / IP | `[IP] - World Bible.md` |
| Content / Brand | `[Brand] - ContentStrategy-Live.md` |
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
| `-Prelive.md` | Awaiting review only. Never edited in place — replaced by clean push from staging. |
| No suffix (live) | Authoritative version. Only updated via explicit live-gate phrase. |
| `-Planning.md` | Permanent editable reference document. NOT a pipeline file. No destination. Never promoted. Updated directly any session. The name refers to purpose (decisions/structure), not state. |

**Hard pipeline rules:**
- Never write directly to live without: "push to live" / "make it live" / "promote to live"
- Never skip staging for new working content
- Live is always the source of truth — never treat staging as more current
- Data flows one direction: Staging → Prelive → Live. Never backward automatically.
- When citing issues across pipeline files: always include file + line number

---

## LEVEL 5 — MEMORY AND FEEDBACK

| File | What it does |
|---|---|
| `_AI/MEMORY/improvements-log.md` | Append-only improvement log. Every change tagged `[SYSTEM]` / `[SKILL: name]` / `[IP: name]` / `[TEMPLATE]` |
| `_AI/MEMORY/session-state-[date].md` | Context window saves: completed work, open items, next moves, improvement flags, files written, decisions |

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
   Zone 5 — Portable config sync
3. Check improvements-log for STATUS: Staged entries
4. Run Project Audit (per daily/REFERENCE.md protocol)
5. Weekly check — Monday or 7+ days since last vault-check: prompt /vault-check
6. Report. Propose first action. NEXT MOVE block.
```

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
| Archive | `/archive [file]` | Move to `[Project]/Archive/` + append ` - Archive` |

---

## HOOKS — AUTOMATION LAYER

Configured in `.claude/settings.json`. Fire automatically without a command.

| Hook | When | What it does |
|---|---|---|
| PreToolUse (Bash) | Any Bash call | Parses JSON — triggers git safety check on git commands only |
| PreToolUse (Write/Edit) | Writing to `-Live` file path | Pipeline quality gate — warns before touching a live file |
| Stop | After each response | Logs session timestamp to `_AI/MEMORY/session-log.txt` |
| UserPromptSubmit | Every prompt | Injects current date into context |
| PreCompact | Before context compaction | Saves session state |

---

## NAMING CONVENTIONS

```
PIPELINE FILES:
  [IP or Project] - [Type]-Staging.md      actively in pipeline
  [IP or Project] - [Type]-Prelive.md      pending review
  [IP or Project] - [Type].md              live / canonical (no suffix = live)

REFERENCE FILES (no pipeline, never promoted):
  [IP or Project] - [Type]-Planning.md     permanent editable reference doc

CONFIG FILES:
  [Project] - AI/[SkillType] Assistant Config.md

LOG FILES (append-only):
  [Brand]-[Type]Log.md

SYSTEM FILES:
  _AI/daily-ai-config.md                   master config
  .claude/skills/[name]/SKILL.md           skill
  _AI/WORKFLOWS/[name].md                  workflow
  _AI/TEMPLATES/[name]-template.md         template
  _AI/SYSTEM/[name].md                     system utility
  _AI/MEMORY/[name].md                     memory/log
  _AI/MEMORY/session-state-[YYYY-MM-DD].md session save
```

---

## INFORMATION FLOW

```
OPERATOR INPUT
      ↓
/daily (L1 — Daily Skill)
      ↓ reads
L0 Master Config + SYSTEM/ + MEMORY/
      ↓ produces
Session Report + Project Audit + NEXT MOVE
      ↓
Skill activated (L1)
      ↓ reads
L2 Project Assistant Config
      ↓ reads on demand
L3 Source of Truth
      ↓ produces
L4 Staging file
      ↓ reviewed → Prelive
      ↓ approved → Live
End of session:
      ↓ writes
L5 Improvements log (tagged) + Session state (if context closing)
```

**Direction rules:**
- Context flows DOWN: L0 → L1 → L2 → L3
- Output flows UP: Staging → Prelive → Live
- Learning flows to L5 (tagged, sourced, actionable)
- Nothing flows backward automatically

---

## ISOLATION RULES

Every level can be removed without hard-breaking the system:
- Remove a skill → projects keep configs and source of truth. Skill not invoked.
- Remove a Project Config → skill falls back to generic behavior.
- Remove Source of Truth → skill loses consistency check. Flags the gap.
- Remove project from registry → becomes inactive. Files remain. Re-register to restore.
- Remove daily config → skills still run standalone. Master protocol lost, core behavior intact.

---

## BUILD PROTOCOL — WHEN IT FIRES

Before any new artifact or significant scope expansion, the build protocol runs 6 phases:

```
GATHER → RESEARCH → PROTOTYPE → GRILL → STAGE → PRELIVE/LIVE
```

Each phase requires explicit operator approval before advancing. No skipping. No auto-advancing.

**Personal requests (lifestyle plan, bookkeeping, personal budget, etc.):**
Build the skill and template first. Add personal data second. System assets are reusable. Personal data is local-only. System first, personal content on top.

---

*System Documentation v1.0 | Update when system changes | Surfaced at session start*
