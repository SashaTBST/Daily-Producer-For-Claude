Phase 7 of the development pipeline — QA after a ralph loop completes. User reviews the build, reports issues conversationally, agent files them, ralph loop runs again. Use after a ralph loop completes, when running QA, or when the user says "QA session".

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

## QA Loop — How This Fits

After a ralph loop completes: run /qa → file issues → kick ralph loop again → repeat until QA passes clean.

```
ralph loop completes → /qa session → issues filed → ralph loop → /qa session → ...
```

When QA passes clean with no new issues: the build is done.

---

**At session start — ask:** "Is this project GitHub-enabled or Markdown-only?"
- **GitHub** → file with `gh issue create`
- **Markdown** → append to `[project]-Issues-Staging.md` in the project folder

## For each issue

### 1. Listen and clarify
Let the user describe the problem. Ask at most 2–3 short questions:
- What they expected vs what actually happened
- Steps to reproduce (if not obvious)
- Consistent or intermittent?

Do NOT over-interview. If the description is clear — move on.

### 2. Explore in the background
Spawn an Explore subagent to understand the relevant area. Goal: learn domain language and what the feature is supposed to do. NOT to find a fix. Do not reference file paths or line numbers in the filed issue.

### 3. Single issue or breakdown?
Break down when: fix spans multiple independent areas, or there are clearly separable concerns. Keep as one when: one behaviour is wrong in one place.

### 4. File the issue
Rules for all issues:
- No file paths or line numbers — they go stale
- Use the project's domain language
- Describe behaviours, not code
- Reproduction steps are mandatory
- Keep concise — readable in 30 seconds

After filing: print URL (GitHub) or confirm staging entry, then ask: "Next issue, or done?"

### 5. Continue
Each issue is independent. Keep going until user says done.

Every response ends with NEXT MOVE.

---

## REFERENCE

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

### Domain Language
If the project has a `UBIQUITOUS_LANGUAGE.md` — load it. Use its terms in all issue titles and bodies. Do not use internal implementation language (function names, module names, file paths) in filed issues.
