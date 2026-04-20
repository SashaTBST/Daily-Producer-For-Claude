◆ HOW TO USE — PRODUCER AI SYSTEM ◆
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

1. Open Claude Code with your workspace as the working directory
2. Type `/daily`
3. The system automatically runs: new files check → project audit → improvement flags → proposes first action
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
| Game Brief | Game project work | `/game-brief` |

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
2. Create a folder for the project
3. Run `/build-config [skill] [project]` for each relevant skill — this builds the AI assistant for that project
4. Create the first staging file with `/stage`

The AI handles links and cross-references once the registry is updated.

---

## COMMANDS — FULL REFERENCE

```
STARTUP
  /daily                     — Full auto-run: audit + improvement flags + first action

DAILY OPERATIONS
  /brief                     — Priority stack across all projects
  /check                     — Scan vault for new/unprocessed files
  /status [project]          — Full project status
  /audit                     — Project audit: missing configs, stale files, gaps
  /focus [role]              — Shift role context (writer / pm / ep / etc.)

CREATIVE & CONTENT
  /content [brief]           — Content creation pipeline → staging
  /concept [topic]           — Ideation session
  /draft [description]       — Draft to staging
  /edit [text or reference]  — Review existing copy
  /script [type + brief]     — Script to staging

PROJECT & BUSINESS
  /plan [project]            — Build or update project plan
  /review [project]          — Business review
  /risk [project]            — Surface risks and assumptions

FILE PIPELINE
  /stage [filename]          — Create staging file
  /push-prelive [filename]   — Promote to prelive
  /push-live [filename]      — Live gate (always confirms first)
  /file-route [description]  — Determine correct file location before creating

SYSTEM & AI
  /build-config [skill] [project]  — Build an AI assistant config for a project
  /new-skill [type]          — Create a new skill for a new discipline
  /ai-producer               — Load producing methodology for any project
  /improve                   — Trigger self-improvement review
  /vault-check               — Weekly maintenance: naming, stale files, broken references
  /save-session              — Save state before closing a long session
```

---

## SYSTEM FILE MAP

```
_AI/                              ← All AI system files
├── daily-ai-config.md            ← Master config — configure this on first use
├── HOW-TO-USE.md                 ← You are here
├── SYSTEM/
│   ├── daily-run.md              ← Efficient session runner
│   ├── log-check.md              ← Single-pass log sweep
│   ├── new-files-checker.md      ← Daily vault scan
│   ├── system-guide.md           ← How the system works (technical reference)
│   ├── system-architecture.md    ← Full five-level architecture spec
│   ├── vault-maintenance-checker.md  ← Weekly cleanup protocol
│   ├── context-window-protocol.md   ← Context window management
│   └── self-improvement-framework.md
├── WORKFLOWS/
│   ├── daily-brief.md            ← Morning setup
│   ├── project-status.md         ← Deep project review
│   ├── content-creation.md       ← Content pipeline
│   ├── business-review.md        ← Strategy / commercial
│   ├── file-routing.md           ← File type and location routing
│   └── game-brief.md             ← Game project protocol
├── TEMPLATES/
│   ├── staging-template.md       ← All staging files start here
│   ├── daily-note-generic.md     ← General daily note
│   ├── writing-assistant-config-template.md
│   ├── content-assistant-config-template.md
│   ├── game-assistant-config-template.md
│   └── producer-assistant-config-template.md
└── MEMORY/
    └── improvements-log.md       ← Self-improvement history

.claude/commands/
├── daily.md                      ← Configure $WORKSPACE path on first deploy
├── writer.md
├── content.md
├── game-maker.md
├── ai-producer.md
└── [name].md                     ← All skills live here. Use /write-a-skill to add new ones.

Daily Notes/                      ← Created as you go — one note per date

[Your Project Folders]/           ← Created as you add projects
```

---

PRODUCER AI SYSTEM v1.4 | Run /daily to start
