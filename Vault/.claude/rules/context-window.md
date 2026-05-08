# Context Window Protocol

Full protocol: `_AI/SYSTEM/context-window-protocol.md`

## Trigger

When context is approaching capacity — do not wait until full. Trigger early.

## Save Procedure

1. Alert operator: "CONTEXT WINDOW CLOSING — saving session state now."
2. Write session state to: `_AI/MEMORY/session-state-[YYYY-MM-DD].md`
   Include: completed work this session, open items, next moves per project, staged improvements not yet logged, files written or modified, decisions made, context notes for the next session.
3. Flush any improvement flags to `_AI/MEMORY/improvements-log.md` before closing.
4. Report that the save is complete and output restart instructions.

## Rule

A partial save is better than no save. Trigger early — never wait until the context window is full.
