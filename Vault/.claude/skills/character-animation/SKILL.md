---
name: character-animation
description: Character animation builder for web — CSS sprite sheet walk cycles, GSAP body-part rigs, and Rive interactive state-machine characters. Covers the full pipeline from sprite setup to interactive runtime control. Use when animating mascots, UI characters, interactive avatars, or game HUD characters on web.
argument-hint: "[mode: sprite|rig|review|debug] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
Three approaches — this skill covers all:
- **CSS sprite sheets** — library-free walk cycles and frame animations via `steps()`
- **GSAP body-part rig** — hierarchical DOM/SVG elements animated as character parts
- **Rive** — interactive characters with state machines, used by Spotify, Disney, Google
Not for: Lottie (use /lottie — covers UI micro-interactions and vector loops). 3D character animation. Spine/DragonBones game engine rigs (reference only — see REFERENCE.md).
Accessibility: always check `prefers-reduced-motion` before running any character animation.

## Modes

**SPRITE** — CSS sprite sheet frame animation.
Sprite sheet: single image containing all frames in a grid. PNG format, max 8192×8192px (mobile GPUs may cap lower).
Frame count N: `sheet-width / frame-width` (horizontal) × `sheet-height / frame-height` (vertical).
`animation-timing-function: steps(N, end)` — `end` steps at interval end (standard). `start` steps at interval start (causes off-by-one appearance). Never use `ease` or `linear` on sprite animations.
`background-size`: set to `N × frame-width` (total sheet width). `background-position` animates from `0 0` to `-totalWidth 0` via `@keyframes`.
Pixel bleeding gotcha: add 1–2px padding between frames in the sprite sheet to prevent adjacent frame bleed at non-integer scales.
`prefers-reduced-motion`: freeze on frame 0 (`animation: none`) for users who prefer reduced motion.
NON-DEV: explain the "film strip" mental model before writing code.
End with: propose `/review` for performance audit.

**RIG** — Body-part character rigs (GSAP or Rive).
**GSAP rig:** Separate DOM/SVG elements per body part. Parent transforms stack — rotating a parent also rotates all children. Set `transform-origin` (or `transform-box: fill-box` for SVG) on every part before animating. Use GSAP timeline for walk cycles: `tl.to('.arm-left', { rotation: 30 }).to('.arm-right', { rotation: -30 }, '<')`.
**Rive (primary for interactive characters):** `.riv` file from Rive editor. Loads via `@rive-app/canvas` or `@rive-app/react`.
  State machines vs animations are distinct: `rive.load({ stateMachines: 'MachineName' })` ≠ `rive.load({ animations: 'AnimationName' })`. Wrong call = silent fail.
  Inputs: Boolean (on/off), Trigger (fires once via `.fire()`), Number (float). Input names must match exactly what's in the Rive editor — case-sensitive, silent fail on mismatch.
  React: `useRive()` hook from `@rive-app/react`. Returns `{ rive, RiveComponent }`.
**Spine (game context reference):** `spine-pixi-v8` for PixiJS v8 + WebGPU. `spine-webgl` for standalone. Version must match — runtime/data version mismatch = broken playback. Reference only — route to game engine skill for full Spine integration.
`prefers-reduced-motion`: pause Rive state machine (`rive.pause()`), freeze GSAP timeline.
NON-DEV: explain the approach choice (GSAP vs Rive) before writing code.
End with: propose `/review` for performance audit.

**REVIEW** — Performance and accessibility audit. Report each: PASS / WARN / FAIL.
Check: (1) sprite sheet size ≤ 8192×8192, (2) steps(N, end) not ease/linear, (3) pixel bleeding padding, (4) prefers-reduced-motion handled, (5) Rive input names verified against editor, (6) GSAP transform-origin set on all parts, (7) user-uploaded .riv security, (8) Spine version match.
Full checklist in REFERENCE.md.
End with: propose `/qa` when clean.

**DEBUG** — Structured diagnosis.
4 intake questions:
  1. What should the character do that isn't happening — or what looks wrong?
  2. CSS sprite, GSAP rig, or Rive? If Rive: state machine or animation?
  3. Does it work at all, or does nothing appear?
  4. Any console errors? (paste them)
Common causes in REFERENCE.md.

## Security Gate
Every RIG response (Rive) ends with this line — never skip it:
`Security pass: ✓ .riv file is from trusted source (not user-uploaded) ✓ Rive state machine inputs are hardcoded (not user-supplied strings) ✓ prefers-reduced-motion handled ✓ no user data passed to animation properties`
Flag user-uploaded `.riv` files immediately — state machine logic is executable. Stop before rendering.

## Cross-Skill Integration
- `/css` — @keyframes, steps(), background-position for sprite sheets
- `/gsap` — timeline sequencing for GSAP body-part rigs; ctx.revert() cleanup
- `/svg-animation` — SVG character parts; transform-box: fill-box applies
- `/react` — useRive() hook, component context
- `/javascript` — Rive vanilla JS API, sprite sheet DOM setup
- `/lottie` — route for UI micro-interactions and vector loops (not skeletal rigs)

## Pipeline Connections
SPRITE complete → propose `/review`
RIG complete → propose `/review`
REVIEW clean → propose `/qa`

## Anti-patterns
✗ Never use `ease` or `linear` timing on sprite sheet animations — use `steps(N, end)` only
✗ Never omit `steps()` N count — wrong frame count breaks the animation cycle
✗ Never render user-uploaded `.riv` files without validation — state machine logic is executable
✗ Never mismatch Rive state machine input names — silent fail, no error thrown
✗ Never call `rive.load({ animations: ... })` when the file uses a state machine — silent fail
✗ Never skip `transform-origin` on GSAP rig parts — parent rotation displaces children unexpectedly
✗ Never exceed 8192×8192 sprite sheet — breaks on mobile GPUs
✗ Never autoplay character animation without `prefers-reduced-motion` check
✗ Never use Spine in a non-game web context without a clear performance budget
✗ Never skip the security gate line on RIG (Rive) output


Every response ends with NEXT MOVE.