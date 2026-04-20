◆ WORKFLOW: AI GAME MAKER — GAME BRIEF ◆
Load via: /game-brief
Layer on top of [[_AI/daily-ai-config]] for all AI Game Maker work.

Source: [[HatchFox Studios - Business/AI Game Maker - Brand Project/Game Brief V3]] | [[HatchFox Studios - Business/AI Game Maker - Brand Project/Game Briefing tool V3 Chat]]

---

PURPOSE

This workflow activates the AI Game Maker briefing protocol. It manages the three-file system architecture, the brief generation process, and the improvement loop. It does not build games — it produces the brief that a build chat uses to build games.

---

01. ARCHITECTURE — THREE-FILE SYSTEM

This is the core design. Never collapse these three files into one.

PROMPT.md
→  AI build instruction. Framework-neutral. No code, no React assumptions.
→  Any AI reads this identically: Claude, GPT-4, Gemini, local models.
→  What gets passed to a new build chat. This chat never returns to a build chat.
→  Opens with: "Do NOT use React, Vue, or any framework unless explicitly stated below."

brief.json
→  Machine-readable spec. Version control.
→  Used only by this brief tool for re-import and module spec updates.
→  Never passed to a build chat directly.

pipeline.zip
→  Human-editable project package. Separated by team role.
→  A traditional game/art team takes this and never touches an AI tool again.
→  AI-free in operation. No AI required to update assets or publish.

CHANGES.md
→  Handoff format from build chat back to this workflow.
→  Structured feedback travels as a file, not as chat history.
→  Pasted back into this workflow to update module specs and trigger escalation.

WORKFLOW CYCLE
Brief tool → Export PROMPT.md + brief.json → DONE (this workflow is done for this project)
Build chat: Paste PROMPT.md → Build → Self-test → Return updated CHANGES.md
Re-open brief workflow → Import CHANGES.md → Update module specs → Export PROMPT-v2.md
New build chat (clean context) → Repeat

---

02. BRIEF GENERATION PROTOCOL

When /game-brief is active, execute brief generation in five sections:

SECTION 01 — CORE SPEC
Generate with these parameters (ask for values or generate defaults):
◈  Complexity: [simple / medium / complex]
◈  Session length: [2min / 5min / 10min / 20min+ / endless]
◈  Innovation dial: [0-100 — 0=familiar mechanics, 100=experimental]
◈  Difficulty: [casual / normal / hard / brutal]
◈  Replay value: [low / medium / high / infinite]
◈  Must include: [list]
◈  Must avoid: [list]

Generate: title, concept, target audience, core loop, win/fail conditions, mechanics, scope, accessibility.

SECTION 02 — CREATIVE SPEC
Generate per area (or bulk generate, or let AI decide all):
◈  Genre, Art style, Camera/view, Palette (visual swatches), Audio mood, UI/UX style
◈  Custom art style gets a description field with example prompt.
◈  Palette displayed as swatch cards with descriptions.

SECTION 03 — PLATFORM SPEC
Web platforms: HTML, JS App, Interactive Web, Interactive Ad
Mobile: Android only / iOS only / Android + iOS
Console/PC: not available (greyed out)
→  Select platform first. Input methods, screen sizes, and performance options auto-populate.

SECTION 04 — QA CRITERIA
This is a spec definition, not a feedback interface.
→  AI self-check panel: 10-point checklist that Claude fills as pass/true or fail/false
→  Human feedback: type tags, star rating, module targeting, running history
→  Approval gate: human gate only unlocks after all AI checks pass

SECTION 05 — MODULE LIBRARY
Every art style, platform, input method, and UI style has its own spec card.
→  Issues and improvements tracked per module
→  An issue with Pixel Art updates only the Pixel Art spec
→  Auto-escalation: same module + 2 human feedback entries → spec patched permanently

---

03. SELF-REPAIR LOOP

This is the core improvement mechanism. It is built into the brief, not this workflow.

RULE: If the same class of issue appears in 2+ revision cycles for the same module, it gets escalated to that module's permanent spec.

HOW IT WORKS IN PRACTICE
1.  Build AI encounters an issue, fixes it, logs it in CHANGES.md
2.  CHANGES.md is imported back into this workflow (via /import-changes)
3.  This workflow identifies the module, increments its version, updates its spec
4.  Next PROMPT.md export includes the updated module spec
5.  Future build chats inherit the fix — the issue doesn't repeat

ESCALATION THRESHOLD: Same module + 2 Bug or UX feedback entries = auto-escalation. Permanent rule added to module spec.

---

04. COMMANDS — GAME BRIEF SESSIONS

→  /new-brief — Start a fresh brief. Walk through all five sections.
→  /gen-core [parameters] — Generate core spec with given parameters.
→  /gen-creative [all|area] — Generate creative spec in bulk or per area.
→  /platform [type] — Set platform. Auto-populate input/screen/performance options.
→  /qa-check — Run AI self-check against current spec.
→  /import-changes [paste CHANGES.md] — Import feedback from build chat. Update modules.
→  /export-prompt — Generate final PROMPT.md for build chat.
→  /export-brief — Generate brief.json for version control.
→  /compile-zip — Generate pipeline.zip with role-separated files.
→  /module [name] — View or update a specific module spec.
→  /version-log — Show full version history for this brief.

---

05. EXPORT FORMATS

PROMPT.md OUTPUT STRUCTURE
```
# GAME BRIEF — [Title] — PROMPT v[X]
## OPERATOR INSTRUCTIONS
Do NOT use React, Vue, or any framework unless explicitly stated below.
This brief is framework-neutral. Build in [specified platform/language].
[claude_instructions block: PLAN → BUILD → SELF-TEST → SELF-REPAIR → RETURN code + updated JSON → HUMAN GATE]

## CORE SPEC
## CREATIVE SPEC
## PLATFORM SPEC
## QA CRITERIA
## MODULE SPECS (accumulated)
## CHANGES LOG (accumulated)
```

PIPELINE.ZIP STRUCTURE
```
index.html          Developer — entry point only
src/game.js         Developer — all logic, reads from config
src/style.css       UI Designer — all styles, CSS variables at top
src/config.json     Game Designer — every tunable value, zero code needed
assets/manifest.json Artist — asset registry, replace by matching names
assets/sprites/      Artist — drop PNG/SVG here
assets/audio/        Audio Designer — drop MP3/OGG here
assets/fonts/        UI Designer — drop WOFF2 here
docs/design-spec.md  All — full art/audio/UI brief
docs/asset-list.md   Artist — every asset with dimensions and use
docs/pipeline-guide.md All — step-by-step: swap assets, change values, publish
brief.json           Dev/AI — reference only for re-entering AI loop
```

---

06. HARD LIMITS — GAME BRIEF SESSIONS

✗  Never build the game in the brief workflow. Brief generation only.
✗  Never pass brief.json directly to a build chat as the prompt.
✗  Never return to the brief workflow from a build chat — CHANGES.md is the handoff.
✗  Never collapse the three-file system into one file.
✗  Never assume a module fix applies to all modules — update the specific module only.

---

END GAME-BRIEF WORKFLOW
Source files: [[HatchFox Studios - Business/AI Game Maker - Brand Project/Game Brief V3]] | [[HatchFox Studios - Business/AI Game Maker - Brand Project/Game Briefing tool V3 Chat]]
