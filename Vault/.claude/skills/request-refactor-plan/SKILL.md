---
name: request-refactor-plan
description: Create a detailed refactor plan with tiny commits via user interview, then file it as a GitHub issue or Markdown staging entry. Use when user wants to plan a refactor, create a refactoring RFC, or break a refactor into safe incremental steps.
---

# Request Refactor Plan

**At start — ask:** "Is this project GitHub-enabled or Markdown-only?"
- **GitHub** → file plan as a GitHub issue
- **Markdown** → file plan to `plans/[feature]-refactor.md`

## Steps

1. **Interview** — Ask for a long, detailed description of the problem and any ideas for solutions.

2. **Explore** — Search the codebase to verify assertions and understand current state.

3. **Present alternatives** — Ask if they've considered other options. Present any alternatives you've identified.

4. **Deep interview on implementation** — Be extremely detailed and thorough. Resolve all open questions.

5. **Hammer scope** — Work out exactly what you plan to change and what you plan NOT to change. Write it down. Confirm with user.

6. **Check test coverage** — Find tests for the affected area. If insufficient, ask: "What's your plan for testing this?"

7. **Plan tiny commits** — Break implementation into the smallest possible commits. Each must leave the codebase in a working state. Follow Martin Fowler: "Make each refactoring step as small as possible, so you can always see the program working."

8. **File the plan** — See REFERENCE.md for the full plan template.

**Do NOT include specific file paths or code snippets in the plan** — they go stale. Describe behaviour and interfaces instead.
