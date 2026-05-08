# FILE ROUTING SYSTEM
**Version:** 2.0
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
- Phase 7 (non-dev) → `Working Files/Staging/[Project] - [Type]-Staging.md` → Prelive at project root → Live at project root

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
| New skill / agent | "create skill", "new agent", "add /command", new operating persona | `.claude/commands/[name].md` | Behavior-only. No IP. No project-specific content. Single file, under 100 active lines. Reads project files on demand via `$ARGUMENTS`. Use /write-a-skill to create. |
| Config file (system-level) | `daily-ai-config`, `AI-Producer-Config`, session startup files | `_AI/` root or `_AI/SYSTEM/` | System Change Protocol applies. Staging → Prelive → Live gate. Never auto-edit live config. |
| Workflow file | "workflow", "process doc", defines a repeatable operating sequence | `_AI/WORKFLOWS/[name].md` | Staging first. Reference from config. Test before live. |
| Template | "template", "blank format", reusable structure | `_AI/TEMPLATES/[name].md` | No staging gate needed. Used as input to other files, not a live artifact itself. |
| System utility | checker, log-check, session starter, context tools | `_AI/SYSTEM/[name].md` | System Change Protocol. Flag before creating. |
| Memory / log | improvement log, session state, decisions log | `_AI/MEMORY/[name].md` | Append-only. Never overwrite existing entries. |

---

### PROJECT FILES

| Request Type | Detection Signals | Correct Location | Protocol |
|---|---|---|---|
| New working file / draft | "draft", "start working on", "build out", any new project content | `[Project] - Working Files/Staging/[Project] - [Type]-Staging.md` | Always staging first. Never skip to prelive or live. Staging files live in Working Files/Staging/ — never at project root. |
| Prelive file | "ready for review", "push to prelive", staging is complete | `[Project] - [Type]-Prelive.md` at project root | Operator's working document. Stays at project root for quick access. Never auto-flag as stale. |
| Live file | "push live", "make it live", "promote", "publish" | `[Project] - [Type].md` or `[Project] - [Type]-Live.md` at project root | Live gate. Explicit confirmation required. Stays at project root. |
| Planning file | "planning doc", "track decisions", "living document", "no pipeline path" | `Working Files/Planning/[IP or Project] - [Type]-Planning.md` | Updated directly. No pipeline. Permanent living document. |
| World bible / lore | world rules, character profiles, lore entries | Staging: `Working Files/Source of Truth/[Project] - World Bible-Staging.md` → Live: project root | Live version stays at project root. All edits go to staging in Source of Truth/. |
| GDD | game design document, game spec, mechanic spec | Staging: `Working Files/Source of Truth/[Project] - [Name] GDD-Staging.md` → Live: project root | GDD is the source of truth for a game. Staging in Source of Truth/. Live stays at root. |
| Chapter / scene | prose, novel chapters, comic scripts | `Working Files/Staging/[Project] - Chapter [N]-Staging.md` | Staging first. See Athena workflow for specifics. |
| Project Assistant Config | "[skill] config for [project]", "how to work on [project]" | `Working Files/AI/[SkillType] Assistant Config.md` | Created by skill's built-in Config Creation Protocol. Maintained directly (no pipeline). Replaces legacy [Project] - AI/ folder at root. |
| PRD | "write a PRD", "product requirements", planning a feature or system | `Working Files/PRDs/[Name] - PRD-Staging.md` | Run /prd. Staging pipeline applies. Never save project PRDs to `_AI/`. |
| Source of Truth | world bible, content strategy, GDD, business plan, brand bible | Staging: `Working Files/Source of Truth/[Name]-Staging.md` → Live: project root | Live gate. Live file stays at project root for quick access. All staging in Working Files/Source of Truth/. Never save to `_AI/`. |
| Production brief | animation brief, game brief, brief assessment, concepts, level design | `Working Files/Briefs/[Project] - [Type]-Staging.md` | Staging in Briefs/. |
| Research file | GitHub reference scan, market research, reference material | `Working Files/Research/[Project] - [Type].md` | No pipeline gate. Reference only. |
| System PRD | infrastructure PRD for the AI system itself, no project IP | `_AI/System PRDs/[Name] - PRD-Staging.md` | System Change Protocol applies. Zero project IP permitted. |
| Execution plan | "prd-to-plan", "vertical slice plan", output of /prd-to-plan or /prd-to-issues | `plans/[feature].md` at vault root | Created by /prd-to-plan or /prd-to-issues. No staging gate — it IS the plan document. Never promoted to live. Checkbox format. |

---

### CONTENT FILES

| Request Type | Detection Signals | Correct Location | Protocol |
|---|---|---|---|
| Content strategy | "strategy", "content plan", platform/brand planning | Staging: `Working Files/Strategy/[Brand] - ContentStrategy-Staging.md` → Live: brand root | Staging in Strategy/. Prelive + Live at brand root. |
| Growth / acquisition strategy | "user acquisition", "growth plan", "marketing strategy" | `Working Files/Strategy/[Brand] - [Type]-Staging.md` → Live: brand root | Staging in Strategy/. Same pipeline as ContentStrategy. |
| Content log | post tracking, what's been posted, pipeline tracker | `Working Files/Content/[Brand]-ContentLog-Staging.md` | Append-only log in Content/. Never delete entries — update status. |
| Short post draft | tweet, reel caption, short-form post | `Working Files/Content/[Brand]-ContentLog-Staging.md` → add entry | Log it immediately. Status: Draft. |
| Article draft | 400+ words, long-form, thought leadership | `Working Files/Content/[Brand]-[Topic]-Article-Staging.md` | Separate file per article. Staging in Content/. |
| Trending music entry | music reference, content hooks | `HatchFox Studios - Business/HatchFox Content - Brand/Trending Music/HatchFox Content - Trending Music Log.md` | Append to log. Follow existing table format. |

---

### PROJECT STRUCTURE

| Request Type | Detection Signals | Correct Location | Protocol |
|---|---|---|---|
| New project folder | "new project", "start a folder for", new IP or client | `[Name] - [HierarchyType]/` at vault root or within parent | **Confirm hierarchy type before creating.** Must follow commercial hierarchy naming — see COMMERCIAL HIERARCHY section below. |
| Working Files subfolder | all non-live, non-prelive project files | `[Project] - Working Files/` with subfolders: `Staging/` `Planning/` `PRDs/` `TDD/` `Strategy/` `Content/` `Source of Truth/` `Briefs/` `Research/` `AI/` | Root stays clean: Live files, Prelive files, Working Files/, Archive/ only. Create subfolder on first use. Unknown future types: add new named subfolder inside Working Files/ — no system changes needed. |
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
PROJECT ROOT (clean — quick access only):
  [Project] - [Type].md                   ← live / canonical (no suffix = live)
  [Project] - [Type]-Live.md              ← live (with suffix variant)
  [Project] - [Type]-Prelive.md           ← operator's working document
  Working Files/                          ← all development files (subfolders below)
  Archive/                                ← all archived files

WORKING FILES SUBFOLDERS:
  Working Files/Staging/       ← -Staging.md drafts (chapters, overviews, concepts, exports)
  Working Files/Planning/      ← -Planning.md living docs (no pipeline)
  Working Files/PRDs/          ← product requirement docs
  Working Files/TDD/           ← test specs (code projects)
  Working Files/Strategy/      ← ContentStrategy, growth plans, acquisition strategy
  Working Files/Content/       ← ContentLog, article drafts, content planners
  Working Files/Source of Truth/ ← GDD, World Bible, Wiki, Brand Bible (staging versions only)
  Working Files/Briefs/        ← production briefs, game briefs, brief assessments
  Working Files/Research/      ← GitHub scans, reference docs, market research
  Working Files/AI/            ← assistant configs (replaces legacy [Project] - AI/ at root)

SYSTEM FILES:
  Skill:    .claude/commands/[name].md
  Workflow: _AI/WORKFLOWS/[name].md
  Template: _AI/TEMPLATES/[name].md
  System:   _AI/SYSTEM/[name].md
  Memory:   _AI/MEMORY/[name].md
```

**KEY DISTINCTION:**
- `-Staging.md` in Working Files/Staging/ = in the pipeline, being worked on
- `-Planning.md` in Working Files/Planning/ = permanent living doc, no pipeline
- Files in Working Files/Source of Truth/ = source-of-truth docs in development
- Root files (no staging suffix) = live canonical sources of truth
- Root -Prelive.md files = operator's active working documents
<<<<<<< HEAD
=======

**LIVE SUFFIX RULE — CANONICAL STANDARD:**
- Canonical live form = **no suffix**: `[Project] - [Type].md`
- `-Live.md` suffix = legacy variant, valid but not used for new files
- When encountering a `-Live.md` file: check if a no-suffix version also exists in the same location. If both exist → conflict. Flag immediately, do not commit until resolved. Resolve by keeping the no-suffix version and archiving the `-Live.md` version.
- When a `-Live.md` file exists alone (no no-suffix duplicate): auto-rename to no-suffix form. Do not ask — this is the canonical resolution.
- Never create a new file with `-Live.md` suffix. Always use no suffix for live files going forward.
>>>>>>> fc3ce3733171692dc66afa6a4bebd79e8f93a21b

**Examples:**
```
[Project Root]/
  Sycthe of Athena - Book.md                           ← live novel
  Sycthe of Athena - World Bible.md                    ← live world bible
  Sycthe of Athena - Chapter 2-Prelive.md              ← operator working doc

  Working Files/Staging/
    Sycthe of Athena - Chapter 1-Staging.md            ← prose in pipeline
  Working Files/Planning/
    Sycthe of Athena - Book-Planning.md                ← decisions/structure
  Working Files/Source of Truth/
    Sycthe of Athena - World Bible-Staging.md          ← world bible edits
    Athena Game - Rebuild GDD-Staging.md               ← GDD in development
  Working Files/AI/
    Writing Assistant Config.md                        ← skill config
<<<<<<< HEAD
  Working Files/Briefs/
    Athena Animations - MotionCapture-Staging.md       ← production brief
=======
>>>>>>> fc3ce3733171692dc66afa6a4bebd79e8f93a21b
  Working Files/Strategy/
    Duio - ContentStrategy-Staging.md                  ← strategy in pipeline
  Working Files/Content/
    Duio - ArticleTopic-Article-Staging.md             ← article draft
  Working Files/Research/
    AI Game Maker - GitHub Reference Scan.md           ← reference only
```

---

## WHAT TO DO IF THE TYPE IS UNCLEAR

1. State what you think it is and why
2. Present the two most likely options
3. Ask which the operator intends
4. Do not create the file until confirmed

Do not guess and create. A file in the wrong location is harder to fix than a one-question pause.

---

*File Routing System v2.0 | Part of [[../_AI/daily-ai-config]]*
