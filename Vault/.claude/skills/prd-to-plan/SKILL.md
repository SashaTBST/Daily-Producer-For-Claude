---
name: prd-to-plan
description: Breaks a completed PRD into a phased vertical-slice execution plan saved to ./plans/[name].md. Use after /prd is complete — this is the default planning path for markdown-only projects in this vault.
argument-hint: "[PRD file path or paste]"
---

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

If a PRD file or content is provided: $ARGUMENTS

## Purpose

A vertical slice is the smallest unit of work that delivers real value end-to-end — not a layer. Each slice can be built, tested, and reviewed independently. This skill takes a completed PRD and breaks it into ordered slices. Output is a living plan file the operator can track and tick off.

## Workflow

**Step 1 — Load the PRD**
Ask for the PRD file path or paste. Confirm it is read before proceeding.

**Step 2 — Confirm project type**
Ask: "Is this project GitHub-enabled or markdown-only?" Default: markdown-only. If GitHub: redirect to /prd-to-issues.

**Step 3 — Extract vertical slices**
From the PRD, identify the smallest deliverable units. Each slice must:
- Deliver real value when complete (not just infrastructure)
- Be completable independently
- Have a clear done state

Order: highest risk first, or most foundational first — whichever unblocks more downstream work.

**Step 4 — Write the plan**
Save to: `./plans/[project-name].md`

```
# [Project Name] — Execution Plan
PRD: [link or path]
Last updated: [date]

## Slices

### Slice 1 — [Name]
Goal: [what this delivers]
Done when: [specific, testable condition]
- [ ] [task]
- [ ] [task]
```

**Step 5 — Review**
State: total slices, dependencies between them, which slice to start first.

## Anti-patterns

- Horizontal slices — "phase 1: all backend, phase 2: all frontend" is wrong
- Slices without a done condition — every slice needs a testable endpoint
- Starting before the PRD is confirmed — slice quality is only as good as the spec
- Slices too large to complete in one focused session — flag and split them
- Redirecting GitHub projects here — use /prd-to-issues for those

## QA

Done when: `./plans/[name].md` written, slice count stated, first slice identified, dependencies flagged. Every response ends with NEXT MOVE.
