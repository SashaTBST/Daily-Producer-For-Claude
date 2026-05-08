---
name: babylonjs
description: Security-first Babylon.js 7.x builder covering Engine, Scene, Camera, Mesh, Materials (Standard/PBR/Node), Lights, Havok physics, Animation, GUI overlays, AssetManager, and Inspector. Use when building browser-based 3D scenes, integrating Havok physics, composing BabylonGUI overlays, or reviewing a Babylon.js scene for performance and security issues.
argument-hint: "[mode: scene|physics|gui|review] [description] â€” add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/babylonjs = Babylon.js 7.x, TypeScript-first, browser runtime.
Node.js server-side rendering, native mobile, and Babylon Native are out of scope.
/react or /typescript for surrounding app scaffolding.

## Non-Negotiable
Always call `engine.dispose()` and `scene.dispose()` on unmount â€” never leak engine or scene instances.
Never load `.babylon` or `.glb` files from unvalidated user-supplied URLs â€” validate and allowlist origins first.
Havok WASM plugin: validate integrity (SRI hash or known CDN tag) before loading â€” WASM execution is privileged.
Never enable Inspector (`scene.debugLayer.show()`) in production builds â€” leaks full scene graph to client.
Cross-origin assets: configure CORS on the asset server; never bypass with wildcard origins on sensitive assets.
Never use deprecated `BABYLON.Mesh` direct constructor for primitives â€” use `MeshBuilder` exclusively.

## Modes

**SCENE** â€” Engine + Scene setup, cameras, meshes, materials, lights.
Boilerplate: `Engine` â†’ `Scene` â†’ attach camera â†’ add lights â†’ build meshes via `MeshBuilder`.
Camera defaults: `ArcRotateCamera` for orbit scenes, `FreeCamera` for FPS-style. Always call `camera.attachControl(canvas, true)`.
Materials: `StandardMaterial` for simple diffuse/specular; `PBRMaterial` for physically-based; `NodeMaterial` for shader graph.
Lights: `HemisphericLight` as ambient baseline; `DirectionalLight` or `PointLight` for shadows.
NON-DEV: explain scene graph parent/child hierarchy on first use.
End with: propose PHYSICS mode if collision or gravity needed.

**PHYSICS** â€” Havok plugin integration, aggregates, collision events.
Plugin init: `HavokPhysics()` â†’ `new HavokPlugin(true, havokInstance)` â†’ `scene.enablePhysics(gravity, plugin)`.
Bodies: `PhysicsAggregate` (7.x preferred over legacy `PhysicsImpostor`) â€” set shape, mass, restitution, friction.
Collision events: `aggregate.body.setCollisionCallbackEnabled(true)` â†’ observe via `scene.onAfterPhysicsObservable`.
WASM integrity: document the CDN version tag in a comment alongside the import â€” flag if loading from an unversioned URL.
NON-DEV: explain why WASM requires integrity validation (arbitrary code execution risk).
End with: propose GUI mode for HUD/overlay layer.

**GUI** â€” AdvancedDynamicTexture, controls, fullscreen and mesh-attached overlays.
Fullscreen: `AdvancedDynamicTexture.CreateFullscreenUI("UI")`.
Mesh-attached: `AdvancedDynamicTexture.CreateForMesh(mesh)` â€” use for world-space labels.
Controls: `Button`, `TextBlock`, `InputText`, `Slider`, `Image`. Set `control.isPointerBlocker` intentionally.
Sanitize any user-supplied text before setting `.text` â€” no raw HTML injection path in BabylonGUI, but log intent.
End with: propose REVIEW mode when scene + GUI are assembled.

**REVIEW** â€” Performance + security audit. Report each item: PASS / WARN / FAIL.
Flags: engine/scene not disposed, Inspector enabled in prod, unvalidated asset URLs, Havok loaded without version lock, texture memory not released, `registerBeforeRender` with heavy computation, draw call count not monitored.
End with: propose `/qa` when clean.

## Security Gate
Every SCENE, PHYSICS, and GUI response ends with this line â€” never skip it:
`Security pass: âœ“ engine/scene dispose on unmount âœ“ no unvalidated asset URLs âœ“ Inspector disabled in prod âœ“ Havok integrity documented âœ“ CORS configured`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/typescript` â€” type-safe wrappers around Babylon.js classes
- `/react` â€” mounting Babylon canvas inside a React component lifecycle
- `/shader-programming` â€” custom GLSL beyond NodeMaterial
- `/qa` â€” propose after REVIEW passes clean

## Pipeline Connections
SCENE complete â†’ propose PHYSICS if dynamics needed
PHYSICS complete â†’ propose GUI for HUD layer
GUI complete â†’ propose REVIEW
REVIEW clean â†’ propose `/qa`
/qa passes â†’ portable sync + commit

## Anti-patterns
âœ— Never call `new BABYLON.Mesh()` directly for primitives â€” use MeshBuilder
âœ— Never load assets from user-supplied URLs without origin validation
âœ— Never enable Inspector (`debugLayer.show()`) in production
âœ— Never load Havok WASM from an unversioned or unverified URL
âœ— Never skip `engine.dispose()` / `scene.dispose()` on component unmount
âœ— Never set wildcard CORS on asset servers serving sensitive content
âœ— Never leave `registerBeforeRender` callbacks with heavy sync work
âœ— Never skip the security gate line on SCENE, PHYSICS, or GUI output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.