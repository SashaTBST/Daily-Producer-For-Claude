# SYSTEM GUIDE
**Version:** 1.0
**Type:** Living technical reference — updated as the system evolves
**Companion to:** STARTER-GUIDE.md (user-facing) | system-architecture.md (full spec)

This document explains how the system works: its logic, hierarchy, and the way files communicate with each other. Not a command reference. Not a project list. The operating model.

---

## THE CORE LOGIC

One command (`/daily`) runs the full system every session. The system is hierarchical — each level informs the one below it. Output flows up through a pipeline (Staging → Prelive → Live). Learning flows to a persistent log.

The system can run with any subset of its components. Removing a layer degrades capability but breaks nothing.

---

## THE HIERARCHY — FIVE LEVELS

```
L0  Master System          _AI/daily-ai-config.md + SYSTEM/ + WORKFLOWS/ + TEMPLATES/ + MEMORY/
L1  Skills                 .claude/commands/[name].md
L2  Project Configs        [ProjectFolder]/[Project] - [SkillType] Assistant Config.md
L3  Source of Truth        [ProjectFolder]/[Project] - [Type].md  (World Bible, Content Strategy, etc.)
L4  Working Files          [Project] - [Name]-Staging.md → Prelive → Live
L5  Memory & Feedback      _AI/MEMORY/improvements-log.md | session-state files
```

**Direction rules:**
- Operating context flows DOWN: L0 → L1 → L2 → L3
- Output flows UP: Staging → Prelive → Live
- Learning flows to L5 (tagged by source)
- Nothing flows backward automatically — edits to live go back through staging

---

## HOW A SESSION FLOWS

```
/daily
  ↓
Daily Skill (L1) reads master config (L0)
  → Checks improvements log (L5) for staged items
  → Scans vault for new/unprocessed files
  → Runs Project Audit: checks each active project against L1/L2/L3/L4
  → Reports + proposes first action
  ↓
Skill activated (L1)
  → Reads Project Assistant Config (L2) for the target project
  → Reads Source of Truth (L3) on demand for consistency
  → Produces output → Staging (L4)
  ↓
End of session
  → Skill proposes new L2 rules learned this session
  → Improvement flags written to L5 (tagged by source)
  → If context filling: session state written to L5
```

---

## THE THREE-LAYER SKILL PATTERN

Every skill operates against three documents:

**L1 — Skill** (generic behavior)
What this role does. Its hard limits. Its checks. Its output format. No IP. No project content.

**L2 — Project Assistant Config** (project-specific behavior)
HOW to apply the skill to THIS project. Voice, tone, constraints, open questions. Created by the skill's Config Creation Protocol (a question set). Grows as the project develops. No generic rules — those stay in L1.

**L3 — Source of Truth** (facts)
WHAT the project contains. World Bible, Content Strategy, GDD, etc. Not behavior — facts. Skills read this for consistency, not for operating rules.

---

## FILE TYPES — THE PATTERN

| Suffix | Type | Pipeline? | Updated how? |
|---|---|---|---|
| `-Staging.md` | Draft in pipeline | Yes → Prelive → Live | Worked on each session |
| `-Prelive.md` | Pending review | Yes → Live | Reviewed, not edited |
| no suffix | Live / canonical | No | Only via staging |
| `-Planning.md` | Living planning doc | No | Directly, any session |
| `Assistant Config.md` | Skill config | No | Skill appends, operator approves |

**KEY DISTINCTION:**
`-Staging` has a destination. It will become prelive, then live.
`-Planning` is permanent. It never gets promoted. It is always current.

---

## THE PROJECT AUDIT

Runs automatically at every `/daily`. Also available as `/audit`.

For each active project in the registry (L0):

1. **Skill match** — which skills apply? Any skill without a config flagged.
2. **Assistant Config check** — does a L2 config exist for each skill? Missing = flag.
3. **Source of Truth check** — does a L3 source exist? Missing = flag.
4. **Stale files** — any Staging untouched 7+ days? Flag for action or cleanup.

---

## THE PIPELINE RULE

```
Staging → Prelive → Live
```

- Never push prose directly to Live
- Never pull Live back to Staging without permission
- Flag conflicts before acting
- Prelive = awaiting review only — not for editing

When citing issues across pipeline files, always include file + line number (Staging L12 / Prelive L34).

---

## SELF-IMPROVEMENT — THREE LEVELS

| Level | What happens | Logging |
|---|---|---|
| L1 — Micro | Real-time small adjustments mid-session | None |
| L2 — Session-end | Flags patterns noticed. You review. | Improvements log |
| L3 — Config change | Proposes edits to L0/L2. Requires approval before applying. | Improvements log + staging |

All logged improvements use source tags: `[SYSTEM]` `[SKILL: name]` `[IP: name]` `[TEMPLATE]`

---

## THE CHECKERS

**`/audit`** — Project health. Runs at session start automatically.
Checks: skill coverage, missing configs, source of truth, stale files.

**`/vault-check`** — Weekly structural audit.
Checks: naming violations, stale files, broken references, cross-folder contamination, orphaned files, config coverage, source of truth coverage.

**`/log-check`** — Single-pass log sweep.
Checks: improvements log (staged items), AI producer log (open items), Athena pipeline (blockers).

---

## FILE COMMUNICATION — HOW COMPONENTS REFERENCE EACH OTHER

```
daily-ai-config (L0)
  → references: all project folders, all skills, all workflows, MEMORY/
  → read by: daily skill at session start

.claude/commands/[name].md (L1)
  → references: Project Assistant Config for current project (L2)
  → references: Source of Truth on demand (L3)
  → writes output to: Staging (L4)
  → proposes to: improvements log (L5)

Project Assistant Config (L2)
  → built by: skill Config Creation Protocol
  → grows via: skill appends (per-addition operator approval)
  → references: Source of Truth for fact checks

Source of Truth (L3)
  → read by: skills for consistency
  → never modified during a session without explicit operator instruction

Staging / Prelive / Live (L4)
  → Staging: worked on directly
  → Prelive: read + reviewed only
  → Live: only updated via new staging pass

improvements-log (L5)
  → append-only
  → tagged by source
  → staged items reviewed at session start via /log-check
```

---

## ADDING A NEW SKILL

Trigger: `/new-skill [type]` or flagged by `/audit`

The skill creation protocol asks 7 questions (discipline, output, hard limits, pre-output checks, config question set, ideal session, AI failure modes) then writes a new `.claude/commands/[name].md`. Also creates an assistant config template and registers the skill in L0.

---

## ADDING A NEW PROJECT

1. Register in `daily-ai-config` Section 08 (project registry)
2. Create project folder
3. Run `/build-config [skill] [project]` for each relevant skill → creates L2 config
4. Create Source of Truth file (or flag as missing until it exists)

---

## ISOLATION RULES

Every level can be removed without hard-breaking the system:
- Remove a skill → projects keep configs and source of truth. Skill just isn't invoked.
- Remove a Project Config → skill falls back to generic behavior.
- Remove Source of Truth → skill loses consistency check. Flags the gap.
- Remove a project from registry → becomes inactive. Files remain. Re-register to restore.
- Remove daily config → skills still run standalone. Master protocol lost, core behavior intact.

---

*System Guide v1.0 | Living document — update when system logic changes | See system-architecture.md for full spec*
