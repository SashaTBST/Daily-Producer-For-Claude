---
name: prd
description: Create a Product Requirements Document through grilling, vault exploration, module design, and the vault PRD template. Use when user wants to write a PRD, document a feature or system, or says "write a PRD". Phase 4 of the 7-phase development process.
---

# PRD

Create a PRD. Comes after Idea → Grilling → Research → Prototype. Gates the build.

Run `/grill-me` first if the idea hasn't been pressure-tested yet.

## Process

### 1. Get a detailed description
Ask for a long, detailed description of the problem and any potential solution ideas.

### 2. Explore the vault or codebase
Verify the user's assertions. Understand current state before designing anything.

### 3. Interview relentlessly
Walk every branch of the design tree. Resolve dependencies one by one.
Provide a recommended answer for each question.
Stop only when shared understanding is reached.

### 4. Sketch major components
Identify what needs to be built or modified.
Look for opportunities to extract deep modules (small interface, large implementation).
Confirm with user: do these components match expectations?

### 5. Write the PRD
Use `_AI/TEMPLATES/prd-template.md` as the output template.
Save to staging: `[name] - PRD-Staging.md`
For GitHub-enabled projects: also submit as a GitHub issue.

## Output

- Local: `[name] - PRD-Staging.md` → pipeline: Staging → Prelive → approved
- GitHub (if enabled): `gh issue create` with PRD content
- Then: use `/prd-to-plan` (markdown) or `/prd-to-issues` (GitHub) to break it down
