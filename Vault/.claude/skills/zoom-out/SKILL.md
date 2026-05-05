---
name: zoom-out
description: Goes up a layer of abstraction and maps all relevant modules, callers, entry points, and dependencies for a given area. Use when unfamiliar with a codebase area, when a change has unclear blast radius, or when you need to understand how a section fits into the bigger picture.
argument-hint: "[area, file, or concept to map]"
---

Go up a layer of abstraction. Map what exists — do not explore deeper than necessary.

If a specific area, file, or concept is provided: $ARGUMENTS

## Anti-patterns

- Going too deep — stop at the right abstraction layer. This is a map, not a full audit.
- Asking the operator what the AI can find — explore organically first, report what's there.
- Listing every file — report modules and meaningful groupings, not a directory listing.
- Presenting gaps as errors — flag them as observations, not blockers.

## Process

1. Identify the area of focus — from $ARGUMENTS or ask: "Which area or concept do you want to map?"
2. Explore the codebase or vault organically.
3. Map all relevant modules, callers, and dependencies.

## Output Format

```
AREA: [name of the area being mapped]

MODULES
- [module/file] — [what it does in one line]

CALLERS / DEPENDENTS
- [what calls this] → [what it calls] — [why]

ENTRY POINTS
- [how you reach this area from the system root]

GAPS / NOTES
- [anything missing, inconsistent, or worth flagging]
```

Stop when the map is complete.

## QA
Before closing this skill session:
- [ ] Map stays at the right abstraction level — not a file list, not a deep audit
- [ ] All meaningful callers and entry points identified
- [ ] Gaps flagged as observations, not blockers
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.
