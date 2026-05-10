---
name: prd
description: Writes a Product Requirements Document — the mandatory gate before any plan or build begins. Runs a 7-question brief, explores the codebase, sketches modules, then generates the PRD in staging. Use after /grill-me to convert shared understanding into a structured spec before /prd-to-plan or /prd-to-issues.
argument-hint: "[system, feature, or topic to spec]"
---

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

If a system, feature, or topic is provided: $ARGUMENTS

**No plan or build begins without a PRD. This is the gate.**

## Phase 1 — Brief

Ask these questions one at a time. Skip any already answered in a prior /grill-me session — confirm what was covered and start from the next open question.

1. What is being built? (name + one-sentence function)
2. What problem does it solve? (the friction — not the feature)
3. Who uses it? (role, context, how they work today)
4. What does success look like? (measurable outcome)
5. What is explicitly NOT included? (scope boundary — be specific)
6. What is the smallest version that tests the core idea?
7. What would make this not worth building? (kill criteria)

## Phase 2 — Repo/Vault Exploration

If this is a code or vault project: explore the codebase before proceeding.

Verify: are the operator's assumptions about the current state accurate? Flag any contradictions before writing the spec.

## Phase 3 — Module Sketch

Identify the major modules to build or modify. For each:

```
Module: [name]
What changes: [new / modified / no change]
Interface: [what callers use — functions, endpoints, commands]
Test surface: [what can be tested at this module's boundary]
```

Present the module sketch. Confirm before writing the PRD.

## Phase 4 — PRD Generation

Populate `_AI/TEMPLATES/prd-template.md` with brief answers + module sketch.

**Naming:** `[SystemName]-PRD-Staging.md`
**Location:** `_AI/System PRDs/` for system builds, relevant project folder for project builds. Run `/file-route` if unsure.

Ask: "GitHub-enabled or markdown-only?" — if GitHub: also submit via `gh issue create`.

After writing: "PRD ready. Review it. Then run /prd-to-plan or /prd-to-issues."

## Phase 5 — Prelive Checklist

- [ ] One-sentence summary describes the transformation, not the feature set
- [ ] Core outcome is measurable
- [ ] "Explicitly Not Included" is specific
- [ ] Kill criteria defined
- [ ] Loop 1 is smallest build that validates core idea
- [ ] No hidden assumptions — all listed in spec
- [ ] Module sketch included with test surfaces identified

## Anti-patterns

- Running /prd before /grill-me — gaps surface mid-spec and cause rewrites
- Re-asking questions already answered in /grill-me — read the session context first
- Writing to a live PRD file — staging only until operator promotes
- Bundling multiple features into one PRD — one system, one spec
- Skipping Phase 2 exploration on code projects — assumptions get baked into the spec

## QA

Done when: PRD file written to staging, prelive checklist passes, operator has confirmed next step (/prd-to-plan or /prd-to-issues).

Every response ends with NEXT MOVE.
