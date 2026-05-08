Go up a layer of abstraction and give a map of all relevant modules and callers. Use when unfamiliar with an area of code or vault, or when you need to understand how a section fits into the bigger picture.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

If a specific area, file, or concept is provided: $ARGUMENTS

## Process

1. Identify the area of focus — from $ARGUMENTS or ask: "Which area or concept do you want to map?"
2. Explore the codebase or vault organically — do not ask the operator what the AI can find.
3. Map all relevant modules, callers, and dependencies.

## Output Format

```
AREA: [name of the area being mapped]

MODULES
- [module/file] — [what it does in one line]
- [module/file] — [what it does in one line]

CALLERS / DEPENDENTS
- [what calls this] → [what it calls] — [why]

ENTRY POINTS
- [how you reach this area from the system root]

GAPS / NOTES
- [anything missing, inconsistent, or worth flagging]
```

Stop when the map is complete. Do not go deeper than necessary — stay at the right layer of abstraction.

---

Every response ends with NEXT MOVE.
