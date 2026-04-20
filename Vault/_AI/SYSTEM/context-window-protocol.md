# CONTEXT WINDOW PROTOCOL
**Version:** 1.0
**Part of:** [[../_AI/daily-ai-config]]
**Status:** Active

The context window in any AI chat session is finite. When it is closing, work must not be lost. This protocol defines what to save, how to save it, and how to restart cleanly.

---

## WHEN TO TRIGGER

This protocol triggers automatically when any of the following occur:

- The AI signals context is approaching capacity (internal signal — act on it immediately)
- The session has been running for an extended period with many file reads and large outputs
- A complex multi-project session shows signs of degraded recall (repeating questions, losing thread)
- The operator types `/save-session` explicitly
- The operator says "save and close", "context is full", "start a new chat"

Do not wait until context is actually full. Trigger this protocol when there is still enough context left to execute it properly.

---

## SAVE PROCEDURE — EXECUTE IN ORDER

### Step 1: Alert
Output immediately:

```
CONTEXT WINDOW CLOSING — saving session state now.
```

Do not continue other work. Prioritise the save.

### Step 2: Write session state file

Save to: `_AI/MEMORY/session-state-[YYYY-MM-DD].md`

If a file for today already exists, append with a timestamp marker `--- [HH:MM] ---` rather than overwriting.

**Session state file format:**

```markdown
# Session State — [YYYY-MM-DD] [HH:MM]

## What Was Completed This Session
- [Bulleted list of files created, decisions made, tasks finished]

## Open Items — Not Yet Completed
- [Bulleted list of work in progress or blocked tasks]

## Next Moves Per Project
- [Project name]: [Specific next action]
- [Project name]: [Specific next action]

## Staged Improvements Not Yet Logged
- [Any improvements identified during session that haven't been written to improvements-log.md]

## Files Written This Session
- [List of all files created or edited, with full paths]

## Decisions Made
- [Any project decisions, world-building decisions, config decisions made in this session]

## Context Notes for Next Session
- [Anything the next session needs to know that isn't in a file — operator preferences stated, corrections made, open questions raised]
```

### Step 3: Update improvements log

If any improvement flags were identified during the session, write them to `_AI/MEMORY/improvements-log.md` before closing. Do not leave them only in session state — the improvements log is the persistent audit trail.

### Step 4: Report the save

Output:
```
SESSION STATE SAVED → _AI/MEMORY/session-state-[YYYY-MM-DD].md

Completed: [N items]
Open: [N items]
Next moves written for: [project list]

TO RESUME: Start a new chat and run /daily
The session state file will be read at startup and the session continues from here.
```

---

## RESTART PROTOCOL

At the start of the new session after a context save:

1. The `/daily` skill session start protocol runs (new files check, improvements log, AI producer log, unprocessed notes)
2. Read `_AI/MEMORY/session-state-[YYYY-MM-DD].md` — the most recent file
3. Report: what was completed in the prior session, what is open, propose the first next move
4. Do not ask the operator to re-explain what was happening — it is in the state file

---

## CONTEXT EFFICIENCY RULES — ONGOING

These rules reduce unnecessary context consumption during sessions:

→ **Don't load full files when sections will do.** Read offset/limit when only part of a file is needed.
→ **Don't repeat large blocks of text in responses.** Reference the file path instead.
→ **Don't re-read files already in context.** Reference what was read earlier.
→ **Summarise tool outputs.** Long Bash/Read outputs should be summarised in the response, not repeated.
→ **One project at a time when possible.** Multi-project context swapping is the fastest way to fill a window.
→ **Close out completed work.** Once a file is written and confirmed, it does not need to stay in active discussion.

---

## COMMAND

`/save-session` — triggers this protocol immediately regardless of context state.

---

*Context Window Protocol v1.0 | Part of [[../_AI/daily-ai-config]]*
