---
name: prd-to-plan
description: Turn a PRD into a phased implementation plan using tracer-bullet vertical slices, saved as a local Markdown file in ./plans/. Use when user wants to break down a PRD, create an implementation plan, or plan phases from a PRD.
---

# PRD to Plan

Break a PRD into a phased plan using vertical slices (tracer bullets). Output: `./plans/[name].md`.

Default for this vault. For GitHub-enabled projects use `/prd-to-issues` instead.

## Process

### 1. Confirm PRD is in context
PRD should be in the conversation. If not, ask the user to paste it or point to the file.

### 2. Explore the vault or codebase
Understand current structure, existing patterns, and how this fits into what already exists.

### 3. Identify durable decisions
Before slicing, surface high-level decisions unlikely to change across phases:
- Code projects: routes, schema shape, key data models, third-party boundaries
- Writing projects: structure, arcs, world rules
- Content projects: platform strategy, format, brand voice decisions
- Business projects: strategy pillars, target market, revenue model

These go in the plan header — every phase references them.

### 4. Draft vertical slices
Each phase = a thin slice cutting through ALL layers end-to-end. See REFERENCE.md for slice rules.

### 5. Quiz the user
Present numbered phases. For each: title + user stories covered.
Ask: granularity right? Merge or split anything?
Iterate until approved.

### 6. Write the plan file
Create `./plans/` if needed. File name = feature name (e.g. `./plans/skill-overhaul.md`).
Use template in [REFERENCE.md](REFERENCE.md).
