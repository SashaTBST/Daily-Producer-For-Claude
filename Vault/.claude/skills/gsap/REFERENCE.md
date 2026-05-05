# /gsap — Reference

## When to Use GSAP vs CSS vs WAAPI

| Tool | Use when | Avoid when |
|------|---------|-----------|
| CSS transitions/animations | Simple hover, fade, slide, spinner | You need runtime control, sequencing, or scroll sync |
| Web Animations API (WAAPI) | Native browser animation, no library overhead | Complex timelines, non-CSS values (SVG, canvas) |
| GSAP | Complex sequences, scroll-linked, SVG/canvas, dynamic runtime control | Simple UI micro-interactions — CSS is lighter |

## Plugin Registry (all free — npm install gsap)

| Plugin | Purpose |
|--------|---------|
| ScrollTrigger | Scroll-linked animation, pinning, scrubbing, snap |
| ScrollSmoother | Smooth scroll with scroll-linked effects (requires ScrollTrigger) |
| Flip | Animate layout changes (FLIP technique — position/size morphing) |
| Draggable | Drag-and-drop with inertia and bounds |
| MorphSVG | SVG path morphing between shapes |
| SplitText | Split text into chars/words/lines for individual animation |
| Observer | Unified input detection (scroll, touch, pointer, wheel) |
| MotionPath | Animate elements along SVG paths |

Register before use (once, at app entry): `gsap.registerPlugin(ScrollTrigger, Flip, SplitText)`.

## React useGSAP Pattern

```tsx
import { useRef } from 'react';
import { useGSAP } from '@gsap/react';
import gsap from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';

gsap.registerPlugin(useGSAP, ScrollTrigger); // at module level, not inside component

export function AnimatedCard() {
  const container = useRef<HTMLDivElement>(null);

  useGSAP(() => {
    // selectors are scoped to container via scope prop
    gsap.from('.card', { opacity: 0, y: 40, duration: 0.6 });
  }, { scope: container }); // scope isolates querySelector to this component

  return <div ref={container}><div className="card">Hello</div></div>;
}
```

### contextSafe for post-mount handlers
```tsx
useGSAP(() => {
  const { contextSafe } = useGSAP(); // available inside the callback

  const handleClick = contextSafe(() => {
    gsap.to('.card', { scale: 1.1, duration: 0.2, yoyo: true, repeat: 1 });
  });

  document.querySelector('.btn')?.addEventListener('click', handleClick);
}, { scope: container });
```
Without `contextSafe()`, click handlers added after mount bypass cleanup and run on unmounted components.

## Vanilla JS SPA Cleanup

```js
import gsap from 'gsap';

// On page enter (e.g. Barba.js, Astro view transitions)
const ctx = gsap.context(() => {
  gsap.from('.hero', { opacity: 0, y: 60, duration: 0.8 });
  gsap.from('.nav a', { opacity: 0, stagger: 0.1, delay: 0.3 });
});

// On page leave — revert all animations in this context
ctx.revert();
```

## ScrollTrigger Patterns

### Basic scroll-linked tween
```js
gsap.to('.box', {
  x: 500,
  scrollTrigger: {
    trigger: '.box',
    start: 'top 80%',    // when top of .box hits 80% down the viewport
    end: 'bottom 20%',   // when bottom of .box hits 20% down the viewport
    scrub: 1,            // smooth scrub (1 = 1 second lag)
    markers: true,       // remove before production
  }
});
```

### Start/end string reference
`'top center'` = top of trigger, center of viewport.
`'bottom 80%'` = bottom of trigger, 80% from top of viewport.
`'+=300'` = 300px past the trigger point.

### Pinning
```js
ScrollTrigger.create({
  trigger: '.section',
  start: 'top top',
  end: '+=600',
  pin: true,
  anticipatePin: 1,   // prevents snap-jump on fast scroll
  pinSpacing: false,  // use when sections stack without gap
});
```

### React refresh pattern
```tsx
useGSAP(() => {
  // create triggers...
  requestAnimationFrame(() => ScrollTrigger.refresh()); // wait one frame for layout
}, { scope: container });
```

## Performance Review Checklist

Report each: PASS / WARN / FAIL.

| # | Check | FAIL condition |
|---|-------|---------------|
| 1 | Layout properties | `left`, `top`, `width`, `height` animated (use `x`, `y`, `scaleX/Y`) |
| 2 | will-change cleared | `will-change` not removed in `onComplete` |
| 3 | will-change overuse | More than 10–15 elements with active `will-change` |
| 4 | force3D on large surfaces | `force3D: true` on full-viewport elements without need |
| 5 | Plugin cleanup | ScrollTrigger triggers not killed on unmount |
| 6 | Context cleanup | `ctx.revert()` missing in SPA or React cleanup |
| 7 | Plugin registration | `registerPlugin` called inside component render |
| 8 | innerHTML risk | User-supplied data animated into innerHTML |
| 9 | CSS property conflict | Same property animated by both GSAP and CSS transition |
| 10 | Vite named imports | Default plugin import used instead of named subpath import |

## DEBUG — Common Issues

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| Animation fires twice (React) | Strict Mode double-invoke | Use `useGSAP()` — it handles Strict Mode correctly |
| ScrollTrigger positions wrong after load | Missing `refresh()` call | `ScrollTrigger.refresh()` after all triggers created |
| ScrollTrigger kills other components' triggers | `getAll().forEach(t.kill())` | Use `ctx.revert()` inside `useGSAP` return |
| Plugin not working — no error | Not registered | `gsap.registerPlugin(PluginName)` at entry file |
| Vite bundler error on plugin import | Default import | `import { ScrollTrigger } from 'gsap/ScrollTrigger'` |
| Selector targeting wrong elements | No scope set in React | Add `scope: containerRef` to `useGSAP` options |
| Animation jerky or blurry | `force3D` on non-retina | Remove `force3D: true` or set `force3D: 'auto'` |
| Event handler runs after unmount | Missing `contextSafe()` | Wrap handler in `contextSafe()` from `useGSAP` |

## Research Sources

1. https://www.npmjs.com/package/gsap — GSAP 3.15.0 release (April 2026)
2. https://gsap.com/pricing/ — All plugins now free
3. https://gsap.com/resources/React/ — Official React/useGSAP docs
4. https://github.com/greensock/react — useGSAP source and patterns
5. https://www.augustinfotech.com/blogs/optimizing-gsap-and-canvas-for-smooth-performance-and-responsive-design/ — Performance guide
6. https://gsap.com/docs/v3/Plugins/ScrollTrigger/refresh()/ — ScrollTrigger refresh API
7. https://medium.com/design-bootcamp/web-animation-apis-vs-gsap-vs-motion-one-d10edb5c4261 — GSAP vs WAAPI comparison
