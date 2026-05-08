---
name: babylonjs
description: Security-first Babylon.js 7.x builder covering Engine, Scene, Camera, Mesh, Materials (Standard/PBR/Node), Lights, Havok physics, Animation, GUI overlays, AssetManager, and Inspector. Use when building browser-based 3D scenes, integrating Havok physics, composing BabylonGUI overlays, or reviewing a Babylon.js scene for performance and security issues.
argument-hint: "[mode: scene|physics|gui|review] [description] — add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/babylonjs = Babylon.js 7.x, TypeScript-first, browser runtime.
Node.js server-side rendering, native mobile, and Babylon Native are out of scope.
/react or /typescript for surrounding app scaffolding.

## Non-Negotiable
Always call `engine.dispose()` and `scene.dispose()` on unmount — never leak engine or scene instances.
Never load `.babylon` or `.glb` files from unvalidated user-supplied URLs — validate and allowlist origins first.
Havok WASM plugin: validate integrity (SRI hash or known CDN tag) before loading — WASM execution is privileged.
Never enable Inspector (`scene.debugLayer.show()`) in production builds — leaks full scene graph to client.
Cross-origin assets: configure CORS on the asset server; never bypass with wildcard origins on sensitive assets.
Never use deprecated `BABYLON.Mesh` direct constructor for primitives — use `MeshBuilder` exclusively.

## Modes

**SCENE** — Engine + Scene setup, cameras, meshes, materials, lights.
Boilerplate: `Engine` → `Scene` → attach camera → add lights → build meshes via `MeshBuilder`.
Camera defaults: `ArcRotateCamera` for orbit scenes, `FreeCamera` for FPS-style. Always call `camera.attachControl(canvas, true)`.
Materials: `StandardMaterial` for simple diffuse/specular; `PBRMaterial` for physically-based; `NodeMaterial` for shader graph.
Lights: `HemisphericLight` as ambient baseline; `DirectionalLight` or `PointLight` for shadows.
NON-DEV: explain scene graph parent/child hierarchy on first use.
End with: propose PHYSICS mode if collision or gravity needed.

**PHYSICS** — Havok plugin integration, aggregates, collision events.
Plugin init: `HavokPhysics()` → `new HavokPlugin(true, havokInstance)` → `scene.enablePhysics(gravity, plugin)`.
Bodies: `PhysicsAggregate` (7.x preferred over legacy `PhysicsImpostor`) — set shape, mass, restitution, friction.
Collision events: `aggregate.body.setCollisionCallbackEnabled(true)` → observe via `scene.onAfterPhysicsObservable`.
WASM integrity: document the CDN version tag in a comment alongside the import — flag if loading from an unversioned URL.
NON-DEV: explain why WASM requires integrity validation (arbitrary code execution risk).
End with: propose GUI mode for HUD/overlay layer.

**GUI** — AdvancedDynamicTexture, controls, fullscreen and mesh-attached overlays.
Fullscreen: `AdvancedDynamicTexture.CreateFullscreenUI("UI")`.
Mesh-attached: `AdvancedDynamicTexture.CreateForMesh(mesh)` — use for world-space labels.
Controls: `Button`, `TextBlock`, `InputText`, `Slider`, `Image`. Set `control.isPointerBlocker` intentionally.
Sanitize any user-supplied text before setting `.text` — no raw HTML injection path in BabylonGUI, but log intent.
End with: propose REVIEW mode when scene + GUI are assembled.

**REVIEW** — Performance + security audit. Report each item: PASS / WARN / FAIL.
Flags: engine/scene not disposed, Inspector enabled in prod, unvalidated asset URLs, Havok loaded without version lock, texture memory not released, `registerBeforeRender` with heavy computation, draw call count not monitored.
End with: propose `/qa` when clean.

## Security Gate
Every SCENE, PHYSICS, and GUI response ends with this line — never skip it:
`Security pass: ✓ engine/scene dispose on unmount ✓ no unvalidated asset URLs ✓ Inspector disabled in prod ✓ Havok integrity documented ✓ CORS configured`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/typescript` — type-safe wrappers around Babylon.js classes
- `/react` — mounting Babylon canvas inside a React component lifecycle
- `/shader-programming` — custom GLSL beyond NodeMaterial
- `/qa` — propose after REVIEW passes clean

## Pipeline Connections
SCENE complete → propose PHYSICS if dynamics needed
PHYSICS complete → propose GUI for HUD layer
GUI complete → propose REVIEW
REVIEW clean → propose `/qa`
/qa passes → portable sync + commit

## Anti-patterns
✗ Never call `new BABYLON.Mesh()` directly for primitives — use MeshBuilder
✗ Never load assets from user-supplied URLs without origin validation
✗ Never enable Inspector (`debugLayer.show()`) in production
✗ Never load Havok WASM from an unversioned or unverified URL
✗ Never skip `engine.dispose()` / `scene.dispose()` on component unmount
✗ Never set wildcard CORS on asset servers serving sensitive content
✗ Never leave `registerBeforeRender` callbacks with heavy sync work
✗ Never skip the security gate line on SCENE, PHYSICS, or GUI output
