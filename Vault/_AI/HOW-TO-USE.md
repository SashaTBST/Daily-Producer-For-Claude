◆ HOW TO USE — DAILY AI SYSTEM ◆
v1.4 | Day-to-day operating guide. Read this once. Reference when needed.

---

## THE ONE THING TO KNOW

Open Claude Code in your workspace and type:
```
/daily
```

That's it. Every session starts with `/daily`. Everything else runs automatically.

---

## STARTING A SESSION

1. Open Claude Code with your vault as the working directory
2. Type `/daily`
3. The system automatically runs: log check + session recovery → vault scan → project pulse → proposes first action
4. Session ready. Work begins.

---

## LOADING A WORKFLOW

Workflows activate specialist processes for specific work types.

| Workflow | When to use | Command |
|---|---|---|
| Daily Brief | Start of day, priority stack | `/brief` |
| Project Status | Reviewing a specific project | `/status [project]` |
| Content Creation | Making any content | `/content [brief]` |
| Business Review | Strategy or commercial decisions | `/review [project]` |
| Writing — Athena | Any Athena 2327 work | `/athena` |
| Game Brief | AI Game Maker work | `/game-brief` |

---

## THE FILE PIPELINE

| Stage | When | Naming | Gate |
|---|---|---|---|
| Staging | All drafts | `[Project] - [Name]-Staging.md` | None — create freely |
| Prelive | Ready for review | `[Project] - [Name]-Prelive.md` | On request |
| Live | Final, approved | `[Project] - [Name].md` | Explicit approval required |
| Planning | Living planning doc | `[Project] - [Name]-Planning.md` | None — update directly |

Never ask for something "final". Ask for staging. Review. Promote when ready.

---

## ADDING A NEW PROJECT

1. Add it to Section 08 of `_AI/daily-ai-config.md` (Active Projects Registry)
2. Create a folder for the project using the `[Project] - AI/` · `[Project] - PRDs/` · `[Project] - Source of Truth/` structure
3. Run the relevant skill (e.g. `/writer`, `/strategy`) — each includes a built-in Config Creation Protocol that builds the AI assistant for that project
4. Create the first staging file with `/stage`

The AI handles links and cross-references once the registry is updated.

---

## COMMANDS — FULL REFERENCE

All commands are slash commands. Type `/name` in Claude Code.

```
STARTUP
  /daily                          — Full auto-run: log check → vault scan → project audit → first action

CREATIVE & CONTENT
  /writer [project]               — Editorial writing partner — prose, scripts, world-building
  /content [project]              — Content pipeline — posts, captions, articles (v1 — use /strategy for new projects)
  /strategy [project]             — Growth strategist — strategy docs, frameworks, content plans
  /ai-producer [project]          — Producing assistant — plans, oversight, cross-project
  /game-maker [project]           — Game tool operator — GDDs, studio workflow
  /game-brief                     — Game brief builder — generates PROMPT.md + brief.json
  /write-article [brand]          — Long-form SEO blog articles — requires brand Content Config
  /edit-article                   — Restructure and tighten an existing article draft

PERSONAL (IP — local only)
<<<<<<< HEAD
  Add personal skills here (e.g. bookkeeping, lifestyle, tax) — local-only, not in portable.
=======
  /bookkeeping                    — AUS sole trader bookkeeping (HatchFox + TBST)
  /tax-return                     — AUS personal tax return preparation
  /lifestyle-plan                 — Weekly lifestyle check-in and plan update
>>>>>>> fc3ce3733171692dc66afa6a4bebd79e8f93a21b

DEV PIPELINE — run in order
  /grill-me                       — Discovery interview — walk every branch of the design tree
  /domain-model                   — Vocabulary-enforced design interview — flags conflicts, creates ADRs
  /prd [description]              — Product Requirements Document — mandatory gate before any plan
  /prd-to-plan                    — Break PRD into vertical-slice plan → plans/[name].md
  /prd-to-issues                  — Break PRD into GitHub Issues (GitHub-enabled projects only)
  /tdd                            — TDD per slice — tracer bullet first, red-green-refactor
  /qa                             — QA session — file bugs as GitHub issues or Markdown entries
  /triage-issue [description]     — Classify and scope an issue before touching code

ARCHITECTURE & QUALITY
  /improve-codebase-architecture  — Audit for AI-navigability — spawns parallel interface designs
  /design-an-interface            — Generate multiple radically different interface designs
  /request-refactor-plan          — Interview-driven refactor plan → GitHub issue or plans/
  /ubiquitous-language            — Establish and maintain shared vocabulary
  /zoom-out                       — Map modules, callers, and entry points for an area
  /git-guardrails                 — Safe git practices — conventional commits, branch safety

SYSTEM
  /write-a-skill                  — Create a new skill in .claude/commands/ format
  /vault-check                    — Weekly maintenance — naming, stale files, broken refs, portable sync
<<<<<<< HEAD
  /improve                        — Trigger self-improvement review
  /save-session                   — Save state before closing a long session
=======
>>>>>>> fc3ce3733171692dc66afa6a4bebd79e8f93a21b
```

---

## SYSTEM FILE MAP

```
_AI/                              ← All AI system files
├── daily-ai-config.md            ← Master config — source of truth for this system
├── HOW-TO-USE.md                 ← You are here
├── SYSTEM/
│   ├── daily-run.md              ← Session startup sequence
│   ├── log-check.md              ← Single-pass log sweep (runs inside /daily)
│   ├── new-files-checker.md      ← Daily vault scan (Zones 1-6)
│   ├── system-guide.md           ← How the system works (technical reference)
│   ├── system-architecture.md    ← Full five-level architecture spec
│   ├── system-documentation.md   ← Complete system reference
│   ├── vault-maintenance-checker.md  ← Weekly cleanup protocol
│   ├── context-window-protocol.md   ← Context window save/resume
│   ├── self-improvement-framework.md
│   └── Archive/                  ← Retired/superseded system files
├── WORKFLOWS/
│   ├── file-routing.md           ← File type and location routing (core — read before creating any file)
│   ├── ralph-loop.md             ← Autonomous execution loop (PRD → plan → execute → commit → repeat)
│   ├── content-creation.md       ← Content pipeline
│   ├── business-review.md        ← Strategy / commercial
<<<<<<< HEAD
=======
│   ├── writing-athena.md         ← Athena 2327 editorial protocol (IP)
>>>>>>> fc3ce3733171692dc66afa6a4bebd79e8f93a21b
│   └── [other workflow files]
├── TEMPLATES/                    ← Blank starters — used by skills when creating new files
│   ├── staging-template.md
│   ├── daily-note-generic.md
│   ├── writing-assistant-config-template.md
│   ├── content-assistant-config-template.md
│   ├── prd-template.md
│   └── [other templates]
├── System PRDs/                  ← Infrastructure PRDs for the AI system itself (no project IP)
└── MEMORY/
    ├── improvements-log.md       ← Append-only improvement history
    └── session-state-[date].md   ← Context window saves

<<<<<<< HEAD
.claude/commands/                 ← All skills (30 files — one per skill; 3 are local-only)
├── daily.md                      ← Operating intelligence
├── writer.md, content.md, game-maker.md, ai-producer.md, strategy.md
├── game-brief.md, write-article.md, edit-article.md
├── [local-only skills]           ← IP — not in portable (bookkeeping, tax-return, lifestyle-plan, etc.)
=======
.claude/commands/                 ← All skills (28 files — one per skill)
├── daily.md                      ← Operating intelligence
├── writer.md, content.md, game-maker.md, ai-producer.md, strategy.md
├── game-brief.md, write-article.md, edit-article.md
├── bookkeeping.md, tax-return.md, lifestyle-plan.md  ← IP — local only
>>>>>>> fc3ce3733171692dc66afa6a4bebd79e8f93a21b
├── prd.md, grill-me.md, domain-model.md
├── prd-to-plan.md, prd-to-issues.md
├── tdd.md, qa.md, triage-issue.md, request-refactor-plan.md
├── improve-codebase-architecture.md, design-an-interface.md
├── ubiquitous-language.md, git-guardrails.md, zoom-out.md
<<<<<<< HEAD
├── write-a-skill.md, vault-check.md, improve.md, save-session.md
=======
├── write-a-skill.md, vault-check.md
>>>>>>> fc3ce3733171692dc66afa6a4bebd79e8f93a21b
└── [see daily-ai-config Section 04 for full registry]

.claude/rules/                    ← Auto-load every session (8 files)
├── pipeline.md, communication.md, file-routing.md
├── system-changes.md, self-improvement.md
├── context-window.md, commands-standard.md, build-protocol.md

plans/                            ← Execution plans from /prd-to-plan and /prd-to-issues

[Business] - Business/
├── [Brand] - Brand/
│   └── [Project] - Brand Project/
│       ├── [Project] - AI/           ← Assistant configs (L2)
│       ├── [Project] - PRDs/         ← Product requirements
│       ├── [Project] - Working Files/ ← All staging files
│       └── [Project] - Source of Truth/ ← World Bible / Strategy / GDD

Producer AI - Portable Config/    ← Portable starter pack (no IP) — synced to GitHub
```

---

DAILY AI SYSTEM v1.4 | Run /daily to start
