Long-form SEO article writer — generates blog articles from a title and brand config using GEO-optimised structure. Use when writing blog articles for any brand. Requires an ARTICLE WRITER section in the brand's Content Assistant Config.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

## Architecture

```
LAYER 1 — THIS SKILL     Generic: GEO structure, article types, pipeline, format rules. No brand IP.
LAYER 2 — Brand Config   ARTICLE WRITER section in [Brand] Content Assistant Config.
LAYER 3 — Brand Strategy Brand positioning and pillars. Read on demand for article context.
```

## Session Start

1. Load brand Content Assistant Config — find ARTICLE WRITER section.
   - Missing → flag: "No ARTICLE WRITER config for [brand]. Add the section to [Brand] Content Assistant Config."
2. Load brand title list (if listed in config) — check status, find next priority.
3. Report: next unwritten HIGH priority article + any drafts in progress.

## Article Types

| Type | When | Structure |
|------|------|-----------|
| Informational | Explains concept, process, or feature | GEO — answer-first |
| Comparison | Compares brand to competitors or alternatives | GEO + comparison table |
| Narrative | Brand story, origin, vision | Story arc — no answer-first |

Determine type from: title list Type column → or infer from title if unlisted.

## GEO Rules (Informational + Comparison only)

→ Answer the question in the first 2–3 sentences. No preamble.
→ One idea per paragraph. Max 3–4 lines per paragraph.
→ Use H2s per section. H3s for sub-points.
→ Include at least one list or table per article.
→ Output clean markdown — no Obsidian `[[links]]`. Ready to paste into CMS.
→ Final section: link naturally back to the brand platform.

## Pipeline

```
Title selected → determine type → draft using correct template
→ Save: [Brand]/Articles/[slug]-Staging.md
→ Log in ContentLog ARTICLES section — Status: Draft
→ Operator reviews → Status: Approved
→ Export clean markdown → upload to platform as draft
→ End client approves → Status: Posted → ContentLog updated
```

Slug format: lowercase, spaces → hyphens, punctuation removed.

## Commands

- `/write-article [brand] [title]` — write article from title
- `/write-article [brand] next` — write next HIGH priority article from title list
- `/write-article [brand] list` — show title list with status
- `/write-article [brand] draft [slug]` — continue or revise an existing draft

Every response ends with NEXT MOVE.

---

## REFERENCE

### Informational Template
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

[Answer paragraph — 2–3 sentences. Directly answers the core question. No preamble.]

## [H2 — first major point]
[Content — 3–4 lines max per paragraph. One idea per paragraph.]

## [H2 — second major point]
[Content]

## [H2 — third major point]
[Content — include a list or numbered steps here]

## [Natural brand mention]
[One paragraph. Brand as the natural answer or context. Links to platform URL.]
```

### Comparison Template
Use Informational template, then add:
```
## What is [X]?
[2–3 sentences. Definition only.]

## What is [Y]?
[2–3 sentences. Definition only.]

## [X] vs [Y] — Key Differences
| Feature | [X] | [Y] |
|---------|-----|-----|
| [Feature 1] | [value] | [value] |
| Best for | [value] | [value] |

## Which should you choose?
[Direct recommendation. Who each option is right for. No hedging without specifics.]
```

### Narrative Template
```
# [H1]
[Hook — one sentence. A moment, a problem, a feeling. Not a question.]

## [The situation]
[World before this existed. What was the problem? Specific, not abstract.]

## [The turning point]
[What changed? What was decided? Why?]

## [What was built]
[What the brand is, in plain language. Community voice — not a product announcement.]

## [Where we're going]
[Forward-looking. Invites the reader in. Links to platform URL naturally.]
```

### GEO Compliance Checklist
- [ ] First paragraph answers the core question directly (2–3 sentences)
- [ ] Each paragraph: one idea, 3–4 lines max
- [ ] At least one list or table included
- [ ] H1 matches the target query closely
- [ ] Brand platform linked naturally at least once
- [ ] Clean markdown output — no `[[Obsidian links]]`
- [ ] No jargon the target reader wouldn't use

### ARTICLE WRITER Config Spec
Add to any brand's Content Assistant Config to activate /write-article:
```
## ARTICLE WRITER
Platform: [blog URL]
Title list: [path to TitleList-Staging.md]
Articles folder: [path]
Content log: [path to ContentLog-Staging.md]
Word count targets:
  Informational: [range]
  Comparison: [range]
  Narrative: [range]
Priority order: [e.g. Comparison first → Informational → Narrative]
```
