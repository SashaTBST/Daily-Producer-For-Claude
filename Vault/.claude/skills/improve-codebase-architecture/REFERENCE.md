# Improve Codebase Architecture — Reference

## Deep Modules (John Ousterhout)

Deep module = small interface + large implementation.

```
┌──────────────────┐
│  Small Interface │  ← Few methods, simple params
├──────────────────┤
│                  │
│ Deep             │  ← Complex logic hidden inside
│ Implementation   │
└──────────────────┘
```

Shallow module = large interface + thin implementation.
Problem: AI must navigate everything at once. No complexity is hidden.

## AI-Readiness Checklist

- [ ] Entry points are obvious — one clear place to start reading
- [ ] Module names match ubiquitous language
- [ ] Public interfaces are minimal and stable
- [ ] Dependencies flow one way (no cycles)
- [ ] Business logic is not scattered across layers
- [ ] No god objects or god files

## Anti-Patterns

**Leaky abstraction** — implementation details exposed in the interface.
**Wide interface** — 10+ methods on a class/module that could be 2.
**Mystery guest** — module depends on global state instead of explicit params.
**Tangled layers** — presentation calls persistence directly; no clear separation.
**Naming fog** — `utils.js`, `helpers.ts`, `misc.md` — vague names that force AI to read everything.

## Refactoring Priority

1. Rename for ubiquitous language (zero risk, high gain)
2. Extract hidden logic into named functions (clarifies intent)
3. Deepen shallow modules (reduce surface area)
4. Untangle dependencies (requires careful testing)
5. Introduce explicit dependency injection (removes mystery guests)
