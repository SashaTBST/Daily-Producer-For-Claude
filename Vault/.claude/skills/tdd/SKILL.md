---
name: tdd
description: Runs test-driven development via vertical slices — one test, one implementation, red-green-refactor, repeat. Use when building any feature with testable output: code, data pipelines, structured documents, or workflow automation.
---

Test-driven development via vertical slices — one test at a time, red-green-refactor.

If a feature, slice, or issue is provided: $ARGUMENTS

## Anti-patterns

**WRONG — horizontal slicing (do not do this):**
```
RED:   test1  test2  test3  test4
GREEN: impl1  impl2  impl3  impl4
```

**RIGHT — vertical slicing:**
```
RED → GREEN: test1 → impl1
RED → GREEN: test2 → impl2
RED → GREEN: test3 → impl3
```

Horizontal slicing produces tests that verify imagined behaviour, not real behaviour. Tests written in bulk test the shape of things — they don't survive real changes.

Additional failure modes:
- Writing implementation before the test — never GREEN before RED
- Testing implementation details instead of public interface behaviour
- Mocking the module under test
- Refactoring while RED

## Workflow

**Step 1 — Plan**
Confirm with the user: which behaviours to test (you can't test everything — prioritise), what interface changes are needed, and whether there are deep module opportunities. List behaviours to test — get approval before writing any test.

**Step 2 — Tracer bullet (RED)**
Write ONE test that confirms ONE behaviour end-to-end. It must: test one behaviour (not one function), use the public interface only, fail for the right reason (missing implementation — not a syntax error), and read like a specification.

**Step 3 — Minimum implementation (GREEN)**
Write the smallest code that makes the test pass. No extras. No speculation about future tests.

**Step 4 — Loop**
For each remaining behaviour: RED (write test → fails) → GREEN (minimal code → passes). One test at a time.

**Step 5 — Refactor**
After all tests pass: extract duplication, deepen modules, apply SOLID where natural. Run tests after every step. Never refactor while RED.

**Step 6 — Extended passes**
After functional green: run relevant passes from REFERENCE.md (security, storage, commercial, performance, a11y). Select only passes relevant to what the slice touches.

## For Non-Code Projects

TDD applies to any work with verifiable output:

| Context | RED | GREEN | Refactor |
|---------|-----|-------|----------|
| Writing | Define structure + acceptance criteria before drafting | Produce content that meets criteria | Tighten, cut, restructure |
| Data pipeline | Define expected schema + sample output before transforms | Write the transform | Optimise, clean |
| Workflow / skill | Define what "done" looks like before building | Build to that definition | Simplify |
| Strategy doc | Define the questions the doc must answer | Write the doc | Edit for clarity |

The principle is the same: commit to a verifiable output before producing anything.

## QA
Before closing this skill session:
- [ ] Every behaviour was tested before it was implemented (RED before GREEN)
- [ ] Tests use public interface only — no internal implementation details
- [ ] No speculative features were added
- [ ] Refactor ran only while GREEN
- [ ] Extended passes applied to relevant slice areas
- [ ] Every response ended with NEXT MOVE

See REFERENCE.md for per-cycle checklist, extended test passes, and mocking rules.

Every response ends with NEXT MOVE.
