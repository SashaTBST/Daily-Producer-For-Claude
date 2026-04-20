# Ralph Loop Workflow
**Version:** 1.0
**Part of:** [[../_AI/daily-ai-config]]
**Status:** Active

The Ralph loop is a while loop for autonomous execution. AI reads a plan, picks one task, completes it, records progress, and loops until done or blocked.

**Scope: dev/code projects with git.** For non-dev projects (writing, content, business) — use the Staging → Prelive → Live pipeline with `/daily` instead.

---

## When to Use

Use when:
- A plan file exists (`plans/[feature].md`) and has been approved
- Tasks are unambiguous — acceptance criteria are clear
- Human review is not required between steps (AFK classification)
- You want autonomous multi-step execution without prompting each step

Do NOT use when:
- Any task is classified HITL
- Acceptance criteria are ambiguous
- Architectural decisions are unresolved
- No plan file exists — run `/prd-to-plan` first

---

## Loop Structure

```
WHILE tasks remain in plan:
  1. READ   → plan file + progress.txt
  2. PICK   → next incomplete task with no unresolved blockers
  3. DO     → complete the task (one unit of work)
  4. VERIFY → check acceptance criteria are met
  5. COMMIT → one commit per completed task (conventional commit)
  6. LOG    → update progress.txt with result
  7. LOOP   → return to step 1

STOP when:
  - All tasks complete
  - A HITL task is reached
  - A blocker is encountered that cannot be resolved autonomously
  - An error occurs that requires operator input
```

---

## Required Files

**Plan file** — `plans/[feature].md`
Must exist and be approved before loop starts.
Must have clear acceptance criteria per phase.

**Progress file** — `plans/[feature]-progress.txt`
Created at loop start if it doesn't exist.
Append-only. Never overwrite. Format:

```
[YYYY-MM-DD HH:MM] STARTED loop — [feature name]
[YYYY-MM-DD HH:MM] COMPLETE — Phase 1: [title] — [brief result]
[YYYY-MM-DD HH:MM] COMPLETE — Phase 2: [title] — [brief result]
[YYYY-MM-DD HH:MM] BLOCKED — Phase 3: [title] — [reason]
[YYYY-MM-DD HH:MM] STOPPED — [reason: complete / blocked / HITL reached]
```

---

## Commit Rules

Every completed task = one commit. Never batch multiple tasks.

```
type(scope): what was done and why

Types: feat | fix | chore | docs | refactor
```

Examples:
```
feat(duio-ai): create typed AI folder structure for Duio
chore(configs): migrate and rename Duio assistant configs
docs(file-routing): add 7-phase development process section
```

---

## Stop Conditions

The loop stops and waits for operator input when:

| Condition | Action |
|---|---|
| HITL task reached | Report: "HITL task reached: [title]. Decision required: [question]." |
| Acceptance criteria unclear | Report: "Cannot verify: [criterion]. Clarification needed." |
| Blocker unresolvable | Report: "Blocked: [what]. Options: [A] [B]." |
| File conflict or unexpected state | Report: "Unexpected state: [description]. Pausing." |
| All tasks complete | Report: summary of completed work, final progress.txt state |

---

## Loop Start Protocol

Before beginning the loop:

1. Confirm plan file exists and is approved (not Staging)
2. Confirm all Phase 1 blockers are resolved
3. Create progress.txt if it doesn't exist
4. Log: `STARTED loop — [feature name]`
5. Report: "Starting Ralph loop for [feature]. [N] phases. First: [Phase 1 title]."

---

## Vault-Specific Notes

**For file operations** — use Write, Edit, Bash tools. Not code execution.
**For config migrations** — confirm pipeline status before moving any Prelive file.
**For system file changes** — System Change Protocol applies even inside the loop.
**No IP in `_AI/`** — the guardrail applies inside the loop. Route all project files correctly.
**One task = one phase** — in this vault, plan phases map 1:1 to loop iterations.
