# /character-animation — Reference

## Approach Decision Table

| Approach | Use when | Avoid when |
|----------|---------|-----------|
| CSS sprite sheet | Simple walk cycles, idle loops, library-free, fixed frame count | Complex rigs, interactive state, runtime control needed |
| GSAP body-part rig | Custom character built in HTML/SVG, precise timeline control | Interactive state machines, pre-built character assets |
| Rive | Interactive characters, state-machine driven responses, designer-built rigs | Simple loops (use CSS), Lottie-style micro-interactions (use /lottie) |
| Spine | Game engine contexts (PixiJS, Phaser), skeletal mesh deformation | Non-game web (use Rive instead) |
| DragonBones | Legacy projects only | New projects — use Rive or Spine |

## CSS Sprite Sheet Pattern

```css
.character {
  width: 128px;         /* single frame width */
  height: 128px;        /* single frame height */
  background-image: url('/sprites/walk-cycle.png');
  background-size: 1024px 128px; /* total sheet width × frame height */
  /* 8 frames across = 1024 / 128 = 8 */
  animation: walk 0.8s steps(8, end) infinite;
}

@keyframes walk {
  from { background-position: 0 0; }
  to   { background-position: -1024px 0; } /* -(sheet-width) 0 */
}

/* prefers-reduced-motion — always include */
@media (prefers-reduced-motion: reduce) {
  .character { animation: none; background-position: 0 0; } /* freeze frame 0 */
}
```

Multi-row sprite sheet (multiple animations on one sheet):
```css
/* Row 0 = idle (4 frames), Row 1 = walk (8 frames), Row 2 = run (6 frames) */
.character.idle { background-position-y: 0; animation: idle 0.4s steps(4, end) infinite; }
.character.walk { background-position-y: -128px; animation: walk 0.8s steps(8, end) infinite; }

@keyframes idle { to { background-position-x: -512px; } }
@keyframes walk { to { background-position-x: -1024px; } }
```

**Pixel bleeding fix:** Add 1–2px transparent padding between frames in the sprite sheet source file. Scale-aware: non-integer CSS scaling causes sub-pixel rendering that bleeds into adjacent frames.

**Frame count formula:**
```
N (horizontal) = sprite-sheet-width / frame-width
N (vertical)   = sprite-sheet-height / frame-height
steps() value  = frames per animation row
```

## GSAP Body-Part Rig Pattern

```html
<div class="character">
  <div class="body"></div>
  <div class="head"></div>
  <div class="arm-left"></div>
  <div class="arm-right"></div>
  <div class="leg-left"></div>
  <div class="leg-right"></div>
</div>
```

```js
import gsap from 'gsap';

// transform-origin MUST be set before any rotation — 
// body parts rotate around their joint, not their centre
gsap.set('.arm-left',  { transformOrigin: 'top center' }); // shoulder pivot
gsap.set('.arm-right', { transformOrigin: 'top center' });
gsap.set('.leg-left',  { transformOrigin: 'top center' }); // hip pivot
gsap.set('.leg-right', { transformOrigin: 'top center' });
gsap.set('.head',      { transformOrigin: 'bottom center' }); // neck pivot

// Walk cycle timeline
const walk = gsap.timeline({ repeat: -1 });
walk
  .to('.arm-left',  { rotation: 35,  duration: 0.4, ease: 'sine.inOut' })
  .to('.arm-right', { rotation: -35, duration: 0.4, ease: 'sine.inOut' }, '<')
  .to('.leg-left',  { rotation: -25, duration: 0.4, ease: 'sine.inOut' }, '<')
  .to('.leg-right', { rotation: 25,  duration: 0.4, ease: 'sine.inOut' }, '<')
  .to('.arm-left',  { rotation: -35, duration: 0.4, ease: 'sine.inOut' })
  .to('.arm-right', { rotation: 35,  duration: 0.4, ease: 'sine.inOut' }, '<')
  .to('.leg-left',  { rotation: 25,  duration: 0.4, ease: 'sine.inOut' }, '<')
  .to('.leg-right', { rotation: -25, duration: 0.4, ease: 'sine.inOut' }, '<');

// prefers-reduced-motion
if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) {
  walk.pause();
}
```

For SVG character parts: add `transform-box: fill-box; transform-origin: center` via CSS — see /svg-animation for the full pattern.

## Rive — State Machine Pattern

Install: `npm install @rive-app/canvas` or `npm install @rive-app/react`

```tsx
// React — useRive hook
import { useRive, useStateMachineInput } from '@rive-app/react';

export function Character() {
  const { rive, RiveComponent } = useRive({
    src: '/animations/character.riv',
    stateMachines: 'CharacterMachine', // must match EXACTLY what's in Rive editor
    autoplay: true,
  });

  // Input names must match exactly — case-sensitive, silent fail on mismatch
  const isRunning = useStateMachineInput(rive, 'CharacterMachine', 'IsRunning'); // Boolean
  const speed     = useStateMachineInput(rive, 'CharacterMachine', 'Speed');     // Number
  const jump      = useStateMachineInput(rive, 'CharacterMachine', 'Jump');      // Trigger

  const handleRun = () => { if (isRunning) isRunning.value = true; };
  const handleJump = () => { if (jump) jump.fire(); };
  const handleSpeed = (v: number) => { if (speed) speed.value = v; };

  return <RiveComponent style={{ width: 200, height: 200 }} />;
}
```

```js
// Vanilla JS
import { Rive } from '@rive-app/canvas';

const r = new Rive({
  src: '/animations/character.riv',
  canvas: document.getElementById('canvas'),
  stateMachines: 'CharacterMachine',
  autoplay: true,
  onLoad: () => {
    const inputs = r.stateMachineInputs('CharacterMachine');
    const isRunning = inputs.find(i => i.name === 'IsRunning');
    const jump = inputs.find(i => i.name === 'Jump');
    isRunning.value = true;
    jump.fire();
  },
});

// prefers-reduced-motion
if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) {
  r.pause();
}
```

**State machine vs animation — they are different load paths:**
```js
// State machine — use stateMachines property
new Rive({ stateMachines: 'MachineName', ... });

// Simple animation — use animations property
new Rive({ animations: 'AnimationName', ... });

// Wrong: silent fail — no error thrown
new Rive({ animations: 'MachineName', ... }); // state machine name in animations field = nothing plays
```

## Rive Input Types

| Type | In editor | In code | Use for |
|------|-----------|---------|---------|
| Boolean | Toggle checkbox | `input.value = true/false` | States: running, jumping, idle |
| Number | Numeric slider | `input.value = 0.5` | Speed, blend amount, health |
| Trigger | Lightning bolt | `input.fire()` | One-shot events: jump, hit, collect |

## Spine Web Reference (Game Context Only)

```
npm install @esotericsoftware/spine-pixi-v8  // PixiJS v8 + WebGPU
npm install @esotericsoftware/spine-webgl     // standalone WebGL
```

**Version matching is mandatory:** Spine editor version → export version → runtime package version must all align. Mismatch = broken playback with no useful error.

Version check: in Spine editor → Help → About. Export with matching version. Install matching runtime.
Not recommended for non-game web — use Rive for interactive web characters instead.

## Review Checklist

Report each: PASS / WARN / FAIL.

| # | Check | FAIL condition |
|---|-------|---------------|
| 1 | Sprite timing function | `ease` or `linear` used on sprite sheet (must be `steps`) |
| 2 | Sprite sheet dimensions | Over 8192×8192 |
| 3 | Pixel bleeding padding | No inter-frame padding on sprite sheet |
| 4 | prefers-reduced-motion | Missing in both sprite and rig implementations |
| 5 | GSAP transform-origin | Not set before animation on body-part elements |
| 6 | Rive state machine name | Input names not verified against Rive editor |
| 7 | Rive load path | `animations` vs `stateMachines` mismatch |
| 8 | User-uploaded .riv | Unvalidated .riv file from user input rendered |
| 9 | Spine version | Runtime and export version not confirmed to match |

## DEBUG — Common Issues

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| Sprite sheet shows wrong frame / partial image | Incorrect `background-size` or `steps(N)` count | Recalculate: `background-size = sheet-width px` and `steps = sheet-width / frame-width` |
| Sprite animation blurs between frames | `ease` or `linear` timing function used | Replace with `steps(N, end)` |
| Adjacent frame visible at edges | No inter-frame padding / sub-pixel scaling | Add 1–2px padding between frames in sprite sheet |
| GSAP rig rotates from wrong pivot | `transformOrigin` not set | `gsap.set(el, { transformOrigin: 'top center' })` before any animation |
| GSAP parent rotation displaces children | Expected — parent transforms stack | Restructure hierarchy or offset children's transforms |
| Rive nothing plays, no error | Input name mismatch or wrong load path | Verify name exactly in Rive editor; check stateMachines vs animations |
| Rive trigger fires but nothing happens | Input not found (returns undefined) | `console.log(r.stateMachineInputs('MachineName'))` to list all inputs |
| Spine broken with no useful error | Version mismatch | Match editor version → export → runtime package exactly |

## Research Sources

1. https://blog.logrocket.com/making-css-animations-using-a-sprite-sheet/
2. https://www.joshwcomeau.com/animation/sprites/ — steps() explained
3. https://gsap.com/ — GSAP body-part rig patterns
4. https://rive.app/ — Rive platform
5. https://digitalbydefault.ai/blog/rive-interactive-animation-review-2026 — Rive 2026 status
6. https://help.rive.app/editor/state-machine — Rive state machine guide
7. https://esotericsoftware.com/spine-runtimes — Spine web runtimes
8. http://esotericsoftware.com/blog/spine-pixi-v8-runtime-released — Spine PixiJS v8
9. https://github.com/DragonBones — DragonBones status
10. https://gsap.com/resources/a11y/ — accessibility in animation
