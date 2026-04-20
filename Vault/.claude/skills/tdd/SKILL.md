---
name: tdd
description: Test-driven development with red-green-refactor loop using vertical slices. Use when building features or fixing bugs with TDD, user mentions red-green-refactor, or wants test-first development.
---

# TDD

Red-green-refactor via vertical slices — one test at a time, never all tests first.
See [REFERENCE.md](REFERENCE.md) for philosophy, anti-patterns, mocking, and deep modules.

## Workflow

### 1. Plan
- [ ] Confirm what interface changes are needed
- [ ] Confirm which behaviours to test (prioritise — can't test everything)
- [ ] Identify deep module opportunities (small interface, large implementation)
- [ ] Design interfaces for testability
- [ ] List behaviours to test (not implementation steps)
- [ ] Get user approval on the plan

### 2. Tracer bullet
Write ONE test for ONE behaviour → prove the path works end-to-end.

```
RED:   Write test → fails
GREEN: Minimal code to pass → passes
```

### 3. Incremental loop
For each remaining behaviour:
```
RED:   Write next test → fails
GREEN: Minimal code to pass → passes
```
Rules: one test at a time · only enough code for current test · no speculative features.

### 4. Refactor
After all tests pass:
- [ ] Extract duplication
- [ ] Deepen modules
- [ ] Run tests after each refactor step
- Never refactor while RED

## Checklist per cycle
- [ ] Test describes behaviour, not implementation
- [ ] Test uses public interface only
- [ ] Test would survive internal refactor
- [ ] Code is minimal for this test
