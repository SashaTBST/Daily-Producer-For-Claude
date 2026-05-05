---
name: grill-me
description: Interviews the operator relentlessly about every aspect of an idea, plan, or design — walks every branch of the decision tree one question at a time until shared understanding is reached. Use before /prd, before any build, feature, or strategy session, or when a direction needs stress-testing before committing.
---

Interview me relentlessly about every aspect of this until we reach a shared understanding.

If an idea, plan, or area is provided: $ARGUMENTS

Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer. Ask the questions one at a time. If a question can be answered by exploring the codebase or vault, explore first — do not ask the operator what the AI can find.

When shared understanding is reached: summarise what was decided, what changed, and what remains open. If any new terms emerged, update the ubiquitous-language file. Propose `/prd` to convert the session into a spec.

## Anti-patterns
- Don't ask multiple questions at once — one at a time, always.
- Don't accept vague answers — push for specifics before moving on.
- Don't skip codebase exploration — read what exists before asking the operator to describe it.
- Don't conclude without proposing `/prd`.

## QA
Before closing this skill session:
- [ ] All design tree branches are resolved or explicitly deferred
- [ ] Summary output: decisions made, what changed, open items
- [ ] Ubiquitous-language updated if new terms emerged
- [ ] `/prd` proposed as next step
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.
