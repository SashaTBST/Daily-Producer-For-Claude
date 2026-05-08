Interview relentlessly about every aspect of an idea or plan — walk every branch of the design tree until shared understanding is reached. Use at the start of any build, feature, or strategy session, or before /prd, to discover and resolve all design decisions before committing to a direction.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

If an idea, plan, or area is provided: $ARGUMENTS

Walk down the design tree one branch at a time — resolving dependencies between decisions before moving on. If one decision depends on another, resolve the dependency first.

Ask one question at a time. Do not accept vague answers — push for specifics. Do not move to the next question until the current one is answered.

**If a question can be answered by exploring the codebase or vault — explore first. Do not ask the operator what the AI can find.**

Cover: what problem does this actually solve, who specifically has this problem, what have they tried before, why will this approach work when others haven't, what is the smallest version that tests the core idea, what would make this not worth doing, what design decisions have branching consequences.

Stop when shared understanding is reached. Summarise: what was decided, what changed during the session, what is still open.

After closing: if any new terms were introduced, update the ubiquitous-language file.

Propose next step: `/prd` — to convert this shared understanding into a spec.

Every response ends with NEXT MOVE.
