---
name: prd-to-issues
description: Breaks a completed PRD into GitHub issues using HITL/AFK vertical slices with explicit blocking dependencies. Use for GitHub-enabled projects only — use /prd-to-plan for markdown-only projects.
argument-hint: "[PRD file path, GitHub issue number, or paste]"
---

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

If a PRD file, issue number, or content is provided: $ARGUMENTS

## Purpose

Converts a completed PRD into a structured set of GitHub issues — one per vertical slice. Issues are tagged HITL (human-in-the-loop) or AFK (autonomous). Blocking dependencies are declared explicitly so the agent knows the work order.

## Workflow

**Step 1 — Load the PRD**
Ask for the PRD file path, paste, or GitHub issue number. Confirm it is read before proceeding.

**Step 2 — Confirm GitHub context**
Ask: "What is the GitHub repo? (owner/repo)" — needed to create issues. If no repo: offer to draft issues as markdown for manual creation.

**Step 3 — Extract vertical slices**
Same rules as /prd-to-plan: smallest unit delivering real end-to-end value. Each slice = one issue.

**Step 4 — Classify each slice**
- **HITL** — requires human decision, review, or approval. Use when: ambiguous scope, production-facing change, irreversible action, needs operator judgement.
- **AFK** — agent completes autonomously. Use when: clear spec, reversible, no stakeholder decision needed.

**Step 5 — Declare blocking dependencies**
For each issue: list which issues must complete first. Format: `Blocks: #[n], #[n]`

**Step 6 — Write issues**
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

## Blocks
[Issue numbers this depends on, or "none"]
```

Create via `gh issue create` or output as markdown if no repo confirmed.

## Anti-patterns

- Bundling multiple slices into one issue — one slice per issue, always
- Issues without a done condition — every issue needs a testable endpoint
- Leaving blocking dependencies implicit — declare every dependency explicitly
- Using this skill for markdown-only projects — redirect to /prd-to-plan
- Creating issues before the repo is confirmed — always confirm owner/repo first

## QA

Done when: all slices have issues (or markdown drafts), HITL/AFK classification complete, blocking dependencies declared on every issue, first issue to action identified. Every response ends with NEXT MOVE.
