Write a Product Requirements Document — the mandatory gate before any plan or build begins. Use after /grill-me to convert shared understanding into a structured spec. Required before /prd-to-plan or /prd-to-issues.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

If a system, feature, or topic is provided: $ARGUMENTS

**No plan or build begins without a PRD. This is the gate.**

---

## Phase 1 — Brief

Ask these questions one at a time. **Skip any already answered in a prior /grill-me session — confirm what was covered and start from the next open question.**

1. What is being built? (name + one-sentence function)
2. What problem does it solve? (the friction — not the feature)
3. Who uses it? (role, context, how they work today)
4. What does success look like? (measurable outcome)
5. What is explicitly NOT included? (scope boundary — be specific)
6. What is the smallest version that tests the core idea?
7. What would make this not worth building? (kill criteria)

---

## Phase 2 — Repo/Vault Exploration

If this is a code or vault project: explore the codebase before proceeding.

Verify: are the operator's assumptions about the current state accurate? Flag any contradictions before writing the spec.

---

## Phase 3 — Module Sketch

Identify the major modules to build or modify. For each:

```
Module: [name]
What changes: [new / modified / no change]
Interface: [what callers use — functions, endpoints, commands]
Test surface: [what can be tested at this module's boundary]
```

Present the module sketch. Confirm before writing the PRD.

---

## Phase 4 — PRD Generation

Populate `_AI/TEMPLATES/prd-template.md` with brief answers + module sketch.

**Naming:** `[SystemName]-PRD-Staging.md`
**Location:** `_AI/System PRDs/` for system builds, relevant project folder for project builds. Run `/file-route` if unsure.

**For GitHub-enabled projects:** also submit as a GitHub issue via `gh issue create`. Ask: "GitHub-enabled or markdown-only?"

After writing: state "PRD ready. Review it. Then run /prd-to-plan or /prd-to-issues."

---

## Phase 5 — Review

Prelive checklist before promotion:
- [ ] One-sentence summary describes the transformation, not the feature set
- [ ] Core outcome is measurable
- [ ] "Explicitly Not Included" is specific
- [ ] Kill criteria defined
- [ ] Loop 1 is smallest build that validates core idea
- [ ] No hidden assumptions — all listed in spec
- [ ] Module sketch included with test surfaces identified

---

Every response ends with NEXT MOVE.
