# FILE ROUTING SYSTEM
**Version:** 1.0
**Part of:** [[../_AI/daily-ai-config]]
**Status:** Active

Before creating any new file, folder, or asset — determine its type and follow the correct process. This system prevents wrong-location files, pipeline violations, and context bleed between projects.

---

## 7-PHASE DEVELOPMENT PROCESS

Every project — code, writing, content, or business — follows this process. Use this to identify which phase a request belongs to before routing it to a skill or file type.

| Phase | Name | What happens | Dev (code/GitHub) | Non-dev (writing/content/business) |
|---|---|---|---|---|
| 1 | Idea | Raw concept. No detail yet. | — | — |
| 2 | Grilling | Pressure-test the idea. Walk the design tree. Resolve dependencies. | /grill-me | /grill-me |
| 3 | Research | Validate assumptions. Explore vault, market, codebase. | — | — |
| 4 | Prototype | Build smallest testable version. Spike only. | — | — |
| 5 | PRD | Document requirements before building. Gates execution. | /prd | /prd |
| 6 | Plan | Break PRD into executable vertical slices. | /prd-to-plan · /prd-to-issues (GitHub) | /prd-to-plan |
| 7 | Execute + QA | Build, test, ship. | /tdd · /triage-issue · /git-guardrails · Ralph loop | /writer · /content · /ai-producer · Staging pipeline |

**Phase rules:**
- Never execute (Phase 7) without a plan (Phase 6)
- Never plan (Phase 6) without an approved PRD (Phase 5)
- PRD gates the build — unapproved PRD means no plan, no execution
- Prototype and Research may run in parallel
- /grill-me can be re-run at any phase when new complexity surfaces
- /prd-to-issues requires GitHub — Obsidian-only projects use /prd-to-plan only
- Ralph loop requires a plan file and git — non-dev projects use the Staging → Prelive → Live pipeline

**File output by phase:**
- Phase 5 → `[Project] - PRDs/[Name] - PRD-Staging.md`
- Phase 6 (dev) → `plans/[feature].md` + GitHub issues (GitHub-enabled projects)
- Phase 6 (non-dev) → `plans/[feature].md` (markdown only)
- Phase 7 (dev) → code files, git commits per task, Ralph loop progress.txt
- Phase 7 (non-dev) → working files at project root, Staging → Prelive → Live pipeline

---

## HOW TO USE THIS SYSTEM

When a request involves creating or modifying anything, ask:

1. **What is the type?** → Find the row in the matrix below
2. **Does this request already have a home?** → Check the relevant project registry
3. **Does this conflict with anything live?** → Check pipeline data flow rules
4. **Is this a new type that isn't in the matrix?** → Flag it, propose where it fits, get confirmation before creating

---

## FILE TYPE ROUTING MATRIX

### SYSTEM & AGENT FILES

| Request Type | Detection Signals | Correct Location | Protocol |
|---|---|---|---|
| New skill / agent | "create skill", "new agent", "add /command", new operating persona | `.claude/commands/[name].md` | Behavior-only. No IP. No project-specific content. Reads project files on demand via `$ARGUMENTS`. Use `/write-a-skill` to create. |
| Config file (system-level) | `daily-ai-config`, `AI-Producer-Config`, session startup files | `_AI/` root or `_AI/SYSTEM/` | System Change Protocol applies. Staging → Prelive → Live gate. Never auto-edit live config. |
| Workflow file | "workflow", "process doc", defines a repeatable operating sequence | `_AI/WORKFLOWS/[name].md` | Staging first. Reference from config. Test before live. |
| Template | "template", "blank format", reusable structure | `_AI/TEMPLATES/[name].md` | No staging gate needed. Used as input to other files, not a live artifact itself. |
| System utility | checker, log-check, session starter, context tools | `_AI/SYSTEM/[name].md` | System Change Protocol. Flag before creating. |
| Memory / log | improvement log, session state, decisions log | `_AI/MEMORY/[name].md` | Append-only. Never overwrite existing entries. |

---

### PROJECT FILES

| Request Type | Detection Signals | Correct Location | Protocol |
|---|---|---|---|
| New working file / draft | "draft", "start working on", "build out", any new project content | `[IP or Project] - [Type]-Staging.md` in project folder | Always staging first. Never skip to prelive or live. |
| Prelive file | "ready for review", "push to prelive", staging is complete | `[IP or Project] - [Type]-Prelive.md` | Generated from staging. Notify: "Prelive ready to review." |
| Live file | "push live", "make it live", "promote", "publish" | `[IP or Project] - [Type].md` (no suffix = live) | Live gate. Explicit confirmation required. No assumptions. |
| Planning file | "planning doc", "track decisions", "living document", "no pipeline path" | `[IP or Project] - [Type]-Planning.md` in project folder | Updated directly. No pipeline. Permanent living document. Not staged, not promoted. Different from working drafts. |
| World bible / lore | world rules, character profiles, lore entries | `[IP or Project] - World Bible-Staging.md` → Prelive → Live | Same pipeline. Lore is live-gated. Staging for all edits. |
| Chapter / scene | prose, novel chapters, comic scripts | `[IP or Project] - Chapter [N]-Staging.md` | Staging first. See Athena workflow for specifics. |
| Project Assistant Config | "[skill] config for [project]", "how to work on [project]" | `[ProjectFolder]/[Project] - AI/[SkillType] Assistant Config.md` | Created by skill via build-config protocol. Maintained directly (no pipeline). |
| PRD | "write a PRD", "product requirements", planning a feature or system | `[ProjectFolder]/[Project] - PRDs/[Name] - PRD-Staging.md` | Run /prd. Staging pipeline applies. Never save project PRDs to `_AI/`. |
| Source of Truth | world bible, content strategy, GDD, business plan, brand bible | `[ProjectFolder]/[Project] - Source of Truth/[Name].md` | Live gate. Staging pipeline for all edits. Never save to `_AI/`. |
| System PRD | infrastructure PRD for the AI system itself, no project IP | `_AI/System PRDs/[Name] - PRD-Staging.md` | System Change Protocol applies. Zero project IP permitted. |

---

### CONTENT FILES

| Request Type | Detection Signals | Correct Location | Protocol |
|---|---|---|---|
| Content strategy | "strategy", "content plan", platform/brand planning | `[Brand]/[Brand]-ContentStrategy-Staging.md` | Staging. Prelive when ready for brand review. |
| Content log | post tracking, what's been posted, pipeline tracker | `[Brand]/[Brand]-ContentLog-Staging.md` | Append-only log. Never delete entries — update status. |
| Short post draft | tweet, reel caption, short-form post | `[Brand]/[Brand]-ContentLog-Staging.md` → add entry | Log it immediately. Status: Draft. |
| Article draft | 400+ words, long-form, thought leadership | `[Brand]/[Brand]-[Topic]-Article-Staging.md` | Separate file per article. Staging first. |
| Trending music entry | music reference, content hooks | `HatchFox Studios - Business/HatchFox Content - Brand/Trending Music/HatchFox Content - Trending Music Log.md` | Append to log. Follow existing table format. |

---

### PROJECT STRUCTURE

| Request Type | Detection Signals | Correct Location | Protocol |
|---|---|---|---|
| New project folder | "new project", "start a folder for", new IP or client | `[Name] - [HierarchyType]/` at vault root or within parent | **Confirm hierarchy type before creating.** Must follow commercial hierarchy naming — see COMMERCIAL HIERARCHY section below. |
| New subfolder | "add a folder inside", "organise [x] into subfolders" | Within the relevant project folder | Justify the subfolder. Does this project have enough volume to warrant it? If multiple projects of same type exist (or will), use `[Type] - Category/` grouping. Flag if it risks clutter. |
| Daily note | session log, project notes for the day | `Sasha - Person/Daily Notes/YYYY-MM-DD.md` | Single flat note per day. No per-project subfolders in Sasha - Person/Daily Notes/. Recordings auto-managed to `Sasha - Person/Daily Notes/Recordings/`. |

---

### CODE FILES

| Request Type | Detection Signals | Correct Location | Protocol |
|---|---|---|---|
| Application code | `.js`, `.ts`, `.py`, `.cs`, React components, scripts | Project's code folder (outside vault if applicable) | **Confirm scope before creating.** Code files live outside the Obsidian Vault unless they are tool configs or embedded scripts. |
| Embedded script / data file | JSON config, `.md` with code blocks, data structures | Within project folder as Staging file | Staging pipeline applies. Flag if it crosses into code territory. |
| AI image prompts | Midjourney, DALL-E, SD prompts | `[ProjectName]-ImagePrompts.md` in project folder | Staging file. Not a live artifact. |

---

## _AI/ GUARDRAIL — NON-NEGOTIABLE

`_AI/` is the global system folder. It contains operating system files only.

**NEVER save to `_AI/`:**
- Project PRDs, project assistant configs, world bibles, content strategies
- Source of truth files, brand strategies, or any project IP
- Staging files for project work

**Only these belong in `_AI/`:**
- `daily-ai-config.md` — master config
- `SYSTEM/` — protocols and utilities
- `WORKFLOWS/` — process flows
- `TEMPLATES/` — blank starters
- `MEMORY/` — improvements log, session state
- `System PRDs/` — infrastructure PRDs for the AI system itself (no project IP)

If a file belongs to a project → it goes into the project folder.
If unsure → run `/file-route [description]` before creating.

---

## CONFLICT CHECKS — RUN BEFORE CREATING

Before creating any file, check:

1. **Does a file by this name already exist?** If yes — are you updating it (→ edit staging) or duplicating it (→ flag and confirm)?
2. **Is there a live version of this file?** If yes — changes go to staging, not direct edits.
3. **Does this file belong to a specific project's IP?** If yes — it stays in that project's folder. It does not enter shared tools, workflows, or skill configs.
4. **Is this a system-level change?** If yes — System Change Protocol applies. State what changes, risk level, and get confirmation.

---

## COMMERCIAL HIERARCHY — FOLDER NAMING

Every top-level and second-level folder must follow this convention: `[Name] - [HierarchyType]`

### HIERARCHY TYPES

| Type | Meaning | Examples |
|---|---|---|
| `- Business` | A legal or commercial entity (company, studio, agency) | `HatchFox Studios - Business`, `TBST Digital - Business` |
| `- Brand` | A brand within a Business, or a standalone brand | `Athena 2327 - Brand`, `HatchFox Content - Brand` |
| `- Brand Project` | A specific project, product, or IP under a Brand | `Sycthe of Athena - Brand Project`, `Spiders Comic - Brand Project` |
| `- Hobby` | Personal creative work not tied to a Business | `[ProjectName] - Hobby` |
| `- Person` | Individual personal life, daily notes, personal projects | `Sasha - Person` |
| `- Category` | Grouping folder for multiple Brand Projects of the same type | `Comic - Category`, `Game - Category` |

### WHEN TO APPLY

**On every new folder creation:**
1. Identify the correct hierarchy type from the table above
2. Name the folder: `[Name] - [HierarchyType]`
3. Confirm parent location matches the hierarchy (Brand Project goes inside Brand, Brand goes inside Business)
4. Flag if the name looks like a placeholder — propose proper names when more project context exists

**On file creation inside a folder:**
- Use the folder's hierarchy type to inform the file prefix: `[ProjectName] - [FileType]-[Stage].md`
- Never create a Brand Project file inside a Business folder without a Brand folder in between

### PLACEHOLDER NAMES — WHAT TO WATCH FOR

Flag any folder or project name that matches these patterns:
- Generic descriptor ("AI Game Maker", "Content Creator", "New Project")
- Version number as name ("V4", "Project 2", "Brand X")
- Internal working name with no market identity

On flag: suggest alternatives when more context is available. Never rename without operator confirmation.

---

## NAMING CONVENTIONS — UNIVERSAL

All project files use the format: `[IP or Project] - [Type]-[Stage].md`

```
PIPELINE FILES (go through Staging → Prelive → Live):
  [IP or Project] - [Type]-Staging.md     ← working draft
  [IP or Project] - [Type]-Prelive.md     ← pending review
  [IP or Project] - [Type].md             ← live / canonical (no suffix = live)

PLANNING FILES (living documents, no pipeline):
  [IP or Project] - [Type]-Planning.md    ← permanent, updated directly

CONFIG FILES (maintained by skill, no pipeline):
  [Project] - AI/[SkillType] Assistant Config.md

LOG FILES (append-only):
  [Brand]-[Type]Log.md                    ← ContentLog, etc. No staging gate.

SYSTEM FILES:
  Skill:    .claude/commands/[name].md
  Workflow: _AI/WORKFLOWS/[name].md
  Template: _AI/TEMPLATES/[name].md
  System:   _AI/SYSTEM/[name].md
  Memory:   _AI/MEMORY/[name].md
  Config:   [System]-Config.md            ← AI Producer, etc.
```

**KEY DISTINCTION:**
- `-Staging.md` = in the pipeline, being worked on, will be promoted
- `-Planning.md` = permanent document, decisions and structure, never promoted
- No suffix = live / canonical source of truth

**Examples:**
```
Sycthe of Athena - Chapter 1-Staging.md      ← prose in pipeline
Sycthe of Athena - Chapter 1-Prelive.md      ← pending review
Sycthe of Athena - Book.md                   ← live novel (no suffix)
Sycthe of Athena - Book-Planning.md          ← planning/decisions (no pipeline)
Sycthe of Athena - Writing Assistant Config.md ← skill config (no suffix)
Sycthe of Athena - World Bible.md            ← live world bible
Sycthe of Athena - World Bible-Staging.md    ← updates to world bible (in pipeline)
```

---

## WHAT TO DO IF THE TYPE IS UNCLEAR

1. State what you think it is and why
2. Present the two most likely options
3. Ask which the operator intends
4. Do not create the file until confirmed

Do not guess and create. A file in the wrong location is harder to fix than a one-question pause.

---

*File Routing System v1.0 | Part of [[../_AI/daily-ai-config]]*
