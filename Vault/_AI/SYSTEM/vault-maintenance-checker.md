# VAULT MAINTENANCE CHECKER
**Version:** 1.0
**Run via:** `/vault-check` or `/audit` (weekly)
**Part of:** [[../_AI/daily-ai-config]]
**Status:** Active

This checker runs periodic maintenance on the vault. It catches naming violations, stale files, broken references, cross-folder contamination, and files that need archiving or removal. Run weekly or before any major session.

Different from the new-files-checker (which runs daily and flags new/unprocessed content). This runs less frequently and is more structural.

---

## WHEN TO RUN

- Weekly, at the start of the first session of the week
- Before promoting any major file to live
- After any bulk rename or restructure
- When the vault feels messy or something seems off

---

## MAINTENANCE PROTOCOL — RUN IN ORDER

---

### STEP 1 — NAMING CONVENTION AUDIT

Check every project file against the universal naming convention:

**Pipeline files** must use: `[IP or Project] - [Type]-Staging.md` / `-Prelive.md` / `.md` (live)
**Planning files** must use: `[IP or Project] - [Type]-Planning.md`
**Config files** must use: `[IP or Project] - [SkillType] Assistant Config.md`

**Violations to flag:**
- Files using the old `[Project]-[File]-Staging` format (hyphen only, no space-dash-space)
- Files using generic names with no IP/Project prefix (e.g. `Writing-config.md`, `Staging.md`)
- Files with `-Staging` suffix that are actually planning documents (no pipeline path)
- Files in the wrong pipeline stage (staging that should have been promoted weeks ago)
- Any `-Staging.md` file directly in `Working Files/` root — should be in a subfolder (Staging/, Strategy/, Content/, Source of Truth/, Briefs/, Research/, Planning/, PRDs/, TDD/, or AI/)
- Any assistant config in `[Project] - AI/` at project root — should be in `Working Files/AI/`

**Output format:**
```
NAMING VIOLATION: [File] → Should be [CorrectName]
OLD FORMAT: [File] → Flag for rename
WRONG STAGE: [File] — has been staging for [N] days → Promote or archive?
```

---

### STEP 2 — STALE FILE AUDIT

**Stale staging (7+ days untouched, active project):**
→ Flag: action needed — promote, continue, or archive

**Stale staging (30+ days untouched, any project):**
→ Flag: archive candidate — move to `[ProjectFolder]/Archive/[file]` or delete if confirmed empty/redundant

**Prelive files (any age):**
→ Prelive = operator's working document. Do NOT flag as stale based on age alone.
→ Flag ONLY if: (a) content is already in live and it is a true duplicate, or (b) operator explicitly requests archive or return.
→ Do NOT flag "why hasn't this been promoted" — prelive may be actively in use.

**Stale planning docs (no updates in 60+ days, active project):**
→ Flag: check if still relevant. Update or note as stable.

**Plans marked COMPLETE with unchecked `[ ]` criteria:**
→ COMPLETE does not mean criteria were ticked. After any plan is marked complete, verify all `[ ]` boxes reflect actual vault state.
→ Audit: search `plans/` and `plans/Archive/` for any file containing `STATUS: COMPLETE` and `- [ ]` on the same file.
→ For each hit: inspect the vault, tick what is confirmed done, flag what is genuinely incomplete.
→ Rule: a plan is not fully closed until every criterion is `[x]` or explicitly noted as N/A with reason.

**Output format:**
```
STALE — [file] — [N] days since last update — [action: promote / archive / review]
```

---

### STEP 3 — BROKEN REFERENCES AUDIT

Check all active files for broken Obsidian links `[[...]]`:
- Links to files that no longer exist (renamed or deleted)
- Links using old naming conventions
- Links to files in wrong locations

**Most common after a rename session:**
- Prelive files linking to old staging names
- Daily notes linking to old file paths
- Workflows referencing old config locations

**Output format:**
```
BROKEN LINK: [source file] line [N] → [[old-reference]] → File does not exist
SUGGESTED FIX: Update to [[correct-reference]]
```

---

### STEP 4 — CROSS-FOLDER CONTAMINATION CHECK

Each project's files must stay in its folder. No IP in skill files. No brand content in system files.

**Check:**
- Any Athena IP content outside `HatchFox Studios - Business/Athena 2327 - Brand/`
- Any Duio content outside `Duio - Business/`
- Any TBST Digital - Business / HatchFox Studios - Business content outside their folders
- Any project content inside `_AI/` (system only)
- Any project content inside `.claude/commands/` (behavior only, no IP)
- Any file at `.claude/skills/` path — wrong location, should be `.claude/commands/[name].md`

**Output format:**
```
CONTAMINATION: [file] found in [wrong folder] → Should be in [correct folder]
```

---

### STEP 5 — ORPHANED FILES

Files with no incoming or outgoing links are invisible to the system.

**Check for:**
- Any `.md` file in a project folder not referenced from any other file
- Templates that have never been used (low priority — note only)
- Old staging/prelive files from completed projects

**Output format:**
```
ORPHAN: [file] — no links in or out → Archive, link, or delete?
```

---

### STEP 6 — ARCHIVE PROTOCOL

When a file is flagged for archiving:

1. Create `[ProjectFolder]/Archive/` if it doesn't exist
2. Move the file into Archive/
3. Rename it: append ` - Archive` to the existing filename (before `.md`)
   → `Game Brief V3.md` becomes `Game Brief V3 - Archive.md`
   → `Duio - ContentStrategy-Live.md` becomes `Duio - ContentStrategy-Live - Archive.md`
4. Update any broken links left by the move
5. Note the archive in the improvements log: `[SYSTEM] Archived [file] — reason`

**Archive candidates:**
- Superseded staging files after live promotion
- Old config versions after a major restructure
- Planning docs from completed or parked projects
- Prelive files that were rejected and not re-staged

**Do NOT archive:**
- Live files (canonical sources of truth)
- Planning files for active projects
- Any file the operator has flagged as keep

---

### STEP 7 — ASSISTANT CONFIG COVERAGE CHECK

For every active project in the registry:

| Project | /writer config | /content config | /game config | /producer config |
|---|---|---|---|---|
| Athena 2327 | ✅ | ? | ? | ? |
| Duio | — | ? | — | — |
| HatchFox Content - Brand - Brand | — | ? | — | — |
| TBST Digital | — | ? | — | ? |
| AI Game Maker | — | — | ? | — |

For each `?`: flag as missing. Propose: "Run the relevant skill — it includes a built-in Config Creation Protocol."
For each `—`: not applicable (project type doesn't use this skill).

---

### STEP 8 — SOURCE OF TRUTH CHECK

Every active project needs a source of truth file (World Bible, Content Strategy, GDD, etc.)

| Project | Source of Truth | Status |
|---|---|---|
| Athena 2327 | `Sycthe of Athena - World Bible.md` | Check: live? |
| Duio | `Duio - ContentStrategy-Live.md` | Check: live? |
| HatchFox Content - Brand | `HatchFox Content - ContentStrategy-Live.md` | Check: live? |
| TBST Digital | `TBST Digital - ContentStrategy-Live.md` | Check: live? |
| AI Game Maker | `AI Game Maker - V4-Staging.md` | Check: staging or live? |

Flag any project where the source of truth is missing or still in staging when it should be live.

---

### STEP 9 — PORTABLE CONFIG SYNC CHECK

Every non-IP system file in local must match the portable config exactly. IP files must be local-only.

**Files that must be IDENTICAL (local = portable):**

| File | Local path | Portable path |
|---|---|---|
| daily-run.md | `_AI/SYSTEM/daily-run.md` | `Producer AI - Portable Config/Vault/_AI/SYSTEM/daily-run.md` |
| HOW-TO-USE.md | `_AI/HOW-TO-USE.md` | `Producer AI - Portable Config/Vault/_AI/HOW-TO-USE.md` |
| vault-maintenance-checker.md | `_AI/SYSTEM/vault-maintenance-checker.md` | `Producer AI - Portable Config/Vault/_AI/SYSTEM/vault-maintenance-checker.md` |
| All non-IP skills | `.claude/commands/[name].md` (excl. lifestyle-plan, bookkeeping) | `Portable Config/Vault/.claude/commands/[name].md` |
| All 7 rules | `.claude/rules/*.md` | `Portable Config/Vault/.claude/rules/*.md` |
| settings.json | `.claude/settings.json` | `Portable Config/Vault/.claude/settings.json` |
| All _AI/SYSTEM/ files | `_AI/SYSTEM/*.md` | `Portable Config/Vault/_AI/SYSTEM/*.md` |
| All non-IP _AI/WORKFLOWS/ | `_AI/WORKFLOWS/*.md` (excl. writing-athena) | `Portable Config/Vault/_AI/WORKFLOWS/*.md` |
| All non-IP _AI/TEMPLATES/ | `_AI/TEMPLATES/*.md` (excl. daily-note-ip) | `Portable Config/Vault/_AI/TEMPLATES/*.md` |

**Files that must be LOCAL ONLY (IP — must NOT exist in portable):**
- `.claude/commands/lifestyle-plan.md`
- `.claude/commands/bookkeeping.md`
- `.claude/commands/tax-return.md`
- `_AI/WORKFLOWS/writing-athena.md`
- `_AI/TEMPLATES/daily-note-athena.md`, `daily-note-duio.md`, `daily-note-game-maker.md`, `daily-note-tbst.md`

**Check procedure:**
1. For each file in the IDENTICAL list: confirm content matches. If different — flag the specific file and which version is more current.
2. For each IP file: confirm it does NOT appear in the portable config folder.
3. Flag any file present in portable that should be local-only.

**Output format:**
```
SYNC DRIFT: [file] — local is more current / portable is more current / unknown divergence
LOCAL ONLY VIOLATION: [file] found in portable — remove
MISSING FROM PORTABLE: [file] — sync needed
```

---

### STEP 10 — GITHUB REPOS AUDIT

Cross-checks the GitHub Repos Registry in `_AI/daily-ai-config.md` §08 against actual local and remote state.

**For each repo in the registry:**

1. **Local clone check** — is this repo cloned on this machine?
   - Check for a local directory with `.git` pointing to the correct remote
   - Run `git remote -v` inside each cloned repo to confirm it matches the registry URL
   - If not cloned: flag as `NOT CLONED` — operator decides if this machine needs it

2. **Sync status** — if cloned locally, is it current?
   ```
   git fetch origin
   git status
   ```
   - Flag if local is behind remote (unpulled changes)
   - Flag if local has uncommitted changes or is ahead of remote (unpushed)

3. **Registry completeness** — any repos on GitHub not in the registry?
   - Run: `gh repo list SashaTBST --limit 50` and compare to `_AI/daily-ai-config.md` §08
   - Flag any repo on GitHub not listed in the registry
   - Rule: every repo must be registered before use

**Output format:**
```
REPO [name] — CONNECTED — UP TO DATE / [N] commits behind / [N] uncommitted changes
REPO [name] — NOT CLONED — [skip (not needed on this machine) / clone needed]
UNREGISTERED: [repo-name] — on GitHub, not in registry → Add to daily-ai-config §08
```

**Note:** Not every repo needs to be cloned on every machine. `SashaTBSTObsidian` (vault backup) must always be connected. Flag others — operator decides.

---

## OUTPUT FORMAT — FULL REPORT

```
VAULT MAINTENANCE CHECK — [YYYY-MM-DD]

NAMING VIOLATIONS:    [N] found
STALE FILES:          [N] found (staging [N] / prelive [N])
BROKEN REFERENCES:    [N] found
CROSS-CONTAMINATION:  [N] found / CLEAR
ORPHANED FILES:       [N] found
ARCHIVE CANDIDATES:   [N] found
MISSING CONFIGS:      [N] projects without full config coverage
SOURCE OF TRUTH GAPS: [N] projects missing source of truth
PORTABLE SYNC DRIFT:  [N] files out of sync / CLEAR
GITHUB REPOS:         [N] unregistered / [N] not cloned / [N] out of sync / CLEAR

PRIORITY ACTIONS:
1. [highest priority fix]
2. [second]
3. [third]

CLEAR ITEMS: [what passed with no issues]
```

---

## MAINTENANCE RULES

→ Never delete a file without confirming with the operator. Archive first.
→ Never rename a live file without updating all references first.
→ Never archive a file the operator is actively using.
→ When in doubt about whether something is stale: flag it, don't delete it.
→ After any maintenance run: add a one-line entry to `_AI/MEMORY/improvements-log.md` tagged `[SYSTEM]`

---

## QUICK COMMANDS

- `/vault-check` — run the full maintenance protocol
- `/vault-check naming` — run Step 1 only (naming violations)
- `/vault-check stale` — run Step 2 only (stale files)
- `/vault-check refs` — run Step 3 only (broken references)
- `/vault-check repos` — run Step 10 only (GitHub repos audit)
- `/archive [file]` — archive a specific file to `[ProjectFolder]/Archive/`
- `/audit` — lighter version, runs at session start (see daily skill)

---

*Vault Maintenance Checker v1.0 | [[../_AI/daily-ai-config]] | Run weekly or after bulk changes*
