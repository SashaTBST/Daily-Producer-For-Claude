# web-game — Reference

## Three.js vs Babylon.js Decision Criteria

Use this table when the task is 3D but the tech stack is not named.

| Dimension | Three.js | Babylon.js |
|---|---|---|
| Physics | External (Rapier/Matter.js) → route to `/collision-physics` | Built-in (Havok/Cannon/Ammo) → still route to `/collision-physics` for setup |
| Shaders | Full GLSL control via ShaderMaterial → `/shader-programming` for deep work | Higher-level abstractions — `/shader-programming` for custom GLSL |
| TypeScript | Community package | Native first-class support |
| Editor tooling | None built-in | Visual editor + scene inspector included |
| GLTF handling | Manual optimization required | Auto mesh merging + optimization |
| WebGPU | Zero-config since r171 via TSL shaders | Feature-complete, mirrors WebGL API |
| Best for | Creative tech, experimental, React integration, custom pipelines | Production teams, long-lived projects, internal consistency, built-in systems |

**Decision rule:** If in doubt, ask one question — "Does this project need built-in physics, audio, and a visual editor, or does it need maximum control and a lighter bundle?" Built-in systems → Babylon.js. Control → Three.js.

---

## WASM Trigger Conditions

`/webassembly` is a supplement, not a default. Trigger only when ONE of these is true:

1. Operator explicitly states a performance problem ("JS is too slow", "hitting 60fps limit")
2. Task is identified as compute-heavy by type:
   - Pathfinding over large grids (A*, Dijkstra at scale)
   - Particle physics simulating thousands of bodies per frame
   - ML inference or computer vision in-game (AR, procedural generation)
   - Real-time audio synthesis
   - Porting an existing native game engine (Unity/Godot WASM export)
3. Profiling output is provided showing JS as the bottleneck

Do NOT trigger WASM for: game logic, input handling, UI, asset loading, general game loop code.

---

## 2D Routing — Tradeoff Flag

When task is 2D game development, route to `/html5-canvas` AND surface this:

```
Routing to /html5-canvas for canvas-level 2D rendering.
Note: for production 2D games, Phaser (full 2D game engine) or Pixi.js
(fast 2D renderer) would be the standard choice — these are outside the
current skill scope. /html5-canvas covers raw canvas API (game loop,
sprite draw calls, pixel manipulation). Flag if you need framework-level
2D game tooling.
```

---

## Out-of-Scope Framework Redirects

| Framework | Reason out of scope | Redirect |
|---|---|---|
| Phaser | Full 2D game engine — no dedicated skill | Point to Phaser docs; note /html5-canvas for primitives |
| Pixi.js | 2D WebGL renderer — no dedicated skill | Point to Pixi docs; note /html5-canvas for primitives |
| PlayCanvas | Growing but no skill yet | Point to PlayCanvas docs |
| A-Frame | VR/AR focus, not general web games | Out of scope entirely |
| GDevelop / Construct | No-code editors | Out of scope entirely |
| Godot HTML5 export | Engine-level, not browser-native | Out of scope — Godot has its own workflow |

---

## /game-maker Integration

`/game-maker` loads `/web-game` when:
- Config Creation Protocol Q3 answer = web / browser / embed
- Task description references web deployment, browser target, or HTML output
- Platform field in Game Assistant Config = web

Signal pattern from /game-maker:
```
Platform: web — loading /web-game director.
Read .claude/skills/web-game/SKILL.md
```

---

## Security Inherited from Sub-Skills

Each sub-skill carries its own security gate. The director does not add a separate gate. Ensure the correct sub-skill's security gate fires on every output:

- `/webgl` — context null-check, no eval GLSL, CORS on textures
- `/threejs` — dispose on removal, no user URLs in loader, crossOrigin set
- `/babylonjs` — scene dispose, no eval GLSL, asset origin validation
- `/html5-canvas` — no user-controlled canvas operations without sanitisation
- `/collision-physics` — user input clamped, WASM SRI verified
- `/shader-programming` — no eval-constructed GLSL, precision declared
- `/asset-management` — URL allowlist, MIME validated, dispose pattern
- `/webassembly` — SRI on WASM binary, no dynamic instantiation from user input
