# /game-brief — Reference

## Brief Generation Protocol — 5 Sections

**Section 01 — Core Spec**
Parameters: complexity (simple/medium/complex), session length, innovation dial (0–100), difficulty, replay value, must include, must avoid.
Generate: title, concept, target audience, core loop, win/fail conditions, mechanics, scope, accessibility.

**Section 02 — Creative Spec**
Per area (or bulk/AI decides): genre, art style, camera/view, palette (visual swatches), audio mood, UI/UX style.
Custom art style gets a description field with example prompt. Palette displayed as swatch cards.

**Section 03 — Platform Spec**
Web: HTML, JS App, Interactive Web, Interactive Ad.
Mobile: Android only / iOS only / Android + iOS.
Console/PC: not available (greyed out).
Select platform first — inputs, screen sizes, and performance options auto-populate.

**Section 04 — QA Criteria**
AI self-check panel: 10-point checklist (pass/fail).
Human feedback: type tags, star rating, module targeting, running history.
Approval gate: human gate only unlocks after all AI checks pass.

**Section 05 — Module Library**
Every art style, platform, input method, and UI style has its own spec card.
Issues and improvements tracked per module. Auto-escalation: same module + 2 human feedback entries → spec patched permanently.

## Self-Repair Loop

Same class of issue in 2+ revision cycles for same module → escalates to permanent spec.
1. Build AI fixes issue, logs in CHANGES.md
2. CHANGES.md imported via `/import-changes`
3. Skill identifies module, increments version, updates spec
4. Next PROMPT.md export includes updated module spec

## PROMPT.md Structure
```
# GAME BRIEF — [Title] — PROMPT v[X]
## OPERATOR INSTRUCTIONS
## CORE SPEC
## CREATIVE SPEC
## PLATFORM SPEC
## QA CRITERIA
## MODULE SPECS (accumulated)
## CHANGES LOG (accumulated)
```

## PIPELINE.ZIP Structure
```
index.html            Developer — entry point only
src/game.js           Developer — all logic, reads from config
src/style.css         UI Designer — all styles
src/config.json       Game Designer — every tunable value, zero code needed
assets/manifest.json  Artist — asset registry
assets/sprites/       Artist
assets/audio/         Audio Designer
assets/fonts/         UI Designer
docs/design-spec.md   All — full art/audio/UI brief
docs/asset-list.md    Artist
docs/pipeline-guide.md All — step-by-step guide
brief.json            Dev/AI — reference only
```
