---
name: triage-issue
description: Classifies and scopes an incoming issue before implementation — assigns type, phase, HITL/AFK mode, effort, done condition, and dependencies. Use at the start of any new issue, bug report, or task before touching code or files.
argument-hint: "[issue title or description]"
---

Triage before implementation. An unscoped issue is a trap — it can expand indefinitely, block other work, or get built wrong.

If an issue, bug report, or task description is provided: $ARGUMENTS

## Anti-patterns

- Starting implementation without a done condition — if you don't know what done looks like, you don't know when to stop.
- Treating Large issues as Medium to avoid slicing — Large must be sliced. Do not attempt in one pass.
- Marking anything production-facing, irreversible, or ambiguous as AFK — HITL exists for a reason.
- Discovering dependencies mid-implementation — declare them upfront or the issue isn't triaged.
- Skipping triage "just this once" because the issue seems simple — simple issues become complex mid-build.

## Process

**Step 1 — Read the issue**
State what the issue is in one sentence. If unclear: ask for clarification before proceeding.

**Step 2 — Classify**

| Dimension | Options |
|-----------|---------|
| Type | Bug / Feature / Improvement / Chore / Research |
| Phase | Idea / Research / Prototype / PRD / Plan / Execute / QA |
| Mode | HITL — needs human approval. AFK — agent can complete autonomously. |
| Effort | Small (< 1 session) / Medium (1–3 sessions) / Large (needs slicing first) |
| Priority | Blocking / High / Normal / Low |

**Step 3 — Scope the slice**
Large effort: must be sliced first — run /prd-to-plan or /prd-to-issues before implementing.
Small/Medium effort: define the done condition now. One sentence. Specific and testable.

**Step 4 — Declare dependencies**
What must be complete before this issue starts? List them. If none: state "none".

**Step 5 — Output triage card**
```
Issue: [title]
Type: [type]
Phase: [phase]
Mode: [HITL / AFK]
Effort: [Small / Medium / Large]
Priority: [Blocking / High / Normal / Low]
Done when: [specific, testable condition]
Depends on: [list or none]
Next action: [implement / slice / wait for approval]
```

**Step 6 — Propose next action**
HITL: present triage card and wait for approval.
AFK: propose starting implementation immediately.
Large: propose running /prd-to-plan to slice it first.

## QA
Before closing this skill session:
- [ ] Every issue has a done condition — specific and testable
- [ ] No Large issue was sent to implementation without slicing
- [ ] HITL set on anything production-facing, irreversible, or ambiguous
- [ ] Dependencies declared before implementation started
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.
