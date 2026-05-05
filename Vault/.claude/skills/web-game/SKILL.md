---
name: web-game
description: Browser/web game development director — routes to the right primitive skill based on rendering dimension, scale, and task layer. Use when building a game for browser embed, full-page web, or any web-specific target. Invoked by /game-maker when platform = browser/web, or called standalone.
argument-hint: "[task description] — name the tech stack if known (e.g. 'Three.js scene setup', 'add physics', 'load GLTF assets')"
---

## Role

Platform routing layer for browser game development. Infers the correct primitive skill from task context. Does not generate game code — delegates to the skill that owns that layer.

IP separation applies: no game IP (characters, lore, world) in routing decisions.

## Invocation

- From `/game-maker` when platform = browser / web / embed
- Standalone: `/web-game [task description]`
- From a game-specific agent config when platform = web

## Routing

If the task names a tech stack explicitly (Three.js, Babylon.js, WebGL, etc.) → skip routing, go direct to that skill.

Otherwise infer from task context:

| Task | Route |
|---|---|
| 3D scene — creative, experimental, React/TS, custom shaders | `/threejs` |
| 3D scene — production scale, built-in systems, team workflow | `/babylonjs` |
| 2D game, canvas-level rendering | `/html5-canvas` + flag Phaser/Pixi tradeoff |
| Raw GPU pipeline, custom engine, max performance | `/webgl` (specialist — last resort only) |
| Physics, collision shapes, rigid bodies | `/collision-physics` |
| Deep GLSL shader authoring | `/shader-programming` |
| GLTF / texture / audio loading, streaming, LOD | `/asset-management` |
| Compute-heavy logic or explicit JS perf bottleneck | `/webassembly` (supplement — not default) |
| UI animation, character animation, scroll effects, motion path | `/animate` |

Multi-layer tasks: list all relevant skills in priority order. Start with the scene/render layer, then physics, then assets, then shaders.

For Three.js vs Babylon.js decision criteria → see REFERENCE.md.
For WASM trigger conditions → see REFERENCE.md.

## Out of Scope

Phaser, Pixi.js, no-code editors (GDevelop, Construct), VR-only frameworks (A-Frame), server-side rendering, native mobile. Flag and redirect — do not attempt to route these.

## Pipeline

Director signals route → skill executes → skill proposes next layer → user re-invokes `/web-game` for next task or continues in the sub-skill.

End every response with NEXT MOVE proposing the next layer.

## Anti-patterns

✗ Never route to `/webgl` by default — specialist fallback only
✗ Never skip `/collision-physics` for Babylon.js — it owns the physics layer regardless of render engine
✗ Never ask routing questions when the tech stack is already named in the task
✗ Never trigger `/webassembly` without a stated perf bottleneck or compute-heavy task type
✗ Never attempt to route 2D framework tasks (Phaser, Pixi.js) — flag out of scope
✗ Never generate game code directly — route to the owning skill


Every response ends with NEXT MOVE.