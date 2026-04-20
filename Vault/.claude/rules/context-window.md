# Context Window Protocol

Full protocol: `_AI/SYSTEM/context-window-protocol.md`

## Trigger

When context is approaching capacity — do not wait until full.

## Save Procedure

1. Alert: "CONTEXT WINDOW CLOSING — saving session state now."
2. Write to: `_AI/MEMORY/session-state-[YYYY-MM-DD].md`
   Contents: completed work, open items, next moves per project, staged improvements not yet logged, files written, decisions made, context notes for next session.
3. Flush any improvement flags to `_AI/MEMORY/improvements-log.md`.
4. Report save. Output: "TO RESUME: Start a new chat and run /daily"

Command: `/save-session` — triggers immediately at any point.

## Restart

New chat → run `/daily` → session state is read at startup → session resumes.

## Context Efficiency (ongoing)

- Don't load full files when sections suffice. Use offset/limit reads.
- Don't repeat large blocks in responses — reference the file path.
- Don't re-read files already in context.
- One project focus at a time where possible.