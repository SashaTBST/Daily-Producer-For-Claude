Stress-test architectural plans and decisions against the project's established vocabulary and documented decisions. Use when designing a system or making significant architectural choices — deeper than /grill-me because it enforces and updates shared language in real-time.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

If a plan, design, or project area is provided: $ARGUMENTS

## Purpose

Design interviews produce better outcomes when terminology is precise and decisions are documented. This skill conducts a relentless interview like /grill-me, but also:
- Challenges against the project's existing vocabulary — flags conflicts immediately
- Sharpens fuzzy language into precise canonical terms during the session
- Updates the vocabulary file inline as terms are agreed
- Creates Architecture Decision Records (ADRs) for significant decisions

---

## Before Interviewing — Load Context

**Step 1 — Find vocabulary source**
Check for, in order:
1. Project ubiquitous-language file: `[ProjectFolder]/[Project] - AI/ubiquitous-language.md`
2. System vocabulary in `/ubiquitous-language` vault core terms
3. If neither exists: flag it and create it immediately — do not wait for the end of the session. File path: `[ProjectFolder]/[Project] - AI/ubiquitous-language.md`. Write the first agreed term as the seed entry. Note: overlaps with /ubiquitous-language skill — if that skill has already created a vocabulary file for this project, load it here.

**Step 2 — Find ADR location**
Check for `[ProjectFolder]/[Project] - AI/Decisions/` or `_AI/DECISIONS/` for system-level decisions.
If neither exists: create the folder when the first ADR is needed.

---

## Interview Process

Conduct the interview one question at a time. Wait for an answer before asking the next.

**If a question can be answered by exploring the codebase or vault — explore first. Do not ask the operator what the AI can find.**

During every answer:

**Flag vocabulary conflicts** — if the operator uses a term that conflicts with a definition in the vocabulary file, stop immediately:
`"TERMINOLOGY CONFLICT: You used '[term]' but the vocabulary defines it as '[definition]'. Do you mean '[canonical term]' instead, or are we coining a new concept?"`

**Sharpen fuzzy terms** — when language is vague, propose a precise canonical form:
`"You said '[vague phrase]'. Can we call this '[proposed term]'? That would distinguish it from '[related term]' by [specific difference]."`

**Update vocabulary inline** — when a term is agreed, write it to the vocabulary file immediately. If the file doesn't exist yet, create it now. Don't batch at the end. Don't offer to update — just do it.

**Test with scenarios** — probe edge cases with concrete examples: "What happens when [specific edge case]? Does your model handle that?"

**Cross-reference code** — when a stated design contradicts the actual implementation, surface it:
`"CONTRADICTION: The codebase does [X], but your design assumes [Y]. Which is correct?"`

Stop when shared understanding is reached. Summarise: decisions made, terms added or updated, open questions.

---

## ADR Creation

Create an ADR only when ALL three criteria are met:
1. The decision is hard or costly to reverse
2. It would be surprising or confusing without context
3. It resulted from genuine trade-offs (not an obvious choice)

**ADR format:**
```
# [Decision title]
Date: [YYYY-MM-DD]
Status: Accepted

## Context
[What situation or problem forced this decision]

## Decision
[What was decided — one clear statement]

## Trade-offs considered
- Option A: [what it offered, why rejected]
- Option B: [what it offered, why rejected]
- Chosen: [chosen option and why]

## Consequences
[What this decision makes easier, harder, or impossible]
```

Save to: `[ProjectFolder]/[Project] - AI/Decisions/[YYYY-MM-DD]-[decision-slug].md`
For system decisions: `_AI/DECISIONS/[YYYY-MM-DD]-[decision-slug].md`

---

Every response ends with NEXT MOVE.
