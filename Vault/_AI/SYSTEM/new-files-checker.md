◆ SYSTEM: NEW FILES CHECKER ◆
Run via: /check
Executes automatically at session start via [[_AI/SYSTEM/session-starter]]

---

PURPOSE

Scans the vault every session to ensure nothing is missed. New files, unprocessed notes, unlinked content, and open actions are surfaced before work begins. This is the system's safety net.

---

SCAN PROTOCOL

Run in this order each session. Report findings for each zone.

ZONE 1 — DAILY NOTES
Scan: [YourName] - Person/Daily Notes/**/*.md  ← RECURSIVE. Includes Notes/, Recordings/, Transcripts/ subfolders. Do not scan root only.

STEP 1 — Unprocessed notes
Two separate checks — both required:

A) Explicit unprocessed flag:
→  Grep for: `processed: false` across all .md files (recursive)
→  Any match = unprocessed. Flag and action.

B) Missing frontmatter (no `processed:` field at all):
→  For every file found in the recursive scan: check whether it contains the string `processed:`
→  Any file with NO `processed:` field = treat as unprocessed. Flag and action.
→  This catches raw voice notes, brain dumps, and any file dropped into the folder without frontmatter.

→  Note: `processed: true` does NOT mean no open actions. Steps 1–3 are independent checks.

STEP 2 — Open action items
→  Grep for: `- [ ]` across all .md files (recursive)
→  Report every unchecked item found, grouped by file
→  Do not skip notes marked `processed: true` — they can still have open actions
→  Cross-check against improvements-log and session-state to identify which items are already resolved before surfacing

STEP 3 — Unlinked or raw content
→  Any note with raw data (transcript, ideas, inputs) that hasn't been processed into a staging file
→  Any note with content that hasn't been linked to a project file

Report per-note:
[Note file] — [status: processed / has open actions / has unlinked content / unprocessed data]
[Open action items listed below the note if found]

ZONE 2 — PROJECT FILES
Scan: HatchFox Studios - Business/ | TBST Digital - Business/
Check for:
→  Any new files that don't appear in the active project registry ([[_AI/daily-ai-config]] Section 08)
→  Any staging files that haven't been touched in 7+ days (flag as stale)
→  Any prelive files that haven't been reviewed or promoted (flag as pending review)
→  Any file with no Obsidian links (isolated node — flag for linking)

Report per-project:
[File] — [status: new unregistered / stale staging / pending prelive review / isolated node]

ZONE 3 — _AI SYSTEM
Scan: _AI/
Check for:
→  Any staging files ready to be promoted
→  Any improvements in [[_AI/MEMORY/improvements-log]] with status "Staged" that haven't been reviewed
→  Any workflow or template that references a file that no longer exists

RULE INTEGRITY CHECK (run every session):
→  Read CLAUDE.md. Extract every rule listed under "Core Rules (loaded automatically from `.claude/rules/`)".
→  For each rule filename listed: confirm the file exists at `.claude/rules/[filename]`.
→  If any listed rule file does not exist → flag: "MISSING RULE: [filename] — listed in CLAUDE.md but does not exist. System never loads it."
→  This check catches the class of error where a rule is documented as created but was never written.

ACTIVE PLANS CHECK (run every session):
→  Scan `plans/` for any `.md` file containing `status: IN PROGRESS` or `STATUS: IN PROGRESS`.
→  For each active plan: check for any unchecked criteria (`- [ ]`).
→  Report: "[plan-name] — [N] criteria incomplete" and surface the first unchecked item.
→  System changes and infrastructure work are not complete until the plan is closed. Do not skip this.

PATH RESOLUTION CHECK (first-run detection — run once):
→  Read `.claude/settings.json`. Check if any hook path values are relative (no drive letter).
→  If relative paths found: this is a portable config install that has not been localised.
→  Flag: "PORTABLE INSTALL DETECTED — settings.json uses relative paths. Offer to resolve to absolute paths for this machine."
→  When operator confirms: replace all relative paths in settings.json with absolute paths using the vault root as the base.
→  After resolution: this check passes silently for all future sessions.

Report:
[File] — [status: ready for prelive / improvement pending review / broken reference / MISSING RULE / active plan with open items]

ZONE 4 — UNLINKED CONTENT
Cross-check:
→  Any file in the vault with zero incoming or outgoing Obsidian links
→  Any concept, character, or project name mentioned in a note that doesn't have a link

Report:
[File or note] — [suggested link to create]

ZONE 5 — PORTABLE CONFIG SYNC
Scan: $WORKSPACE/
Check for:
→  Any live system file updated more recently than its portable counterpart
→  Any live system file that has no portable counterpart (and should)
→  IP-specific files (daily-note-athena, writing-athena workflow, etc.) are exempt — they should NOT be in portable

Files to check (live path → portable path):
  _AI/SYSTEM/daily-run.md
  _AI/SYSTEM/new-files-checker.md
  _AI/SYSTEM/session-starter.md
  _AI/SYSTEM/system-architecture.md
  _AI/SYSTEM/system-guide.md
  _AI/SYSTEM/self-improvement-framework.md
  _AI/SYSTEM/vault-maintenance-checker.md
  _AI/SYSTEM/log-check.md
  _AI/SYSTEM/context-window-protocol.md
  _AI/WORKFLOWS/file-routing.md
  _AI/WORKFLOWS/content-creation.md
  _AI/WORKFLOWS/daily-brief.md
  _AI/WORKFLOWS/project-status.md
  _AI/WORKFLOWS/business-review.md
  _AI/WORKFLOWS/game-brief.md
  _AI/TEMPLATES/staging-template.md
  _AI/TEMPLATES/daily-note-generic.md
  _AI/TEMPLATES/writing-assistant-config-template.md
  _AI/TEMPLATES/content-assistant-config-template.md
  _AI/TEMPLATES/game-assistant-config-template.md
  _AI/TEMPLATES/producer-assistant-config-template.md
  .claude/skills/daily/SKILL.md
  .claude/skills/writer/SKILL.md
  .claude/skills/content/SKILL.md
  .claude/skills/game-maker/SKILL.md
  .claude/skills/ai-producer/SKILL.md
  .claude/skills/strategy/SKILL.md
  .claude/skills/prd/SKILL.md
  .claude/skills/write-a-skill/SKILL.md
  .claude/skills/grill-me/SKILL.md
  .claude/skills/prd-to-plan/SKILL.md
  .claude/skills/prd-to-issues/SKILL.md
  .claude/skills/tdd/SKILL.md
  .claude/skills/improve-codebase-architecture/SKILL.md
  .claude/skills/triage-issue/SKILL.md
  .claude/skills/ubiquitous-language/SKILL.md
  .claude/skills/git-guardrails/SKILL.md
  .claude/skills/edit-article/SKILL.md
  .claude/skills/design-an-interface/SKILL.md
  .claude/skills/qa/SKILL.md
  .claude/skills/request-refactor-plan/SKILL.md
  .claude/rules/pipeline.md
  .claude/rules/communication.md
  .claude/rules/file-routing.md
  .claude/rules/system-changes.md
  .claude/rules/self-improvement.md
  .claude/rules/context-window.md
  .claude/rules/commands-standard.md
  .claude/rules/build-protocol.md
  .claude/settings.json

Note: portable skills use $WORKSPACE placeholder — do not overwrite with hardcoded paths.
Note: portable daily-ai-config.md is a generic version — do not sync directly from live (contains IP/project data).

Report:
[File] — [live timestamp] vs [portable timestamp] — [CURRENT / BEHIND / MISSING]

---

ZONE 6 — GITHUB REPOS
Tool: gh CLI (must be installed and authenticated via `gh auth login`)
Account: Set in daily-ai-config.md — GITHUB REPOS REGISTRY
Command: `gh repo list [account] --limit 50 --json name,visibility`

STEP 1 — Registry drift
→  Run `gh repo list` and compare against GITHUB REPOS REGISTRY in daily-ai-config.md
→  Any repo on GitHub not in the registry → flag: "UNREGISTERED REPO: [name] — add to registry"
→  Any repo in registry marked LIVE that no longer exists on GitHub → flag as deleted/renamed

STEP 2 — Open PRs
→  For each registered repo (skip any repos marked as auto-managed/Claude Design): run `gh pr list --repo [account]/[name] --state open`
→  Report any open PRs: [repo] — [PR title] — [author] — [age]
→  If no open PRs: report "No open PRs"

STEP 3 — Local git status (known clones only)
→  Run `git status --short` for each repo with a known local path in the registry
→  Report uncommitted changes, unpushed commits, or dirty state
→  Repos with PATH UNKNOWN: skip and flag for path confirmation

Note: Auto-managed repos (e.g. connected to design tools) — do not check, do not push manually.
Note: Any repo with a documented push gate — surface as a standing reminder every session.

Report:
[Repo] — [open PRs: N] — [local status: clean / N uncommitted / N unpushed / PATH UNKNOWN]
UNREGISTERED: [any new repos found or NONE]

---

OUTPUT FORMAT

## NEW FILES CHECK — [DATE]

### DAILY NOTES
| Project | Date | Status | Action Needed |
|---------|------|--------|---------------|
| [project] | [date] | [status] | [action or NONE] |

### PROJECT FILES
| File | Status | Action Needed |
|------|--------|---------------|
| [file] | [status] | [action or NONE] |

### _AI SYSTEM
| File | Status | Action Needed |
|------|--------|---------------|
| [file] | [status] | [action or NONE] |

### UNLINKED CONTENT
| File | Suggested Link |
|------|---------------|
| [file] | [[suggested-link]] |

### PORTABLE CONFIG SYNC
| File | Live | Portable | Status |
|------|------|----------|--------|
| [file] | [timestamp] | [timestamp] | [CURRENT / BEHIND / MISSING] |

### GITHUB REPOS
| Repo | Open PRs | Local Status |
|------|----------|--------------|
| [repo] | [N or NONE] | [clean / dirty / PATH UNKNOWN] |
UNREGISTERED: [list or NONE]

### SUMMARY
[X] items need attention. [Y] are clear.
Priority: [the one thing to address first if anything is flagged]

---

CHECKER RULES
→  Run at every session start. Do not skip.
→  Surface everything — do not filter or triage silently.
→  When an action is needed, state it specifically. Not "review this" — "create a staging file from the inputs in this daily note."
→  A clear vault is a clear mind. Flag small things before they become lost things.

---

END NEW FILES CHECKER
Auto-runs via [[_AI/SYSTEM/session-starter]] | Manual run: /check
