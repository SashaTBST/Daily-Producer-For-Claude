# /character-animation — Reference

## When-to-Use Decision Tree

| Need | Use |
|------|-----|
| Simple looping UI animation (icon, loader) | /lottie |
| Interactive character with state (mascot, chatbot avatar) | /character-animation — RIG (Rive) |
| Walk cycle / frame-by-frame sprite | /character-animation — SPRITE |
| SVG character rig with GSAP timeline | /character-animation — RIG (GSAP) |
| 2D game character with complex skeleton (game engine) | Spine or DragonBones — reference below |
| 3D game character | /threejs or /babylonjs (skeleton/skinning) |
| Motion path for a character part | /motion-path |
| SVG body parts animated individually | /svg-animation |

---

## CSS Sprite Sheet — Full Pattern

```css
/* Container: matches single frame dimensions */
.character {
  width: 64px;
  height: 64px;
  background-image: url('walk-cycle.png');
  background-repeat: no-repeat;
  /* Total sheet width = frame-width × frame-count */
  background-size: 512px 64px; /* 8 frames × 64px */
  animation: walk 0.8s steps(8, end) infinite;
}

@keyframes walk {
  from { background-position: 0 0; }
  to   { background-position: -512px 0; }
}

/* Accessibility — always include */
@media (prefers-reduced-motion: reduce) {
  .character { animation: none; }
}
```

**steps(N, end) explained:**
- N = total frame count
- `end` = step at interval end (standard — matches frame timing)
- `start` = step at interval start (off-by-one appearance — avoid)
- Never use `ease`, `linear`, or `cubic-bezier` — they interpolate between frames

**Sprite sheet requirements:**
- PNG format
- Max 8192×8192px (mobile GPU limit)
- Add 1–2px padding between frames to prevent bleeding at non-integer scales
- Use TexturePacker or similar to generate consistent sheet

---

## Rive State Machine — Full Pattern

### Vanilla JS
```javascript
import { Rive } from '@rive-app/canvas';

const r = new Rive({
  src: 'character.riv',
  canvas: document.querySelector('#canvas'),
  autoplay: true,
  stateMachines: 'CharacterMachine', // exact name from Rive editor
  onLoad: () => {
    // Only access inputs after load
    const inputs = r.stateMachineInputs('CharacterMachine');
    const isWalking = inputs.find(i => i.name === 'IsWalking'); // Boolean
    const speed = inputs.find(i => i.name === 'Speed');         // Number
    const jump = inputs.find(i => i.name === 'Jump');           // Trigger

    isWalking.value = true;   // Boolean: set directly
    speed.value = 2.5;        // Number: set directly
    jump.fire();               // Trigger: call .fire()
  }
});

// Cleanup on unmount
// r.cleanup();
```

### React
```jsx
import { useRive, useStateMachineInput } from '@rive-app/react-canvas';

function Character() {
  const { RiveComponent, rive } = useRive({
    src: 'character.riv',
    stateMachines: 'CharacterMachine',
    autoplay: true,
  });

  const isWalking = useStateMachineInput(rive, 'CharacterMachine', 'IsWalking');
  const jump = useStateMachineInput(rive, 'CharacterMachine', 'Jump');

  return (
    <>
      <RiveComponent style={{ width: 200, height: 200 }} />
      <button onClick={() => { if (isWalking) isWalking.value = !isWalking.value; }}>
        Toggle Walk
      </button>
      <button onClick={() => { if (jump) jump.fire(); }}>
        Jump
      </button>
    </>
  );
}
```

**Silent failure causes:**
- Input name mismatch (case-sensitive) — no error, just no response
- `stateMachines:` vs `animations:` wrong key — plays nothing silently
- Accessing inputs before `onLoad` fires — inputs are undefined

---

## GSAP Body-Part Rig — Pattern

```javascript
import { gsap } from 'gsap';

// HTML structure: parent → child parts
// <div class="character">
//   <div class="torso">
//     <div class="arm-left"></div>
//     <div class="arm-right"></div>
//     <div class="leg-left"></div>
//     <div class="leg-right"></div>
//   </div>
// </div>

// Set transform-origin before animating — always
gsap.set('.arm-left',  { transformOrigin: 'top center' });
gsap.set('.arm-right', { transformOrigin: 'top center' });
gsap.set('.leg-left',  { transformOrigin: 'top center' });
gsap.set('.leg-right', { transformOrigin: 'top center' });

// Walk cycle timeline
const walkTl = gsap.timeline({ repeat: -1 });

walkTl
  .to('.arm-left',  { rotation: 30,  duration: 0.3, ease: 'sine.inOut' })
  .to('.arm-right', { rotation: -30, duration: 0.3, ease: 'sine.inOut' }, '<')
  .to('.leg-left',  { rotation: -20, duration: 0.3, ease: 'sine.inOut' }, '<')
  .to('.leg-right', { rotation: 20,  duration: 0.3, ease: 'sine.inOut' }, '<')
  .to('.arm-left',  { rotation: -30, duration: 0.3, ease: 'sine.inOut' })
  .to('.arm-right', { rotation: 30,  duration: 0.3, ease: 'sine.inOut' }, '<')
  .to('.leg-left',  { rotation: 20,  duration: 0.3, ease: 'sine.inOut' }, '<')
  .to('.leg-right', { rotation: -20, duration: 0.3, ease: 'sine.inOut' }, '<');

// prefers-reduced-motion
if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) {
  walkTl.pause();
}

// React cleanup: useEffect(() => () => walkTl.kill(), []);
```

---

## State Machine Transition Checklist

Verify every transition path before shipping. Missing = character freezes in that state.

| From → To | Check |
|-----------|-------|
| Idle → Walk | ☐ |
| Walk → Idle | ☐ |
| Walk → Run | ☐ |
| Run → Walk | ☐ |
| Any → Jump | ☐ |
| Jump → Idle | ☐ |
| Jump → Walk | ☐ |
| Any → Attack (if applicable) | ☐ |
| Attack → Idle | ☐ |
| Any → Death (if applicable) | ☐ |

---

## Spine 2D — Game Context Reference

Route game engine Spine integration to the relevant game engine skill. This is reference only.

**Export pipeline:** Spine editor → Export → JSON or binary (.skel) + .atlas + .png. Three files must travel together.

**Web runtimes:**
- PixiJS v8 + WebGPU → `spine-pixi-v8`
- Standalone WebGL → `spine-webgl`
- Phaser 4 → built-in Spine plugin (added April 2025)
- PlayCanvas, Defold — official plugins

**Version lock rule:** Spine runtime version must match Spine editor export version. Version mismatch = silent broken playback. Lock in `package.json`.

**Never:** Edit Spine JSON manually. Always use Spine editor tools.

---

## Security Patterns

| Risk | Mitigation |
|------|-----------|
| Malicious JSON schema in Spine/Rive/Lottie files | `JSON.parse()` only, never `eval()`. Validate schema before loading. |
| User-uploaded .riv file | STOP. State machine logic is executable. Server-side validation required before rendering. |
| Untrusted Spine .skel binary | Official runtimes only. No custom binary parsers. |
| CDN-hosted animation assets compromised | Self-host. Lock CDN versions with SRI where possible. |
| User input passed to animation properties | Sanitise before passing to `input.value` or `speed.value`. |

---

## Performance Checklist

| Check | Pass condition |
|-------|---------------|
| Sprite sheet size | ≤ 8192×8192px |
| Sprite timing function | `steps(N, end)` — not ease/linear |
| Frame bleed prevention | 1–2px padding between frames in sheet |
| prefers-reduced-motion | Pause/freeze handled |
| Rive input names | Verified against Rive editor (case-sensitive) |
| GSAP transform-origin | Set on all animated parts before animating |
| .riv source | Trusted source only |
| Spine version | Runtime === editor export version |
| Memory cleanup | Rive: `r.cleanup()`. GSAP: `tl.kill()`. Both on unmount. |
| Mobile GPU | Confirmed on target devices |

---

## DEBUG — Common Issues

| Symptom | Cause | Fix |
|---------|-------|-----|
| Sprite blurred or interpolating between frames | `ease` instead of `steps()` | Change to `steps(N, end)` |
| Sprite shows 2 frames simultaneously | No padding between frames | Add 1–2px frame padding in sheet |
| Rive no response to input | Input name mismatch (case-sensitive) | Check exact names in Rive editor |
| Rive silent fail on load | Wrong key: `animations:` instead of `stateMachines:` | Match key to file type |
| GSAP rig rotation displaces children | `transform-origin` not set | Set `transformOrigin` on every part first |
| Character frozen in state | Missing state transition path | Complete transition matrix |
| Spine animation garbled | Runtime / editor version mismatch | Lock runtime version to match export |
| Works on desktop, broken on mobile | Sprite sheet > 8192×8192px | Reduce or split sheet |
| Memory leak after unmount | Rive/GSAP not cleaned up | `r.cleanup()` / `tl.kill()` on unmount |

---

## Research Sources

1. [Phaser JS and Spine 2D — April 2025](https://phaser.io/news/2025/04/phaser-js-and-spine-2d)
2. [Spine Runtimes](http://esotericsoftware.com/spine-runtimes)
3. [Spine JSON format](http://en.esotericsoftware.com/spine-json-format)
4. [DragonBones GitHub](https://github.com/DragonBones)
5. [Rive State Machine Overview](https://help.rive.app/editor/state-machine)
6. [Rive as Lottie alternative](https://rive.app/blog/rive-as-a-lottie-alternative)
7. [CSS Sprite Sheet Animations — steps()](https://blog.teamtreehouse.com/css-sprite-sheet-animations-steps)
8. [Animating Sprite Sheets With JS](https://dev.to/martyhimmel/animating-sprite-sheets-with-javascript-ag3)
9. [GSAP SVG character animation](https://gsap.com/svg/)
10. [Rive React integration](https://rive.app/community/doc/react/docvlgbnS1mp)