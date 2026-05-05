---
name: qa
description: Interactive QA session — user describes bugs conversationally, agent clarifies and files each as a GitHub issue or Markdown entry. Use when a ralph loop completes, when running post-build review, or when the user says "QA session".
---

Phase 7 of the development pipeline. After a ralph loop completes: run /qa → file issues → ralph loop again → repeat until QA passes clean.

```
ralph loop → /qa session → issues filed → ralph loop → /qa session → ... → QA clean → done
```

**At session start — ask once:** "Is this project GitHub-enabled or Markdown-only?"
- GitHub → file with `gh issue create`
- Markdown → append to `[project]-Issues-Staging.md` in the project folder

If the project has a `UBIQUITOUS_LANGUAGE.md` — load it. Use its terms in all issue titles and bodies.

## Anti-patterns

- Over-interviewing — if the description is clear, file it. 2–3 questions max, never more.
- File paths and line numbers in issues — they go stale. Describe behaviours, not code.
- One mega-issue for a multi-area problem — break into independently fixable slices.
- Exploring to find a fix — the Explore subagent learns domain language only. No fix proposals during QA.
- Closing the session before asking "Next issue, or done?" after every filed issue.

## Workflow

**1. Listen and clarify**
Let the user describe the problem. Ask at most 2–3 short questions:
- What they expected vs what actually happened
- Steps to reproduce (if not obvious)
- Consistent or intermittent?

**2. Explore in the background**
Spawn an Explore subagent to understand the relevant area. Goal: learn domain language and what the feature is supposed to do. Not to find a fix. Do not surface file paths or line numbers in the issue.

**3. Single issue or breakdown?**
Break down when: fix spans multiple independent areas, or there are clearly separable concerns.
Keep as one when: one behaviour is wrong in one place.

**4. File the issue**
See REFERENCE.md for GitHub and Markdown templates.
After filing: print URL (GitHub) or confirm staging entry, then ask: "Next issue, or done?"

**5. Continue**
Each issue is independent. Keep going until user says done.

## QA
Before closing this skill session:
- [ ] Every issue was filed — none left in "I'll file that later" limbo
- [ ] No file paths or line numbers appear in any filed issue
- [ ] Issues are broken down to independently fixable slices
- [ ] Domain language used throughout (not implementation language)
- [ ] Every response ended with NEXT MOVE

See REFERENCE.md for issue templates and breakdown format.

Every response ends with NEXT MOVE.
