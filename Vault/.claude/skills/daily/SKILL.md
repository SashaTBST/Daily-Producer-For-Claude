---
name: daily
description: Session start operating system — loads the daily AI config, runs the startup sequence, and surfaces open actions per project. Use at the start of every session, or when recovering from a context compaction or power loss.
argument-hint: "[project or area to focus on after startup]"
---

Read `_AI/daily-ai-config.md` — treat it as fully loaded. All rules, protocols, project registry, pipeline rules, and commands defined there apply immediately.

Then execute the startup sequence from `_AI/SYSTEM/daily-run.md` — Steps 1 through 4 in order. Begin immediately without waiting for instruction.

If `$ARGUMENTS` is provided, focus the session on that project or area after completing the startup sequence.

## Anti-patterns

- Skipping the startup sequence and jumping straight to project work — run all 4 steps first.
- Asking the operator what to start with — Step 4 decides and proposes. Do not wait.
- Running /daily when already mid-session — /daily is for session start only. Use /status for mid-session project checks.
- Ignoring session-state files — if one exists from today or yesterday, load it and surface open items.

## QA
Session start complete when:
- [ ] `_AI/daily-ai-config.md` loaded and applied
- [ ] Steps 1–4 from `_AI/SYSTEM/daily-run.md` executed in order
- [ ] Any today/yesterday session-state file loaded and open items surfaced
- [ ] Highest-priority next action proposed — not "what do you want to work on?"
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.
