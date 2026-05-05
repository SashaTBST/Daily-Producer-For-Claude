# /anime-js — Reference

## When to Use anime-js vs CSS vs GSAP vs WAAPI

| Tool | Use when | Avoid when |
|------|---------|-----------|
| CSS transitions/animations | Simple hover, fade, slide, spinner | Runtime control, sequencing, SVG morphing |
| Web Animations API (WAAPI) | Native browser, no library overhead | Complex timelines, non-CSS values |
| anime-js | Multi-step timelines, SVG effects, vanilla JS sequencing | Scroll-linked (use GSAP), React-heavy (Strict Mode risk) |
| GSAP | Scroll-linked, React (useGSAP hook), production-grade | Small bundle budget, simple timelines |

## React createScope() Pattern

```tsx
import { useEffect, useRef } from 'react';
import { animate, createScope } from 'animejs';

export function AnimatedCard() {
  const containerRef = useRef<HTMLDivElement>(null);
  const scopeRef = useRef<ReturnType<typeof createScope> | null>(null);
  const didInit = useRef(false); // guards against React 18 Strict Mode double-invoke

  useEffect(() => {
    if (didInit.current) return;
    didInit.current = true;

    scopeRef.current = createScope({ root: containerRef });
    scopeRef.current.execute(() => {
      animate('.card', { opacity: [0, 1], translateY: [40, 0], duration: 600, ease: 'outExpo' });
    });

    return () => {
      scopeRef.current?.revert();
    };
  }, []);

  return <div ref={containerRef}><div className="card">Hello</div></div>;
}
```

**Strict Mode warning:** React 18 runs effects twice in dev. The `didInit` ref guard prevents duplicate animations. For production-grade React animation, consider /gsap — `useGSAP()` handles Strict Mode natively.

## Timeline Offset Syntax

| Offset value | Meaning |
|---|---|
| `500` | Start 500ms from timeline start (absolute) |
| `'+=300'` | Start 300ms after previous animation ends |
| `'-=200'` | Start 200ms before previous animation ends (overlap) |
| `'<'` | Start at the same time as previous animation |
| `'<+=200'` | Start 200ms after previous animation starts |
| `labelName` | Start at a named label position |

```js
import { createTimeline } from 'animejs';

const tl = createTimeline({ defaults: { duration: 600, ease: 'outExpo' } });

tl.add('.hero',    { opacity: [0, 1], translateY: [60, 0] })
  .add('.nav a',   { opacity: [0, 1], translateX: [-20, 0] }, '+=100')
  .add('.cta',     { scale: [0.8, 1], opacity: [0, 1] }, '<+=200')
  .call(() => console.log('sequence complete'), '+=500');
```

## Cleanup: .cancel() vs .revert() vs utils.remove()

| Method | What it does | When to use |
|---|---|---|
| `animation.pause()` | Stops playback, keeps inline styles | Temporary pause |
| `animation.cancel()` | Stops + removes from engine, keeps inline styles | Stop without visual reset |
| `animation.revert()` | Cancel + reverts ALL inline styles + cleans memory | **Preferred** — SPA/React cleanup |
| `utils.remove(targets)` | Removes targets from ALL active animations globally | Global clear — use with care |

## SVG Utilities

All require the actual SVG element (not always a CSS selector string):

```js
import { animate, svg } from 'animejs';

// Stroke draw animation
const drawable = svg.createDrawable('path#myPath');
animate(drawable, { draw: '0 1', duration: 1200, ease: 'inOutSine' });

// Motion path — element follows SVG path
const motionPath = svg.createMotionPath('path#track');
animate('.car', { ...motionPath, duration: 2000, ease: 'linear' });

// Shape morph
animate('path#shape', {
  d: svg.morphTo('path#targetShape', 2), // precision = 2
  duration: 800,
  ease: 'inOutQuad',
});
```

## Easing Reference (v4 — shortened names)

| v3 name | v4 name | Notes |
|---|---|---|
| `easeOutQuad` | `outQuad` | |
| `easeInOutSine` | `inOutSine` | |
| `easeOutExpo` | `outExpo` | Common default |
| `easeInOutBack` | `inOutBack` | |
| `linear` | `linear` | Unchanged |
| `spring` | `spring(mass, stiffness, damping, velocity)` | Physics-based |
| — | `out(2)` | Default (equivalent to outQuad) |

Import all easings: `import * as easings from 'animejs/easings'`

## Performance Review Checklist

Report each: PASS / WARN / FAIL.

| # | Check | FAIL condition |
|---|-------|---------------|
| 1 | Layout properties | `left`, `top`, `width`, `height` animated |
| 2 | Composition explicit | Relying on default `replace` when layering intended |
| 3 | Cleanup present | `.revert()` missing in SPA/React cleanup |
| 4 | utils.remove() scope | Called globally when component-scoped intent |
| 5 | CSS custom properties | User-supplied data fed into animated CSS variable |
| 6 | SVG attributes | User-supplied data fed into animated SVG attribute |
| 7 | Easing format | v3 easing string used (`easeOutExpo` vs `outExpo`) |
| 8 | React Strict Mode | createScope() used without double-invoke guard |

## DEBUG — Common Issues

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| `animate is not a function` | Default import used | `import { animate } from 'animejs'` |
| Animation fires but wrong easing | v3 easing string | Rename — `easeOutExpo` → `outExpo` |
| New animation cancels previous | Composition default `replace` | Set `composition: 'blend'` to layer |
| Animation fires twice (React) | Strict Mode double-invoke | Add `didInit` ref guard (see React pattern above) |
| Styles not reset after unmount | `.cancel()` used not `.revert()` | Replace with `.revert()` |
| Timeline step fires at wrong time | Offset string misread | Check offset reference table above |
| SVG morph has jagged edges | Low precision value | Increase `morphTo` precision parameter |
| Element not found in createScope | Scope root not set | Pass `{ root: containerRef }` to `createScope()` |

## Research Sources

1. https://www.npmjs.com/package/animejs — v4.4.0
2. https://animejs.com/documentation/ — official docs
3. https://github.com/juliangarnier/anime/wiki/Migrating-from-v3-to-v4 — breaking changes
4. https://github.com/juliangarnier/anime/wiki/What's-new-in-Anime.js-V4
5. https://animejs.com/documentation/getting-started/using-with-react/
6. https://animejs.com/documentation/timeline/
7. https://animejs.com/documentation/svg/
8. https://animejs.com/documentation/animation/animation-methods/cancel/
