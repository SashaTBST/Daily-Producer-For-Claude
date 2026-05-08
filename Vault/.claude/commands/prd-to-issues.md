Break a PRD into GitHub issues using HITL/AFK vertical slices with blocking dependencies. Use for GitHub-enabled projects only — use /prd-to-plan for markdown-only projects.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

If a PRD file, issue number, or content is provided: $ARGUMENTS

## Purpose

Converts a completed PRD into a structured set of GitHub issues — one per vertical slice. Issues are tagged HITL (human-in-the-loop, requires approval) or AFK (autonomous, agent can complete alone). Blocking dependencies are declared explicitly so the agent knows what order to work in.

---

## Process

**Step 1 — Load the PRD**
Ask for the PRD file path, paste, or GitHub issue number. Confirm it is read before proceeding.

**Step 2 — Confirm GitHub context**
Ask: "What is the GitHub repo? (owner/repo)" — needed to create issues. If no repo provided: offer to draft issues as markdown for manual creation.

**Step 3 — Extract vertical slices**
Same rules as /prd-to-plan: smallest unit delivering real end-to-end value. Each slice = one issue.

**Step 4 — Classify each slice**
- **HITL** — requires human decision, review, or approval before proceeding. Use when: ambiguous scope, production-facing change, irreversible action, needs operator judgement.
- **AFK** — agent can complete autonomously. Use when: clear spec, reversible, no stakeholder decision needed.

**Step 5 — Declare blocking dependencies**
For each issue: list which issues must be complete before this one can start. Format: `Blocks: #[n], #[n]`

**Step 6 — Write issues**
Issue format:
```
Title: [Slice name]
Labels: HITL or AFK, vertical-slice
Body:
## Goal
[What this slice delivers]

## Done when
[Specific, testable condition]

## Tasks
- [ ] [task]
- [ ] [task]

## Blocks
[Issue numbers this depends on, or "none"]
```

Create via gh CLI or output as markdown for manual creation.

---

## Rules

- One issue per vertical slice — no bundling
- Every issue has a done condition — no open-ended issues
- HITL issues pause the agent loop until approved
- AFK issues can be picked up by ralph-loop autonomously
- Blocking deps declared on every issue — none left implicit

---

Every response ends with NEXT MOVE.
