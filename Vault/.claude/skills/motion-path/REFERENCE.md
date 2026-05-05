# /motion-path — Reference

## Approach Decision Table

| Approach | Use when | Avoid when |
|----------|---------|-----------|
| CSS offset-path | Simple path traversal, no scroll, no sequencing, library-free | Safari is a hard requirement (use url() fallback), complex timing |
| GSAP MotionPath | Scroll-linked, React, sequenced, partial start/end traversal | Small bundle budget, no GSAP already in project |
| anime-js createMotionPath | anime-js already in use, lightweight timeline sequencing | Scroll-linked (use GSAP), React Strict Mode edge cases |

## Browser Support (offset-path)

| Browser | offset-path | path() | url(#id) | ray() |
|---------|------------|--------|----------|-------|
| Chrome / Edge | ✓ Full | ✓ | ✓ | ✓ |
| Firefox | ✓ Full | ✓ | ✓ | Partial |
| Safari | ⚠ Partial | iOS 16.2+ (inconsistent) | ✓ | ✗ |

**Safari production recommendation:** Use `url(#svgPathId)` referencing an inline SVG `<path>` as the universal fallback. `path()` string works in Safari iOS 16.2+ but support is inconsistently documented — always test on device.

Feature detection pattern:
```css
@supports (offset-path: path('M0,0')) {
  .element { offset-path: path('M0,0 C100,0 200,100 300,0'); }
}
/* Fallback for non-supporting browsers */
.element { /* static position or CSS transform fallback */ }
```

## CSS offset-path Full Pattern

```html
<!-- Inline SVG path — used as reference for url(#id) fallback -->
<svg style="position:absolute;width:0;height:0;overflow:hidden" aria-hidden="true">
  <defs>
    <path id="orbit-path" d="M0,0 C100,-80 300,-80 400,0 C500,80 700,80 800,0"/>
  </defs>
</svg>

<div class="element"></div>
```

```css
.element {
  offset-path: url(#orbit-path);       /* Safari-safe fallback */
  offset-path: path('M0,0 C100,-80 300,-80 400,0 C500,80 700,80 800,0'); /* modern */
  offset-distance: 0%;                 /* explicit start — never omit */
  offset-rotate: auto;                 /* follow tangent */
  offset-anchor: center;              /* rotation pivot at element centre */
  animation: travel 3s ease-in-out infinite;
}

@keyframes travel {
  to { offset-distance: 100%; }
}
```

`offset-rotate` values:
- `auto` — element nose points in direction of travel (tangent)
- `auto reverse` — element tail points in direction of travel
- `auto 90deg` — tangent + 90° offset (e.g. side-facing element on curve)
- `0deg` — element keeps constant angle regardless of path

## Getting the Path String

**From Figma:**
1. Draw your path with Pen tool
2. Export as SVG (File → Export → SVG)
3. Open SVG in text editor, find `<path d="..."/>`
4. Copy the `d` attribute value — this is your path string
5. Figma exports absolute coordinates — confirm the path origin matches your element's parent

**From Illustrator:**
1. Draw path → Object → Path → Simplify (reduce nodes)
2. File → Export As → SVG
3. Extract `d` attribute from exported code

**Hand-coded paths (key commands):**
- `M x,y` — move to (start point, no line)
- `L x,y` — line to
- `C cx1,cy1 cx2,cy2 x,y` — cubic bezier
- `Q cx,cy x,y` — quadratic bezier
- `A rx,ry rotation large-arc sweep x,y` — arc
- `Z` — close path

## GSAP MotionPath Pattern

```js
import { gsap } from 'gsap';
import { MotionPathPlugin } from 'gsap/MotionPathPlugin';

gsap.registerPlugin(MotionPathPlugin); // once, at app entry

gsap.to('.element', {
  duration: 4,
  ease: 'power1.inOut',
  motionPath: {
    path: '#svgPath',          // SVG element selector or path string
    align: '#svgPath',         // align coordinate space to this element
    alignOrigin: [0.5, 0.5],   // [x, y] pivot — [0.5, 0.5] = centre
    autoRotate: true,          // true = follow tangent; degrees = offset
    start: 0,                  // 0 = path start (0–1)
    end: 1,                    // 1 = path end (0–1)
    resolution: 40,            // sample points — default 12 is too low for curves
  }
});
```

**Resolution guide:**
| Path type | Recommended resolution |
|-----------|----------------------|
| Straight lines | 12 (default ok) |
| Gentle curves | 20–30 |
| Complex curves | 40–60 |
| Tight loops | 80+ |

**Scroll-linked path motion:**
```js
gsap.to('.element', {
  motionPath: { path: '#track', align: '#track', alignOrigin: [0.5, 0.5], autoRotate: true },
  scrollTrigger: { trigger: '.section', start: 'top top', end: 'bottom bottom', scrub: 1 }
});
```

## anime-js createMotionPath Pattern

```js
import { animate, createTimeline, svg } from 'animejs';

const pathEl = document.querySelector('#orbit-path'); // SVG element ref, not string alone
const motionPath = svg.createMotionPath(pathEl);
// returns: { translateX: Function, translateY: Function, rotate: Function }

// Spread into animate()
animate('.element', {
  ...motionPath,
  duration: 3000,
  ease: 'linear',
  loop: true,
});

// In a timeline
const tl = createTimeline({ defaults: { duration: 2000 } });
tl.add('.element', { ...motionPath, ease: 'inOutSine' });
```

**v3 migration note:** `anime.path()` → `svg.createMotionPath()`. Returns `{ translateX, translateY, rotate }` not `{ x, y, angle }`.

## Path Coordinate Mismatch — Fix

Symptom: element travels along a path but in the wrong position on screen.
Cause: SVG `viewBox` scaling transforms the path's coordinate space relative to the page.

**CSS fix:** Place the `<path>` element inside an inline SVG that has `overflow: visible` and no `viewBox` transform, or use coordinates relative to the parent container.

**GSAP fix:** Always set `align` to the same SVG element the path lives in. GSAP resolves coordinate transforms automatically when `align` is set.

```js
// align: '#svgContainer' tells GSAP to resolve coordinates relative to that SVG
gsap.to('.dot', {
  motionPath: { path: '#track', align: '#svgContainer', alignOrigin: [0.5, 0.5] }
});
```

**anime-js fix:** Ensure `pathEl` is within the same SVG as your target, and the SVG has no conflicting `viewBox` transform. Manual coordinate normalisation may be needed for complex layouts.

## Review Checklist

Report each: PASS / WARN / FAIL.

| # | Check | FAIL condition |
|---|-------|---------------|
| 1 | Property name | `motion-path` used instead of `offset-path` |
| 2 | offset-distance explicit | `offset-distance: 0%` missing — element jumps |
| 3 | Animated property | `offset-path` animated directly (not `offset-distance`) |
| 4 | Safari fallback | `path()` used without `url(#id)` fallback |
| 5 | GSAP resolution | Resolution < 20 on curved path |
| 6 | Coordinate system | SVG viewBox mismatch without `align` or coordinate fix |
| 7 | offset-anchor | Default used on element where visual ≠ geometric centre |
| 8 | User-supplied path | `path()` string value comes from user input |

## DEBUG — Common Issues

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| Element snaps to path start on load | Missing `offset-distance: 0%` | Add explicit `offset-distance: 0%` |
| Element not rotating with path | `offset-rotate` not set or explicit angle used | Set `offset-rotate: auto` |
| Path works in Chrome, broken in Safari | `path()` unsupported | Use `url(#svgPathId)` fallback |
| GSAP motion jerky on curves | `resolution: 12` default | Increase `resolution` to 40+ |
| anime-js createMotionPath wrong position | Selector string passed, not element ref | `document.querySelector('#path')` then pass the element |
| Element travels in wrong area of screen | Coordinate system mismatch | Add `align` in GSAP; check SVG viewBox for CSS/anime-js |
| `offset-path: path()` has no effect | Old `motion-path` property used | Replace with `offset-path` |
| Partial traversal needed | CSS can't do partial | Use GSAP `start`/`end` (0–1) on motionPath |

## Research Sources

1. https://developer.mozilla.org/en-US/docs/Web/CSS/offset-path
2. https://developer.mozilla.org/en-US/docs/Web/CSS/offset-rotate
3. https://developer.mozilla.org/en-US/docs/Web/CSS/offset-anchor
4. https://www.w3.org/TR/motion-1/ — CSS Motion Path Level 1 spec
5. https://caniuse.com/css-motion-paths — browser support
6. https://gsap.com/docs/v3/Plugins/MotionPathPlugin/
7. https://animejs.com/documentation/svg/createmotionpath/
8. https://github.com/juliangarnier/anime/wiki/Migrating-from-v3-to-v4
9. https://forum.figma.com/ask-the-community-7/help-exporting-svg-files-absolute-vs-relative-paths-11118
10. https://www.smashingmagazine.com/2016/12/gpu-animation-doing-it-right/
