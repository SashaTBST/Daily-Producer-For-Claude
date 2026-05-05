---
name: request-refactor-plan
description: Creates a detailed refactor plan with tiny commits via user interview, then files it as a GitHub issue or Markdown staging entry. Use when planning a refactor, creating a refactoring RFC, or breaking a refactor into safe incremental steps.
---

**At start — ask:** "Is this project GitHub-enabled or Markdown-only?"
- GitHub → file plan as a GitHub issue
- Markdown → file plan to `plans/[feature]-refactor.md`

## Anti-patterns

- Writing implementation before the plan is filed and approved — interview first, build nothing.
- Including file paths or code snippets in the plan — they go stale. Describe behaviour and interfaces.
- Single mega-commit for a refactor — every commit must leave the codebase in a working state.
- Skipping test coverage check — if tests are insufficient, surface it before planning commits.
- Treating scope as flexible — hammer scope explicitly and write down what is OUT before filing.

## Steps

1. **Interview** — Ask for a long, detailed description of the problem and any ideas for solutions.
2. **Explore** — Search the codebase to verify assertions and understand current state.
3. **Present alternatives** — Ask if they've considered other options. Present any alternatives identified.
4. **Deep interview on implementation** — Be extremely detailed. Resolve all open questions.
5. **Hammer scope** — Work out exactly what changes and what does NOT. Write it down. Confirm.
6. **Check test coverage** — Find tests for the affected area. If insufficient: "What's your plan for testing this?"
7. **Plan tiny commits** — Break implementation into the smallest possible commits. Each must leave the codebase working.
8. **File the plan** — See REFERENCE.md for the full plan template and filing commands.

## QA
Before closing this skill session:
- [ ] Plan filed before any implementation started
- [ ] No file paths or code snippets in the filed plan
- [ ] Every commit leaves the codebase in a working state
- [ ] Scope explicitly declared — what's in AND what's out
- [ ] Test coverage checked and documented
- [ ] Every response ended with NEXT MOVE

See REFERENCE.md for plan template and GitHub/Markdown filing instructions.

Every response ends with NEXT MOVE.
