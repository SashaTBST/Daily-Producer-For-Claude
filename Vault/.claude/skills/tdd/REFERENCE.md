# TDD — Reference

## Core Principle

Tests verify behaviour through public interfaces, not implementation details.
Code can change entirely — tests shouldn't.

**Good test:** exercises real code paths through public APIs. Reads like a specification.
**Bad test:** coupled to implementation. Mocks internal collaborators. Breaks on refactor.

Warning sign: your test breaks when you refactor but behaviour hasn't changed.

## Anti-Pattern: Horizontal Slices

**Never write all tests first, then all implementation.**

```
WRONG (horizontal):
  RED:   test1, test2, test3, test4, test5
  GREEN: impl1, impl2, impl3, impl4, impl5

RIGHT (vertical):
  RED→GREEN: test1→impl1
  RED→GREEN: test2→impl2
  RED→GREEN: test3→impl3
```

Horizontal slicing produces tests that test imagined behaviour, not actual behaviour.
You commit to test structure before understanding the implementation.

## Deep Modules

Deep module = small interface + large implementation (John Ousterhout).

```
┌──────────────────┐
│  Small Interface │  ← Few methods, simple params
├──────────────────┤
│                  │
│ Deep             │  ← Complex logic hidden
│ Implementation   │
└──────────────────┘
```

Shallow modules (large interface, thin implementation) are hard to test and hard for AI to navigate. When designing interfaces, ask: can I reduce methods? Simplify params? Hide more complexity inside?

## Mocking Guidelines

- Mock at system boundaries only (external APIs, databases, file system)
- Do not mock internal collaborators
- If you must mock something internal, the module boundary is wrong

## Refactoring

After all tests pass:
- Extract duplication into named abstractions
- Move complexity behind simpler interfaces (deepen shallow modules)
- Apply SOLID principles where natural — not dogmatically
- One refactor at a time, test after each
- Never refactor while RED
