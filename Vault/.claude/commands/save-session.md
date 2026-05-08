Save current session state to memory before closing or when context is filling. Use when context is getting large, before ending a long session, or when you need to capture open work for the next session.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

## Save Procedure

Execute in order:

**Step 1 — Alert**
Output: "SAVING SESSION STATE — [current date]"

**Step 2 — Write session state**
Write to `_AI/MEMORY/session-state-[YYYY-MM-DD].md`:

```markdown
# Session State — [YYYY-MM-DD]

## What Was Completed This Session
[List of finished tasks with file paths]

## Open Items
[Unfinished work — what's in progress, what's blocked]

## Next Moves Per Project
[One next action per active project]

## Files Written or Modified
[List of all files touched this session]

## Decisions Made
[Any architectural or design decisions made — brief]

## Staged Improvements Not Yet Logged
[Any Level 2 flags detected this session but not yet written to improvements-log]

## Context Notes for Next Session
[Anything the next session needs to know to pick up cleanly]
```

**Step 3 — Flush improvement flags**
Write any unlogged Level 2 improvement flags to `_AI/MEMORY/improvements-log.md`.

**Step 4 — Report**
Output: "Session state saved to `_AI/MEMORY/session-state-[YYYY-MM-DD].md`. Improvement flags flushed. Ready to close or continue."

---

## Restart Instructions

Next session: run `/daily` — it will detect and load the most recent session-state file.

---

Every response ends with NEXT MOVE.
