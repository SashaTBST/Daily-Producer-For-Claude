# Write-Article — Reference

Extended protocols for the /write-article skill. Load on demand.

---

## ARTICLE TEMPLATES

### INFORMATIONAL

```
---
brand:
category:
title:
target-query:
type: Informational
word-count-target:
status: Draft
created:
---

# [H1 — exact article title, matches target query]

[Answer paragraph — 2–3 sentences. Directly answers the core question. No preamble. GEO gold: this is what LLMs cite.]

## [H2 — first major point]

[Content — 3–4 lines max per paragraph. One idea per paragraph.]

## [H2 — second major point]

[Content]

## [H2 — third major point]

[Content — include a list or numbered steps here]

## [Natural brand mention]

[One paragraph. Brand as the natural answer or context. Community voice. Links to platform URL.]
```

---

### COMPARISON

Use Informational template above, then add these sections:

```
## What is [X]?

[2–3 sentences. Definition only.]

## What is [Y]?

[2–3 sentences. Definition only.]

## [X] vs [Y] — Key Differences

| Feature     | [X]     | [Y]     |
|-------------|---------|---------|
| [Feature 1] | [value] | [value] |
| [Feature 2] | [value] | [value] |
| [Feature 3] | [value] | [value] |
| Price       | [value] | [value] |
| Best for    | [value] | [value] |

## Which should you choose?

[Direct recommendation. Names who each option is right for. No hedging. No "it depends" without specifics.]
```

---

### NARRATIVE

```
---
brand:
category:
title:
type: Narrative
word-count-target:
status: Draft
created:
---

# [H1]

[Hook — one sentence. A moment, a problem, a feeling. Not a question. Not "Have you ever..."]

## [The situation]

[Set up the world before this existed. What was the problem? Specific, not abstract.]

## [The turning point]

[What changed? What was decided? Why?]

## [What was built]

[What the brand is, in plain language. Community voice — not a product announcement.]

## [Where we're going]

[Forward-looking. Invites the reader in. Links to platform URL naturally.]
```

---

## GEO COMPLIANCE CHECKLIST

Before saving any Informational or Comparison article:

- [ ] First paragraph answers the core question directly (2–3 sentences)
- [ ] Each paragraph: one idea, 3–4 lines max
- [ ] At least one list or table included
- [ ] H1 matches the target query closely
- [ ] Brand platform linked naturally at least once
- [ ] Clean markdown output — no `[[Obsidian links]]`, no `![[embeds]]`
- [ ] No jargon the target reader wouldn't use

---

## ARTICLE WRITER BRAND CONFIG SPEC

Add this section to any brand's Content Assistant Config to activate /write-article:

```
## ARTICLE WRITER

Platform: [blog URL]
Title list: [path to TitleList-Staging.md]
Articles folder: [path — e.g. Brand - Business/Articles/]
Content log: [path to ContentLog-Staging.md]

Word count targets:
  Informational: [range — e.g. 400–700]
  Comparison: [range — e.g. 600–1000]
  Narrative: [range — e.g. 400–700]

Priority order: [e.g. Comparison first → Informational → Narrative]

Workflow:
  1. Draft → [Brand]/Articles/[slug]-Staging.md
  2. Log in ContentLog ARTICLES section — Status: Draft
  3. Operator approves → Status: Approved
  4. Export clean markdown → upload to [platform] as draft
  5. End client approves → Status: Posted → update ContentLog
```

---

## SLUG FORMAT

Convert title to slug: lowercase, spaces → hyphens, remove punctuation.

Example: `"How Duio is different from other game dev communities"` → `how-duio-is-different-from-other-game-dev-communities`

---

*Write-Article Reference v1.0 | Skill: /write-article*
