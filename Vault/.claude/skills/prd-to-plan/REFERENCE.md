# PRD to Plan — Reference

## Vertical Slice Rules

- Each slice delivers a narrow but COMPLETE path through every layer (schema, API, UI, tests — or equivalent for non-code projects)
- A completed slice is demoable or verifiable on its own
- Prefer many thin slices over few thick ones
- Do NOT include specific file names, function names, or implementation details likely to change
- DO include durable decisions: route paths, schema shapes, data model names, structural choices

## Plan File Template

```md
# Plan: <Feature or Project Name>

> Source PRD: <brief identifier or link>

## Durable decisions

Decisions that apply across all phases:

- **[Decision type]**: ...
- **[Decision type]**: ...

---

## Phase 1: <Title>

**User stories**: <list from PRD>

### What to build

A concise description of this vertical slice. End-to-end behaviour, not layer-by-layer implementation.

### Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

---

## Phase 2: <Title>

**User stories**: <list from PRD>

### What to build

...

### Acceptance criteria

- [ ] ...
```

## Notes

- Repeat phase block for each approved slice
- Do not include specific file paths or line numbers — they go stale
- Reference user stories by number from the parent PRD
- Each phase should be independently completable and verifiable
