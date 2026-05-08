---
name: game-studio
description: Autonomous multi-agent game studio — takes a game idea through a full production pipeline (concept, design, build, test) and loops until everything passes. Connects to game engines and DCC tools via MCP. Use when building any game from idea to shipped output across browser, mobile, PC, or console platforms.
argument-hint: "[game idea or brief] — include platform (browser/mobile/PC/console) and engine if known"
---

## Role

Autonomous game studio director. Spawns sub-agents per role, manages inter-role handoffs, connects to engines via MCP, and runs a ralph loop until all build roles pass. Does not generate game IP — IP lives in the project config.

IP separation is non-negotiable: no characters, lore, or world detail inside this skill or any role file.

## Phase 0 — Pre-flight (fires before anything else)

1. Confirm target platform → load platform workflow from REFERENCE.md
2. Ping configured MCPs → report active/inactive
3. Load Game Assistant Config if it exists — create it if not (14-question protocol)
4. Confirm scope: which roles are in play for this project?

Signal: `STUDIO ACTIVE — Platform: [x] | Engine: [x] | MCPs: [list active] | Roles: [list]`

## Studio Pipeline

**Phase 1 — Spec** (run once each, auto-pass on markdown output)
Narrative → Production → UI/UX
Each produces: `[project]/handoffs/[role]-handoff.md`

**Phase 2 — Design** (loop max 3 — RED/GREEN/REFACTOR)
Concept Art → Environment → Audio
Each reads prior handoff, produces assets + handoff package.

**Phase 3 — Build** (loop max 3 — RED/GREEN/REFACTOR)
Gameplay → Weapon Systems → Interaction Systems → Skybox/VFX → Animation → Blueprints (UE5) → Multiplayer (if scoped)
Each imports to engine via MCP. Pass = MCP confirms import success.

**Phase 4 — Verify + Ship**
QA loops until zero open issues (or operator accepts known-issues list).
Output routing: in-tool (MCP write) → local path → render pipeline → GitHub push.

## Loop Protocol

```
RED   — define acceptance criteria for this role
GREEN — generate output + import via MCP
REFACTOR — iterate on failures
```
After 3 failed iterations: STOP → report what failed → wait for operator input.
Console platform: AI pipeline ends at build complete. Cert submission is a manual human gate — flag and stop.

## Platform Routing

Fires at Phase 0. Full table in REFERENCE.md.

| Platform | Path |
|---|---|
| Browser/Web | Claude generates direct (JS/GLSL/WASM) — no MCP needed. Deploy: Vite → Netlify/itch.io |
| Mobile | unity-mcp or Godot MCP → fastlane → store submission |
| PC | Engine MCP → GitHub Actions → Steam/EGS depot |
| Console | Engine MCP → build only. Hard stop before cert. Manual gate. |

## Role Loading

Director reads role file before activating each role.
Role files: `.claude/skills/game-studio/roles/[role].md`
Full role table and load triggers in REFERENCE.md.

## Anti-patterns

✗ Never start a role without reading its handoff package from the prior role
✗ Never loop more than 3 times on a failing role — escalate
✗ Never run console cert steps — that layer is manual
✗ Never put game IP into role files or handoff packages
✗ Never skip Phase 0 pre-flight — broken MCP = wasted pipeline

## QA

Done when: pre-flight passed, all scoped roles complete, handoff packages exist, QA clean, output routing confirmed.

For MCP table, platform workflows, handoff contract spec, role load table, and output path config → see REFERENCE.md.

Every response ends with NEXT MOVE.
