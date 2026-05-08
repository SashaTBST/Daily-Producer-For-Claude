Test-driven development via vertical slices — one test at a time, red-green-refactor. Use when building any feature with testable output: code, data pipelines, structured documents, or workflow logic.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

If a feature, slice, or issue is provided: $ARGUMENTS

## Purpose

Tests verify behavior through public interfaces — not implementation details. Code can change entirely; tests shouldn't. A good test reads like a spec: "user can checkout with valid cart." It survives refactors because it doesn't care about internal structure.

---

## Anti-Pattern: Do NOT Write All Tests First

Do not write all tests, then all implementation. That is horizontal slicing and produces bad tests:
- Tests written in bulk test imagined behavior, not actual behavior
- You end up testing the shape of things, not user-facing behavior
- Tests become insensitive to real changes

**Correct:** Vertical slices. One test → one implementation → repeat. Each test responds to what you learned from the previous cycle.

---

## Process

**Step 1 — Plan (before writing any test)**
Confirm with the user:
- What interface changes are needed?
- Which behaviors to test? (prioritize — you can't test everything)
- Are there deep module opportunities? (small interface, large implementation)
- Design interfaces for testability

List the behaviors to test (not implementation steps). Get user approval before proceeding.

**Step 2 — Tracer bullet (Red)**
Write ONE test that confirms ONE thing about the system. This is the tracer bullet — it proves the path works end-to-end. The test must:
- Test one behavior, not one function
- Use the public interface only
- Fail for the right reason (missing implementation, not a syntax error)
- Read like a specification

Confirm it is correct before proceeding.

**Step 3 — Minimum implementation (Green)**
Write the smallest code that makes the test pass. No extras. No speculation about future tests.

**Step 4 — Incremental loop**
For each remaining behavior: RED (write test → fails) → GREEN (minimal code → passes). One test at a time.

**Step 5 — Refactor**
After all tests pass: clean up. Extract duplication. Deepen modules. Apply SOLID where natural.
Consider what new code reveals about existing code. Run tests after each refactor step.
Never refactor while RED.

**Step 6 — Extended test passes**
After functional green: run relevant passes from REFERENCE (security, storage, commercial, performance, a11y). Not all passes apply to every slice — select based on what the slice touches.

---

## Per-Cycle Checklist

```
[ ] Test describes behavior, not implementation
[ ] Test uses public interface only
[ ] Test would survive an internal refactor
[ ] Code is minimal for this test
[ ] No speculative features added
```

---

## Rules

- Red before green — never write implementation before the test
- One test per behavior — not one test per function
- Minimum implementation only
- Refactor with tests green — never refactor on red
- Vertical slices — test a full behavior end-to-end, not a layer
- Better tests over more tests

---

## For Non-Code Projects

TDD applies beyond code:
- Red = define the expected output structure before producing content
- Green = produce content that meets the structure
- Refactor = review and tighten

---

Every response ends with NEXT MOVE.

---

## REFERENCE

### Extended Test Passes

Run these after functional green. Select passes relevant to what the slice touches.

**Security pass** (auth, input handling, data access, APIs)
- Authentication: is access gated correctly?
- Authorisation: can a user access another user's data? Are role boundaries enforced?
- Input validation: are all inputs sanitised? Injection vectors blocked (SQL, XSS, command)?
- Output encoding: is sensitive data masked or excluded from responses?

**Storage pass** (databases, files, caches, migrations)
- Persistence: does state survive a restart?
- Migration safety: runs forward cleanly? Can it be reversed without data loss?
- Concurrency: race conditions under simultaneous writes?
- Integrity: are constraints enforced? Can invalid data states be reached?

**Commercial pass** (pricing, billing, permissions, subscriptions, business rules)
- Pricing logic: are rates calculated correctly? Edge cases at tier boundaries?
- Billing: are charges triggered at the right events? No double charges?
- Permissions: do plan/tier restrictions correctly gate features?
- Audit trail: are commercial events logged for compliance?

**Performance pass** (critical paths, large data)
- Response time under expected load
- Memory usage under sustained operation
- Query efficiency: N+1, missing indexes, large scans

**Accessibility pass** (UI slices)
- Keyboard navigation works
- Screen reader labels on all interactive elements
- Colour contrast WCAG AA
- Error states announced, not just styled

### Mocking Rules

- Mock at system boundaries only (external APIs, file system, time, randomness)
- Never mock the module under test
- If a module is hard to test without mocking its internals: it is a design problem
- Test databases: prefer a real test database over in-memory mock — mocked DB tests pass when real ones fail
