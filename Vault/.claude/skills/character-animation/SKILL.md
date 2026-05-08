---
name: character-animation
description: Character animation builder for web â€” CSS sprite sheet walk cycles, GSAP body-part rigs, and Rive interactive state-machine characters. Covers the full pipeline from sprite setup to interactive runtime control. Use when animating mascots, UI characters, interactive avatars, or game HUD characters on web.
argument-hint: "[mode: sprite|rig|review|debug] [description] â€” add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
Three approaches â€” this skill covers all:
- **CSS sprite sheets** â€” library-free walk cycles and frame animations via `steps()`
- **GSAP body-part rig** â€” hierarchical DOM/SVG elements animated as character parts
- **Rive** â€” interactive characters with state machines, used by Spotify, Disney, Google
Not for: Lottie (use /lottie â€” covers UI micro-interactions and vector loops). 3D character animation. Spine/DragonBones game engine rigs (reference only â€” see REFERENCE.md).
Accessibility: always check `prefers-reduced-motion` before running any character animation.

## Modes

**SPRITE** â€” CSS sprite sheet frame animation.
Sprite sheet: single image containing all frames in a grid. PNG format, max 8192Ã—8192px (mobile GPUs may cap lower).
Frame count N: `sheet-width / frame-width` (horizontal) Ã— `sheet-height / frame-height` (vertical).
`animation-timing-function: steps(N, end)` â€” `end` steps at interval end (standard). `start` steps at interval start (causes off-by-one appearance). Never use `ease` or `linear` on sprite animations.
`background-size`: set to `N Ã— frame-width` (total sheet width). `background-position` animates from `0 0` to `-totalWidth 0` via `@keyframes`.
Pixel bleeding gotcha: add 1â€“2px padding between frames in the sprite sheet to prevent adjacent frame bleed at non-integer scales.
`prefers-reduced-motion`: freeze on frame 0 (`animation: none`) for users who prefer reduced motion.
NON-DEV: explain the "film strip" mental model before writing code.
End with: propose `/review` for performance audit.

**RIG** â€” Body-part character rigs (GSAP or Rive).
**GSAP rig:** Separate DOM/SVG elements per body part. Parent transforms stack â€” rotating a parent also rotates all children. Set `transform-origin` (or `transform-box: fill-box` for SVG) on every part before animating. Use GSAP timeline for walk cycles: `tl.to('.arm-left', { rotation: 30 }).to('.arm-right', { rotation: -30 }, '<')`.
**Rive (primary for interactive characters):** `.riv` file from Rive editor. Loads via `@rive-app/canvas` or `@rive-app/react`.
  State machines vs animations are distinct: `rive.load({ stateMachines: 'MachineName' })` â‰  `rive.load({ animations: 'AnimationName' })`. Wrong call = silent fail.
  Inputs: Boolean (on/off), Trigger (fires once via `.fire()`), Number (float). Input names must match exactly what's in the Rive editor â€” case-sensitive, silent fail on mismatch.
  React: `useRive()` hook from `@rive-app/react`. Returns `{ rive, RiveComponent }`.
**Spine (game context reference):** `spine-pixi-v8` for PixiJS v8 + WebGPU. `spine-webgl` for standalone. Version must match â€” runtime/data version mismatch = broken playback. Reference only â€” route to game engine skill for full Spine integration.
`prefers-reduced-motion`: pause Rive state machine (`rive.pause()`), freeze GSAP timeline.
NON-DEV: explain the approach choice (GSAP vs Rive) before writing code.
End with: propose `/review` for performance audit.

**REVIEW** â€” Performance and accessibility audit. Report each: PASS / WARN / FAIL.
Check: (1) sprite sheet size â‰¤ 8192Ã—8192, (2) steps(N, end) not ease/linear, (3) pixel bleeding padding, (4) prefers-reduced-motion handled, (5) Rive input names verified against editor, (6) GSAP transform-origin set on all parts, (7) user-uploaded .riv security, (8) Spine version match.
Full checklist in REFERENCE.md.
End with: propose `/qa` when clean.

**DEBUG** â€” Structured diagnosis.
4 intake questions:
  1. What should the character do that isn't happening â€” or what looks wrong?
  2. CSS sprite, GSAP rig, or Rive? If Rive: state machine or animation?
  3. Does it work at all, or does nothing appear?
  4. Any console errors? (paste them)
Common causes in REFERENCE.md.

## Security Gate
Every RIG response (Rive) ends with this line â€” never skip it:
`Security pass: âœ“ .riv file is from trusted source (not user-uploaded) âœ“ Rive state machine inputs are hardcoded (not user-supplied strings) âœ“ prefers-reduced-motion handled âœ“ no user data passed to animation properties`
Flag user-uploaded `.riv` files immediately â€” state machine logic is executable. Stop before rendering.

## Cross-Skill Integration
- `/css` â€” @keyframes, steps(), background-position for sprite sheets
- `/gsap` â€” timeline sequencing for GSAP body-part rigs; ctx.revert() cleanup
- `/svg-animation` â€” SVG character parts; transform-box: fill-box applies
- `/react` â€” useRive() hook, component context
- `/javascript` â€” Rive vanilla JS API, sprite sheet DOM setup
- `/lottie` â€” route for UI micro-interactions and vector loops (not skeletal rigs)

## Pipeline Connections
SPRITE complete â†’ propose `/review`
RIG complete â†’ propose `/review`
REVIEW clean â†’ propose `/qa`

## Anti-patterns
âœ— Never use `ease` or `linear` timing on sprite sheet animations â€” use `steps(N, end)` only
âœ— Never omit `steps()` N count â€” wrong frame count breaks the animation cycle
âœ— Never render user-uploaded `.riv` files without validation â€” state machine logic is executable
âœ— Never mismatch Rive state machine input names â€” silent fail, no error thrown
âœ— Never call `rive.load({ animations: ... })` when the file uses a state machine â€” silent fail
âœ— Never skip `transform-origin` on GSAP rig parts â€” parent rotation displaces children unexpectedly
âœ— Never exceed 8192Ã—8192 sprite sheet â€” breaks on mobile GPUs
âœ— Never autoplay character animation without `prefers-reduced-motion` check
âœ— Never use Spine in a non-game web context without a clear performance budget
âœ— Never skip the security gate line on RIG (Rive) output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.