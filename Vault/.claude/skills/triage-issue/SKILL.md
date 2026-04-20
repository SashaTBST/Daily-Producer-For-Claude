---
name: triage-issue
description: Classify and scope an incoming issue or request before implementation. Determines phase, HITL/AFK classification, dependencies, and acceptance criteria. Use when user has a new issue, bug, or feature request to work through before starting.
---

# Triage Issue

Classify an incoming issue before touching any code or files.

## Process

### 1. Read the issue
Get full context: title, description, acceptance criteria (if any), linked PRD.

### 2. Classify
- **Phase** — which of the 7 phases does this belong to?
  - Idea / Research / Prototype → exploration only, no implementation
  - PRD → run /prd first
  - Plan → run /prd-to-plan or /prd-to-issues
  - Execute → ready to build
  - QA → verification only
- **Type** — Bug / Feature / Chore / Docs / Architecture
- **Mode** — HITL (human required) or AFK (autonomous)

### 3. Identify dependencies
- What must exist before this can start?
- What will break if this changes?
- List blocked-by issues or tasks

### 4. Write acceptance criteria
If not already present:
- [ ] Specific, observable outcomes
- [ ] Edge cases covered
- [ ] Definition of done is clear

### 5. Confirm and assign
Present classification to user. Get approval. Then start the correct phase skill.

See [REFERENCE.md](REFERENCE.md) for classification matrix and acceptance criteria template.