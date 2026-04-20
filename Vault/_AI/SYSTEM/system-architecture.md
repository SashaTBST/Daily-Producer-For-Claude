# SYSTEM ARCHITECTURE
**Version:** 1.0
**Part of:** [[../_AI/daily-ai-config]]
**Status:** Active — canonical reference

This document defines the complete hierarchy of the AI operating system. Every file, skill, config, and memory artifact has a defined layer. Every flow of information has a defined direction. The system can be understood and reasoned about from this document alone.

---

## THE HIERARCHY — FIVE LEVELS

```
LEVEL 0 — MASTER SYSTEM
  _AI/daily-ai-config.md
  _AI/SYSTEM/          (protocols and utilities)
  _AI/WORKFLOWS/       (process flows)
  _AI/TEMPLATES/       (blank starters)
  _AI/MEMORY/          (improvements, session state, feedback)
  _AI/System PRDs/     (system infrastructure PRDs — no project IP)

LEVEL 1 — SKILLS (behavior agents)
  .claude/commands/[name].md
  One per discipline or role type
  Generic — no IP, no project-specific content
  Reads Level 2 on demand

LEVEL 2 — PROJECT ASSISTANT CONFIGS (behavior adapted to project)
  [ProjectFolder]/[IP or Project] - [SkillType] Assistant Config.md
  One per skill × per project (where that skill applies to that project)
  Created by the skill using the config creation protocol
  Grows as the project develops
  HOW to work on this project using this skill

LEVEL 3 — SOURCE OF TRUTH (what the project contains)
  [ProjectFolder]/[IP or Project] - [Type].md
  World Bible, Content Strategy, Project Brief, Business Plan, etc.
  Facts, lore, decisions — not behavior direction
  Read by skills for consistency checks, not for operating rules

LEVEL 4 — WORKING FILES (active production)
  [ProjectFolder]/[Name]-Staging.md
  [ProjectFolder]/[Name]-Prelive.md
  [ProjectFolder]/[Name]-Live.md (or canonical live file)
  Output of sessions — moves through Staging → Prelive → Live

LEVEL 5 — MEMORY & FEEDBACK
  _AI/MEMORY/improvements-log.md        (system-wide log, tagged by source)
  _AI/MEMORY/session-state-[date].md    (context window saves)
  [ProjectFolder]/feedback/             (project-specific feedback files if needed)
  [SkillFolder]/feedback/               (skill-specific feedback if isolated)
```

---

## LAYER DEFINITIONS

### Level 0 — Master System

The daily config and its supporting files. Governs all sessions. Defines operating protocol, pipeline rules, self-improvement, file routing, context window management.

**Key rule:** This level contains no IP, no project content, and no project-specific behavior. It is the operating system, not the content.

**Files:**
- `_AI/daily-ai-config.md` — master config (live)
- `_AI/daily-ai-config-Staging.md` — proposed changes (requires approval to go live)
- `_AI/SYSTEM/` — protocols: context window, self-improvement framework, architecture (this file), session utilities
- `_AI/WORKFLOWS/` — process flows: daily brief, file routing, content creation, etc.
- `_AI/TEMPLATES/` — blank templates: staging, daily notes, assistant configs
- `_AI/MEMORY/` — persistent memory: improvements log, session states
- `_AI/System PRDs/` — system infrastructure PRDs (e.g. Config Restructure, Rules Migration) — zero project IP

---

### Level 1 — Skills

Each skill is a behavior agent for a specific discipline or role type. It defines HOW to operate in that role — what to check, what to produce, what hard limits apply. Skills contain no IP.

**Naming:** `.claude/commands/[role-name].md`

**Current skills:**
| Skill | Role | When to use |
|---|---|---|
| `/daily` | Operating intelligence | Session start, cross-project management |
| `/writer` | Editorial partner | Any novel or long-form writing project |
| `/content` | Content pipeline | Posts, articles, content detection across brands |
| `/game-maker` | Game tool operator | Game brief, AI studio workflow, config library |
| `/ai-producer` | Self-improving producer | TBST producing work, testing, improvement cycles |

**New skills are created when:**
- A project requires a discipline not covered by existing skills
- A role has enough recurring behavior to justify its own set of operating rules
- The daily skill or producer flags a gap during a project audit

**How to create a new skill:** See NEW SKILL CREATION PROTOCOL below.

---

### Level 2 — Project Assistant Configs

The bridge between a skill's generic behavior and a specific project's requirements.

**Naming:** `[ProjectFolder]/[Project] - AI/[SkillType] Assistant Config.md`

**Examples:**
- `HatchFox Studios - Business/Athena 2327 - Brand/Sycthe of Athena - Brand Project/Sycthe of Athena - AI/Writing Assistant Config.md`
- `Duio - Business/Duio - AI/Content Assistant Config.md`
- `HatchFox Studios - Business/HatchFox Content - Brand/HatchFox Content - AI/Content Assistant Config.md`
- `TBST Digital - Business/TBST Digital - AI/Content Assistant Config.md`
- `HatchFox Studios - Business/AI Game Maker - Brand Project/AI Game Maker - AI/Game Assistant Config.md`

**What it contains:**
- HOW to apply the skill's behavior to THIS project
- Project-specific rules that override or extend the skill's generic behavior
- Voice, tone, constraints, do's and don'ts specific to this IP or project
- Open questions that need answers before the skill can operate optimally
- The question set answers that built this file
- How this file grows (the skill adds to it when new decisions are made)

**What it does NOT contain:**
- Generic behavior rules (those are in the skill)
- World/story/brand facts (those are in the Source of Truth)

**Created by:** The skill's Config Creation Protocol (question set)
**Maintained by:** The skill appends to it when new project-specific rules are established
**Pipeline:** No staging gate needed — the skill can write to this file directly, with the operator's per-addition approval

---

### Level 3 — Source of Truth

What the project contains. Not behavior — facts.

**Types by project type:**
| Project type | Source of Truth file type |
|---|---|
| Novel / IP | `[IP] - World Bible.md` |
| Content / Brand | `[Brand] - ContentStrategy-Staging.md` → Live |
| Game project | `[Project]-GDD-Staging.md` + `[Project]-PRD-Staging.md` |
| Business / Client | `[Project] - Business Strategy.md` or equivalent |

**Key rule:** Skills read the Source of Truth for consistency checks, not for operating rules. The operating rules are in Level 2.

---

### Level 4 — Working Files

All draft, review, and published output. Follows the standard pipeline.

`Staging → Prelive → Live`

Naming: `[Project/IP Name] - [Descriptor]-Staging.md` etc.

---

### Level 5 — Memory and Feedback

Persistent memory that improves the system over time.

**IMPROVEMENTS LOG** — `_AI/MEMORY/improvements-log.md`
All improvements tagged at source:
- `[SYSTEM]` — affects master config or cross-skill behavior
- `[SKILL: name]` — affects a specific skill's behavior
- `[IP: name]` — affects a specific project's assistant config
- `[SKILL: name + IP: name]` — spans both
- `[TEMPLATE]` — affects a template

If a feedback or improvement spans more than one skill or IP: split it. Write a separate entry for each. Tag each separately. Do not bundle multi-source improvements into one entry.

**SESSION STATE** — `_AI/MEMORY/session-state-[YYYY-MM-DD].md`
Context window saves. Ephemeral reference — used to restart sessions, then superseded.

---

## INFORMATION FLOW

```
OPERATOR INPUT
      ↓
DAILY SKILL (session start)
  → Project Audit: check each project against skills + assistant configs
  → Surface open items across all projects
  → Route to relevant skill(s)
      ↓
SKILL ACTIVATED
  → Reads Level 2 (Project Assistant Config) for this project
  → Reads Level 3 (Source of Truth) on demand for consistency checks
  → Produces output → Level 4 (Staging)
      ↓
STAGING → (review) → PRELIVE → (approval) → LIVE
      ↓
END OF SESSION
  → Skill proposes additions to Level 2 (new rules learned this session)
  → Improvement flags written to Level 5 (improvements log), tagged by source
  → If context closing: session state written to Level 5
```

**Direction rules:**
→ Information flows DOWN through the hierarchy for operating context (L0 → L1 → L2 → L3)
→ Output flows UP for production (L4 Staging → Prelive → Live)
→ Learning flows to L5 (tagged, sourced, actionable)
→ Nothing flows backward automatically. Edits to live go back through staging first.

---

## PROJECT AUDIT PROTOCOL

Run at session start by the `/daily` skill. Also available as `/audit`.

For each active project in the registry:

1. **Skill match check** — what skills apply to this project?
   - Match project type to skill registry
   - Flag any project type that has no matching skill → propose creating one

2. **Assistant config check** — for each matching skill, does `[SkillType] Assistant Config.md` exist in `[Project] - AI/`?
   - If yes: note it, flag if it's stale (not updated in 30+ days and project is active)
   - If no: flag — "Missing [SkillType] Assistant Config for [Project]. Run /build-config [skill] [project]"

3. **Source of truth check** — does the project have a current source of truth file?
   - If no: flag — "No Source of Truth for [Project]. Should one be created?"
   - If a file exists but appears to contain the wrong format (code, placeholder, or wrong type) → flag

4. **Working files check** — are there staging files that have been untouched for 7+ days?
   - Flag for action or cleanup

5. **Sub-project check** — for Brands with multiple Brand Projects, audit each sub-project:
   - Check for empty sub-project folders with no files → flag: "Empty: [folder]. Active or inactive?"
   - Check each sub-project for its own assistant config and source of truth
   - Unregistered folders inside a project directory → flag and offer to register

6. **Brand name check** — is the project or brand name a placeholder or working title?
   - Signs: generic descriptor ("AI Game Maker", "Content", "Strategy"), version numbers in names, obvious working titles
   - Flag: "[Name] may be a placeholder. Suggest a proper brand name when more information is available."
   - Do NOT rename automatically — flag only, propose when operator confirms the project is taking shape

**Output format:**
```
PROJECT AUDIT — [date]

[Project Name]
  Skills:        /writer ✅ | /content — no config
  Assistant Configs: Writing Assistant Config ✅ | Content Assistant Config ✗ → flag
  Source of Truth: World Bible ✅ | Content Strategy ✗ → flag
  Working files: Chapter 1 Staging — 3 days untouched → CLEAR
  Proposed action: Create Duio - Content Assistant Config
```

---

## NEW SKILL CREATION PROTOCOL

When the system identifies a project type with no matching skill, run this protocol.

**Trigger:** Daily audit flags a gap. Or operator uses `/new-skill [type]`.

**Questions to ask (one at a time):**

1. What discipline or role does this skill cover? (e.g. brand strategy, video production, UX design)
2. What does this skill produce? What is its output?
3. What are the hard limits for this role? (what must it never do)
4. What checks must it run before output? (like the writer's three-check sequence)
5. What questions would it need to ask to build a Project Assistant Config for a specific project?
6. What does a good session look like for this role?
7. What are the most common failure modes for AI in this role? (what to protect against)

**Output:** New command file at `.claude/commands/[name].md`
**Template:** Use the structure of any existing skill as the base pattern
**Template for configs:** Create `_AI/TEMPLATES/[name]-assistant-config-template.md`
**Register:** Add to daily-ai-config Section 08 skill registry and Section 04 commands

---

## ISOLATION RULES

Every level can be isolated without breaking the system.

- Removing a skill: projects retain their assistant configs and source of truth. No data is lost. The skill just isn't invoked.
- Removing a project assistant config: the skill falls back to generic behavior. It loses project-specific rules but still operates.
- Removing a source of truth: the skill loses its consistency check reference. It continues but flags that the source of truth is missing.
- Removing a project from the daily registry: it becomes inactive but its files remain. Re-registering restores full function.
- Removing the daily config: individual skills still operate standalone (they carry core behavior internally). The master operating protocol is lost but skills self-contained.

**Key principle:** Everything is additive. Removing components degrades capability but never breaks a hard dependency.

---

## NAMING CONVENTIONS — CANONICAL

All project files use the pattern: `[IP or Project] - [Type]-[Stage].md`

```
PIPELINE FILES (go through Staging → Prelive → Live):
  [IP or Project] - [Type]-Staging.md       ← in pipeline, being worked on
  [IP or Project] - [Type]-Prelive.md       ← pending review
  [IP or Project] - [Type].md               ← live / canonical (no suffix = live)

PLANNING FILES (living documents, no pipeline):
  [IP or Project] - [Type]-Planning.md      ← decisions, structure, design — updated directly

CONFIG FILES (maintained by skill, no pipeline gate):
  [Project] - AI/[SkillType] Assistant Config.md

LOG FILES (append-only, no pipeline):
  [Brand]-[Type]Log.md

SYSTEM FILES:
  Master config:    _AI/daily-ai-config.md
  Skill:            .claude/commands/[name].md
  Workflow:         _AI/WORKFLOWS/[name].md
  Template:         _AI/TEMPLATES/[name]-template.md
  System utility:   _AI/SYSTEM/[name].md
  Memory/log:       _AI/MEMORY/[name].md
  Session state:    _AI/MEMORY/session-state-[YYYY-MM-DD].md
  Portable copies:  Producer AI - Portable Config/Vault/.claude/commands/
  Starter pack:     Producer AI - Portable Config/
```

**KEY DISTINCTION — STAGING vs PLANNING:**
- `-Staging.md` = actively in pipeline. Will become prelive, then live. Has a destination.
- `-Planning.md` = living planning document. No destination. Updated directly. Never promoted.
- No suffix = live canonical. Source of truth.

**Examples:**
```
Sycthe of Athena - Chapter 1-Staging.md      pipeline file
Sycthe of Athena - Chapter 1-Prelive.md      pipeline file
Sycthe of Athena - Book.md                   live file (the novel)
Sycthe of Athena - Book-Planning.md          planning file (novel structure, decisions)
Sycthe of Athena - World Bible.md            live file (lore)
Sycthe of Athena - AI/Writing Assistant Config.md  config file
Duio - AI/Content Assistant Config.md              config file
Duio - ContentStrategy-Staging.md                  pipeline file (brand strategy, going to live)
```

---

## THE CLEAN SYSTEM CHECKLIST

A well-maintained system has:

- [ ] Every active project registered in daily-ai-config Section 08
- [ ] Every project has a Source of Truth file
- [ ] Every project has `[Project] - AI/[SkillType] Assistant Config.md` for each skill used on it
- [ ] No IP content inside skill command files (.claude/commands/)
- [ ] No generic behavior rules inside project assistant config files
- [ ] All improvements tagged by source in the improvements log
- [ ] No orphaned staging files (untouched 30+ days)
- [ ] No files in wrong folders (run /audit to check)
- [ ] Portable copies in `Producer AI - Portable Config/Vault/.claude/commands/` are current

---

*System Architecture v1.0 | [[../_AI/daily-ai-config]] | Run /audit to check system health*
