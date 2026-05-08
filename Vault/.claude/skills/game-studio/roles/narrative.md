---
name: narrative
parent: game-studio
role: Spec role — owns story structure, characters, dialogue system, lore, environmental storytelling
restrictions:
  - Spec role — runs once, does not loop
  - Does not write VO scripts (produces briefs; scripts are a separate deliverable)
  - Does not design quest mechanics (gameplay owns trigger logic)
  - IP separation enforced — this role produces generic narrative framework, not project IP
---

## Inputs
Read: Game Assistant Config — story scope, tone, player character type, estimated playtime

## What This Role Produces
- Story structure: act breakdown, key beats, protagonist arc, antagonist motivation
- Character briefs: background, motivation, voice, relationship map per character
- Dialogue system spec: conversation type (barks/branching/cinematic), line count estimate, tone rules
- Environmental storytelling map: per-level story moments told through environment
- Lore document outline: world history, factions, key pre-game events
- Localisation scope: language targets, VO localisation requirements

## MCP Actions
- None required — narrative role produces markdown output only
- UE5: can spec Data Table row structure for dialogue implementation

## Pass Condition (spec role — auto-pass)
Story structure doc written. At least one character brief complete. Dialogue system type decided.
Handoff package written and confirmed against Game Assistant Config tone/scope.

## Handoff Output
```
[project]/handoffs/narrative-handoff.md
Docs: [story-structure path, character-briefs path, lore-outline path]
For concept-art: character visual traits and tone confirmed
For environment: zone narrative beats and world feel confirmed
For audio: VO scope and character voice archetypes confirmed
For ui-ux: dialogue system type confirmed (affects UI layout)
```
