# /qa — Reference

## Issue Templates

### Single issue (GitHub)
```
## What happened
[Actual behaviour, in plain language]

## What I expected
[Expected behaviour]

## Steps to reproduce
1. [Concrete steps using domain terms]
2. [Include relevant inputs or configuration]

## Additional context
[Extra observations — domain language, no file paths]
```

### Single issue (Markdown — append to [project]-Issues-Staging.md)
```
## Issue — [short title]
**Reported:** [date]
**Status:** Open

### What happened
[Description]

### What I expected
[Expected behaviour]

### Steps to reproduce
1. [Steps]

### Notes
[Context]
```

### Breakdown — multiple issues (GitHub)
Create in dependency order (blockers first) so you can reference real issue numbers.
```
## Parent issue
#<parent-issue-number> or "Reported during QA session"

## What's wrong
[This specific slice only]

## What I expected
[Expected behaviour for this slice]

## Steps to reproduce
1. [Steps specific to THIS issue]

## Blocked by
- #<issue-number> or "None — can start immediately"

## Additional context
[Relevant observations for this slice]
```

Breaking down rules: many thin issues over few thick ones — each independently fixable. Mark blocking relationships honestly. Maximise parallelism.

## Domain Language

If the project has a `UBIQUITOUS_LANGUAGE.md` — load it at session start. Use its terms in all issue titles and bodies. Do not use internal implementation language (function names, module names, file paths) in filed issues.
