---
name: save-session
description: Saves current session state to memory before closing or when context is filling. Use when context is getting large, before ending a long session, or when the user says "save session" or "context is getting full".
---

## Anti-patterns

- Waiting until context is full to trigger — save early. A partial save is better than no save.
- Vague "open items" — be specific: file path, what's blocked, what the next action is.
- Forgetting to flush improvement flags — if Level 2 flags were detected this session, write them to the log before closing.
- Writing session state without writing restart instructions — the next session needs to know where to start.

## Save Procedure

**Step 1 — Alert**
Output: "SAVING SESSION STATE — [current date]"

**Step 2 — Write session state**
Write to `_AI/MEMORY/session-state-[YYYY-MM-DD].md`:

```markdown
# Session State — [YYYY-MM-DD]

## What Was Completed This Session
[Finished tasks with file paths]

## Open Items
[Unfinished work — what's in progress, what's blocked]

## Next Moves Per Project
[One next action per active project]

## Files Written or Modified
[All files touched this session]

## Decisions Made
[Architectural or design decisions — brief]

## Staged Improvements Not Yet Logged
[Level 2 flags detected but not yet written to improvements-log]

## Context Notes for Next Session
[What the next session needs to know to pick up cleanly]
```

**Step 3 — Flush improvement flags**
Write any unlogged Level 2 flags to `_AI/MEMORY/improvements-log.md`.

**Step 4 — Report**
"Session state saved to `_AI/MEMORY/session-state-[YYYY-MM-DD].md`. Improvement flags flushed. Ready to close or continue."

Restart: run `/daily` — it will detect and load the most recent session-state file.

## QA
Before closing this skill session:
- [ ] Session state file written to correct path with today's date
- [ ] Open items are specific — not vague
- [ ] Improvement flags flushed to improvements-log
- [ ] Restart instructions included
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.
