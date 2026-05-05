---
name: domain-model
description: Conducts a relentless design interview that enforces shared vocabulary in real-time — flags terminology conflicts, sharpens fuzzy language, updates the vocabulary file inline, and creates ADRs for significant decisions. Use when designing a system, making architectural choices, or when precise language and documented trade-offs matter — deeper than /grill-me.
disable-model-invocation: true
---

Design interview that enforces and updates shared language in real-time. Like /grill-me but also flags vocabulary conflicts, coins canonical terms, and creates ADRs.

If a plan, design, or project area is provided: $ARGUMENTS

## Before Interviewing — Load Context

**Step 1 — Find vocabulary source**
Check for, in order:
1. Project vocabulary file: `[ProjectFolder]/Working Files/AI/ubiquitous-language.md`
2. System vocabulary via `/ubiquitous-language` vault core terms
3. If neither exists: create it immediately — do not wait for the end of the session. Seed it with the first agreed term.

**Step 2 — Find ADR location**
Check for `[ProjectFolder]/Working Files/AI/Decisions/` or `_AI/DECISIONS/` for system-level decisions. Create the folder when the first ADR is needed.

## Interview Process

One question at a time. Wait for an answer before asking the next. If a question can be answered by exploring the codebase or vault — explore first. Do not ask the operator what the AI can find.

During every answer:

**Flag vocabulary conflicts** — if the operator uses a term that conflicts with the vocabulary file, stop:
`"TERMINOLOGY CONFLICT: You used '[term]' but the vocabulary defines it as '[definition]'. Do you mean '[canonical term]', or are we coining a new concept?"`

**Sharpen fuzzy terms** — when language is vague, propose a precise canonical form:
`"You said '[vague phrase]'. Can we call this '[proposed term]'? That distinguishes it from '[related term]' by [specific difference]."`

**Update vocabulary inline** — when a term is agreed, write it to the vocabulary file immediately. Don't batch at the end.

**Test with scenarios** — probe edge cases: "What happens when [specific edge case]? Does your model handle that?"

**Cross-reference code** — when a stated design contradicts the actual implementation:
`"CONTRADICTION: The codebase does [X], but your design assumes [Y]. Which is correct?"`

Stop when shared understanding is reached. Summarise: decisions made, terms added or updated, open questions.

## ADR Creation

Create an ADR only when ALL three are true: (1) the decision is hard or costly to reverse, (2) it would be surprising without context, (3) it resulted from genuine trade-offs. See REFERENCE.md for the ADR format template.

Save to: `[ProjectFolder]/Working Files/AI/Decisions/[YYYY-MM-DD]-[decision-slug].md`
System-level decisions: `_AI/DECISIONS/[YYYY-MM-DD]-[decision-slug].md`

## Anti-patterns
- Don't batch vocabulary updates — write each agreed term immediately, not at session end.
- Don't create an ADR for obvious choices — only for reversible, context-dependent trade-offs.
- Don't skip loading the vocabulary file — flagging conflicts requires knowing what's already defined.
- Don't ask the operator what the codebase contains — explore it first.

## QA
Before closing this skill session:
- [ ] Vocabulary file updated with all new terms agreed this session
- [ ] All terminology conflicts surfaced and resolved
- [ ] ADRs created for decisions meeting the three-criteria threshold
- [ ] Summary: decisions made, terms added, open questions
- [ ] Every response ended with NEXT MOVE

See REFERENCE.md for ADR format template.

Every response ends with NEXT MOVE.
