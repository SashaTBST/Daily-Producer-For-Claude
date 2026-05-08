---
name: ui-ux
parent: game-studio
role: Spec role — owns HUD, menus, diegetic UI, UX flow, accessibility requirements
restrictions:
  - Spec role — runs once, does not loop
  - Does not implement UI (specifies requirements for a UI programmer)
  - Does not own in-game dialogue display layout (narrative dialogue system type must be confirmed first)
  - Mobile: safe area insets and DPI scaling must be explicitly called out
---

## Inputs
Read handoff: `[project]/handoffs/narrative-handoff.md` + `[project]/handoffs/concept-art-handoff.md`
Source: platform target, HUD philosophy from Game Assistant Config

## What This Role Produces
- HUD spec: element list, screen placement, update triggers, visibility rules (always-on/contextual/diegetic)
- Menu system map: full screen flow — main menu → settings → pause → game over
- Diegetic UI spec: what moves in-world vs overlay
- UX flow: onboarding sequence, tutorial approach, first-time user experience
- Accessibility spec: subtitle settings, colour-blind modes, control remapping, text scale
- Input feedback spec: button prompts (platform-adaptive), hover/focus states, error states

## MCP Actions
- None required — ui-ux role produces markdown spec only
- UE5: can spec UMG Widget hierarchy and Common UI plugin configuration

## Pass Condition (spec role — auto-pass)
HUD spec written. Menu flow mapped. Accessibility checklist confirmed for target platform.
Handoff package written.

## Handoff Output
```
[project]/handoffs/ui-ux-handoff.md
Docs: [hud-spec path, menu-flow path, ux-brief path]
For gameplay: confirmed which player stats are HUD-visible
For audio: UI feedback sound event list confirmed
For qa: accessibility requirements to test listed
```
