Break a PRD into a phased vertical-slice execution plan saved to ./plans/[name].md. Use after /prd is complete — this is the default planning path for markdown-only projects in this vault.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

If a PRD file or content is provided: $ARGUMENTS

## Purpose

A vertical slice is the smallest unit of work that delivers real value end-to-end — not a layer (not "all the data work", not "all the UI work"). Each slice can be built, tested, and reviewed independently.

This skill takes a completed PRD and breaks it into ordered vertical slices. Output is a living plan file the operator can track and tick off.

---

## Process

**Step 1 — Load the PRD**
Ask for the PRD file path or paste. Confirm you have read it before proceeding.

**Step 2 — Confirm project type**
Ask: "Is this project GitHub-enabled or markdown-only?" Default: markdown-only. If GitHub: redirect to /prd-to-issues.

**Step 3 — Extract vertical slices**
From the PRD, identify the smallest deliverable units. Each slice must:
- Deliver real value when complete (not just infrastructure)
- Be completable independently
- Have a clear done state

Order slices: highest risk first, or most foundational first — whichever unblocks more downstream work.

**Step 4 — Write the plan**
Save to: `./plans/[project-name].md`

Plan format:
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

### Slice 2 — [Name]
...
```

**Step 5 — Review**
After writing: state the total number of slices, flag any dependencies between them, identify the first slice to start.

---

## Rules

- Slices are vertical, not horizontal — no "phase 1: all backend" structures
- Each slice has a clear done condition — no vague "in progress" states
- The plan is a living document — update it as slices complete or scope changes
- Flag if any slice is too large to complete in a single focused session

---

Every response ends with NEXT MOVE.
