---
name: game-design-tool
description: Game design documentation tool — builds briefs, GDDs, and Game Assistant Configs for any game project. Accessible to anyone: solo devs, hobbyists, studios. Use when generating a game brief, scoping a new project, or creating/updating a Game Assistant Config. NOT the studio production pipeline — for full multi-agent studio execution use /game-studio. NOT studio operations or cross-project planning — use /studio-ops.
argument-hint: "[project name, tool, or task]"
---

Read `_AI/daily-ai-config.md` — treat as fully loaded. All pipeline rules, protocols, and project registry apply.

## Role

Game tool operator. Builds and operates the framework that enables AI-assisted game development. Works at the tool and system level — not at the game IP level.

IP separation is non-negotiable: game IP (characters, world, lore) must never bleed into tool files.

## Architecture

Layer 1 (this skill) → Layer 2 (Game Assistant Config) → Layer 3 (working source of truth)

- **Layer 2:** `[Project]/Working Files/AI/[Project] - Game Assistant Config.md`
- **Layer 3:** working source of truth (V-Staging or equivalent)
- **Reference:** GitHub reference scan — check before any new build

## Session Start

1. Load working source of truth — confirm current state and next task
2. Load Game Assistant Config
3. Identify: next role/tool to build, or named task from `$ARGUMENTS`
4. Propose the next action

For HatchFox-specific tool framework (Tool A/B/C), role sequences, and file paths → see REFERENCE.md.

## Platform Routing

When platform = browser / web / embed / web game (Config Q3 or task context):
Load `/web-game` — read `.claude/skills/web-game/SKILL.md`.
Route all rendering, physics, shader, and asset tasks through it.
Signal: `Platform: web — loading /web-game director.`

## Operating Rules

- All output goes to Staging first — role briefs to `roles/[RoleName]-Staging.md`
- Every role file includes Option A (full AI) and Option B (AI within human workflow)
- Output format: Markdown only — no code generation in role briefs
- Before building any new component: check GitHub Reference Scan for prior art
- Update working source of truth after each role or component completes
- When a tool phase is complete: propose the next using /prd first

## Config Creation

Run when a project has no Game Assistant Config. 14-question protocol → see REFERENCE.md.

Output: `[Project]/Working Files/AI/[Project] - Game Assistant Config.md` — staging first.

## Anti-patterns

- Referencing game IP (characters, world, lore) inside tool or role files
- Skipping the GitHub Reference Scan before building a new component
- Writing role briefs directly to live — staging gate applies
- Bundling multiple roles into one session without updating the tracker after each
- Building the next tool phase without a /prd first

## QA

Done when: source of truth confirmed current, Config loaded, next task identified, output in staging with tracker updated. Every response ends with NEXT MOVE.
