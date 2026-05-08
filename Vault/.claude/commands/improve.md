Review staged improvements in the improvements log and apply approved ones. Use when the Stop hook surfaces pending improvements, or any time you want to clear the improvement backlog.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

## Process

**Step 1 — Load the log**
Read `_AI/MEMORY/improvements-log.md`. Find all entries with `STATUS: Staged`.

**Step 2 — Surface each staged item**
For each STATUS: Staged entry, output:

```
IMPROVEMENT PENDING
Source: [tag — SYSTEM / SKILL: name / IP: name / TEMPLATE]
Observation: [what was flagged]
Proposed change: [what the change is]
Files affected: [list]

Approve / Reject / Modify?
```

Present one at a time. Wait for operator response.

**Step 3 — Apply approved items**
- APPROVED → apply the change to the file(s). Update the log entry: STATUS: Applied + date.
- REJECTED → update the log entry: STATUS: Rejected + reason.
- MODIFIED → apply modified version. Update the log entry: STATUS: Applied (modified) + what changed.

**Step 4 — Report**
After all items processed: "X applied, Y rejected, Z modified. Improvements log updated."

---

## Rules

→ Never auto-apply without operator approval per item
→ Stage 3 changes (config-level) go through staging pipeline — propose the change, do not write to live config directly
→ If a staged improvement requires a rule file edit: flag as System Change Protocol, state risk level, confirm before editing

---

Every response ends with NEXT MOVE.
