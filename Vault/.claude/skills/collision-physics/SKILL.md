---
name: collision-physics
description: Collision detection and physics simulation for games and interactive 3D — AABB, OBB, sphere/capsule colliders, broad phase (BVH, spatial hashing), narrow phase (SAT, GJK/EPA), rigid body dynamics, impulse resolution, and engine integration (Matter.js 2D, Rapier/Havok/Ammo.js 3D, Three.js, Babylon.js). Use when setting up colliders, implementing physics bodies, integrating a physics engine, or auditing simulation correctness and performance.
argument-hint: "[mode: collider|rigid|integrate|review] [description] — add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/collision-physics = collision shapes, broad/narrow phase, rigid body dynamics, physics engine integration (2D + 3D browser).
Fluid simulation, cloth/soft body, character controllers, and vehicle physics are out of scope — flag and redirect to engine-specific docs.

## Non-Negotiable
Validate mesh complexity before running collision on user-provided geometry — degenerate or high-poly meshes cause DoS.
Never trust client-provided physics state in multiplayer — simulate server-side; treat client as intent-only.
Rapier/Havok WASM loaded from CDN: verify subresource integrity hash before use.
Document explicitly when simulation is non-deterministic across platforms — floating point is usually not deterministic.
Clamp all user-supplied velocity, force, and impulse values to reasonable domain ranges — unbounded input breaks simulation.

## Modes

**COLLIDER** — Shape setup and configuration.
Shape selection: sphere (cheapest), capsule (characters), AABB (static boxes), convex hull (complex rigid bodies). Never use trimesh for dynamic bodies — static only.
Broad phase: use engine's built-in BVH or spatial hash — never O(n²) all-pairs for more than ~20 objects.
Layers/masks: configure collision layers to exclude irrelevant pairs (e.g. projectile vs projectile).
NON-DEV: explain why simpler shapes are faster and when each shape type is appropriate.
End with: propose RIGID mode to add physics responses to the colliders.

**RIGID** — Rigid body dynamics, mass, restitution, friction, impulse resolution.
Body types: dynamic (moved by physics), kinematic (moved by code, collides), static (immovable). Choose deliberately.
Properties: mass, linearDamping, angularDamping, restitution (bounciness 0–1), friction (0–1).
Impulse vs force: impulse is instant (collision response); force is continuous (gravity, thrust). Never apply impulse every frame.
Sleep threshold: always enable body sleep for inactive objects — prevents wasted simulation on still objects.
NON-DEV: explain impulse resolution in plain English (two objects exchange momentum on contact).
End with: propose INTEGRATE mode to wire physics to the render engine.

**INTEGRATE** — Engine integration with Three.js or Babylon.js.
Three.js + Rapier: create `World`, add `RigidBody` + `Collider`, each frame: `world.step()` → copy `body.translation()` → `mesh.position`.
Babylon.js + Havok: `PhysicsAggregate` handles the sync automatically — document which properties are auto-synced vs manual.
Fixed timestep: run physics at fixed `dt` (e.g. 1/60), render at display rate — decouple with accumulator pattern.
Debug renderer: enable physics debug view during dev; disable in production.
NON-DEV: explain why physics runs at its own timestep and what tunnelling is (fast objects passing through thin walls).
End with: propose REVIEW mode when integration is working.

**REVIEW** — Correctness + performance audit. Report each item: PASS / WARN / FAIL.
Flags: trimesh on dynamic body, no broad phase, unbounded user velocity/force input, WASM without SRI, non-determinism not documented, client physics state trusted in multiplayer, body sleep disabled, debug renderer in production.
End with: propose `/qa` when clean.

## Security Gate
Every COLLIDER, RIGID, and INTEGRATE response ends with this line — never skip it:
`Security pass: ✓ user input clamped ✓ mesh complexity validated ✓ WASM SRI verified ✓ multiplayer state server-authoritative ✓ non-determinism documented`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/threejs` — Three.js scene integration for physics bodies
- `/babylonjs` — Babylon.js Havok physics integration
- `/webassembly` — Rapier/Havok WASM loading and integrity
- `/qa` — propose after REVIEW passes clean

## Pipeline Connections
COLLIDER complete → propose RIGID to add physics response
RIGID complete → propose INTEGRATE to wire to render engine
INTEGRATE complete → propose REVIEW
REVIEW clean → propose `/qa`
/qa passes → portable sync + commit

## Anti-patterns
✗ Never use trimesh colliders on dynamic bodies
✗ Never run O(n²) all-pairs collision checks beyond ~20 objects
✗ Never trust client-provided physics state in multiplayer
✗ Never load Rapier/Havok WASM without SRI verification
✗ Never apply impulse every frame — use force for continuous effects
✗ Never pass unbounded user velocity or force values to the simulation
✗ Never leave physics debug renderer enabled in production
✗ Never skip the security gate line on COLLIDER, RIGID, or INTEGRATE output


Every response ends with NEXT MOVE.