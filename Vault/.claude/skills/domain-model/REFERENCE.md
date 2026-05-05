---
name: domain-model-reference
description: Extended reference for /domain-model — ADR format template.
type: reference
---

## ADR Format Template

```markdown
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

**Save paths:**
- Project decision: `[ProjectFolder]/Working Files/AI/Decisions/[YYYY-MM-DD]-[decision-slug].md`
- System-level decision: `_AI/DECISIONS/[YYYY-MM-DD]-[decision-slug].md`

**Three criteria for creating an ADR:**
1. The decision is hard or costly to reverse
2. It would be surprising or confusing without context
3. It resulted from genuine trade-offs (not an obvious choice)
