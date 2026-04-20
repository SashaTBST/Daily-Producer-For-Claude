---
name: prd-to-issues
description: Break a PRD into independently-grabbable GitHub issues using tracer-bullet vertical slices with HITL/AFK classification. Use when user wants to convert a PRD to GitHub issues or the project is GitHub-enabled.
---

# PRD to Issues

Break a PRD into GitHub issues using vertical slices. Use for GitHub-enabled projects only.
For markdown-only projects, use `/prd-to-plan` instead.

## Process

### 1. Locate the PRD
Ask for the PRD GitHub issue number or URL. Fetch with `gh issue view <number>` (with comments).

### 2. Explore the vault or codebase (optional)
If not already explored, understand current state before slicing.

### 3. Draft vertical slices
Each issue = a tracer bullet — thin slice through ALL layers end-to-end.

Each slice is classified:
- **HITL** — requires human interaction (architectural decision, design review)
- **AFK** — can be implemented and merged autonomously

Prefer AFK over HITL where possible. See REFERENCE.md for slice rules.

### 4. Quiz the user
Present numbered list. For each slice: title · type (HITL/AFK) · blocked by · user stories covered.

Ask: granularity right? Dependencies correct? HITL/AFK correct? Merge or split anything?
Iterate until approved.

### 5. Create GitHub issues
Use `gh issue create` in dependency order (blockers first).
Use issue template in [REFERENCE.md](REFERENCE.md).

Do NOT close or modify the parent PRD issue.
