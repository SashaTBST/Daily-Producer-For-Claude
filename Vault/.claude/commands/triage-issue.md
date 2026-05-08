Classify and scope an incoming issue before implementation — assigns phase, HITL or AFK, effort, and dependencies. Use at the start of any new issue or bug report before touching code or files.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

If an issue, bug report, or task description is provided: $ARGUMENTS

## Purpose

Triage before implementation. An unscoped issue is a trap — it can expand indefinitely, block other work, or get built wrong. This skill classifies the issue, scopes it to a vertical slice, and decides whether a human needs to approve before work starts.

---

## Process

**Step 1 — Read the issue**
State what the issue is in one sentence. If it is unclear: ask for clarification before proceeding.

**Step 2 — Classify**

| Dimension | Options |
|-----------|---------|
| Type | Bug / Feature / Improvement / Chore / Research |
| Phase | Which of the 7 phases does this belong to? (Idea / Research / Prototype / PRD / Plan / Execute / QA) |
| Mode | HITL — needs human approval before/during. AFK — agent can complete autonomously. |
| Effort | Small (< 1 session) / Medium (1–3 sessions) / Large (needs slicing first) |
| Priority | Blocking / High / Normal / Low |

**Step 3 — Scope the slice**
If effort is Large: this issue must be sliced before implementation. Run /prd-to-plan or /prd-to-issues first.
If effort is Small or Medium: define the done condition now. One sentence. Specific and testable.

**Step 4 — Declare dependencies**
What must be complete before this issue can start? List them. If none: state "none".

**Step 5 — Output the triage card**

```
Issue: [title]
Type: [Bug / Feature / Improvement / Chore / Research]
Phase: [phase]
Mode: [HITL / AFK]
Effort: [Small / Medium / Large]
Priority: [Blocking / High / Normal / Low]
Done when: [specific, testable condition]
Depends on: [issue list or none]
Next action: [what happens now — implement, slice, or wait]
```

**Step 6 — Propose next action**
If HITL: present the triage card and wait for approval before starting.
If AFK: propose starting implementation immediately.
If Large: propose running /prd-to-plan to slice it first.

---

## Rules

- Triage before touching files — no implementation without a done condition
- HITL on anything production-facing, irreversible, or ambiguous
- Large issues must be sliced — do not attempt to implement in one pass
- Dependencies declared upfront — not discovered mid-implementation

---

Every response ends with NEXT MOVE.
