---
name: improve
description: Reviews staged improvements in the improvements log and applies approved ones. Use when the Stop hook surfaces pending improvements, or any time the operator wants to clear the improvement backlog.
---

Read `_AI/MEMORY/improvements-log.md`. Find all entries with `STATUS: Staged`. Present and resolve each one.

## Anti-patterns

- Auto-applying without asking — present each item, wait for operator response per item.
- Batch-approving multiple items — one at a time. Each item is an independent decision.
- Writing config-level changes directly to live — Stage 3 changes go through staging pipeline.
- Marking an item Applied before the file change is verified — check the file, then update the log.

## Process

**Step 1 — Load the log**
Read `_AI/MEMORY/improvements-log.md`. Find all `STATUS: Staged` entries.

**Step 2 — Surface each item (one at a time)**
```
IMPROVEMENT PENDING
Source: [SYSTEM / SKILL: name / IP: name / TEMPLATE]
Observation: [what was flagged]
Proposed change: [what the change is]
Files affected: [list]

Approve / Reject / Modify?
```
Wait for operator response before proceeding.

**Step 3 — Apply**
- APPROVED → apply the change. Update log: `STATUS: Applied` + date.
- REJECTED → update log: `STATUS: Rejected` + reason.
- MODIFIED → apply modified version. Update log: `STATUS: Applied (modified)` + what changed.

Note: Stage 3 (config-level) changes go through staging pipeline — propose the change, do not write to live config directly.

**Step 4 — Report**
"X applied, Y rejected, Z modified. Improvements log updated."

## QA
Before closing this skill session:
- [ ] Every STATUS: Staged item surfaced and resolved
- [ ] No item auto-applied without explicit operator approval
- [ ] Config-level changes staged, not written to live
- [ ] Improvements log updated with final status on each item
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.
