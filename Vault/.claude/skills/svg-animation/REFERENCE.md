# /svg-animation — Reference

## Technique Decision Table

| Technique | CSS/WAAPI | SMIL | /gsap | /anime-js |
|-----------|-----------|------|-------|-----------|
| fill / stroke / opacity | ✓ | ✓ | ✓ | ✓ |
| transform (rotate/scale/translate) | ✓ (with transform-box fix) | ✓ | ✓ | ✓ |
| stroke-dashoffset draw-on | ✓ | ✓ | ✓ (DrawSVGPlugin) | ✓ (createDrawable) |
| clip-path | ✓ (same shape only) | — | ✓ | ✓ |
| Path morph (d attribute) | ✗ | ✓ | ✓ (MorphSVG) | ✓ (morphTo) |
| viewBox animation | ✗ | ✓ | ✓ (.attr()) | ✓ (animate attr) |
| Motion path | ✗ | ✓ | ✓ (MotionPath) | ✓ (createMotionPath) |
| SVG filter params (stdDeviation) | ✗ | ✓ | ✓ (.attr()) | ✗ |
| r, cx, cy, rx attributes | ✗ | ✓ | ✓ (.attr()) | ✓ |
| Scroll-linked SVG | ✗ | ✗ | ✓ (ScrollTrigger) | ✗ |

✓ = supported | ✗ = not supported / not recommended

## SMIL — Legacy Reference (Do Not Produce)

**What it is:** XML-embedded animation via `<animate>`, `<animateTransform>`, `<animateMotion>` tags inside SVG markup.

**Why not produced:** Safari does not GPU-accelerate SMIL animations. IE never supported it. CSS and JS libraries cover all use cases with better performance and tooling.

**When you'll encounter it:** Legacy SVGs from design tools (older Illustrator exports, SVG icon libraries pre-2015), open-source SVG animations on CodePen, Wikipedia diagrams.

**What to do:** If asked to work with existing SMIL — identify what it does, rewrite to CSS or JS equivalent. Do not extend SMIL-based animations.

```xml
<!-- SMIL pattern you may encounter — do not produce -->
<circle>
  <animate attributeName="r" from="10" to="50" dur="1s" repeatCount="indefinite"/>
</circle>

<!-- CSS equivalent — produce this instead -->
```
```css
@keyframes grow { to { r: 50px; } } /* r is not CSS-animatable — use transform: scale() */
circle { animation: grow 1s infinite; transform-box: fill-box; transform-origin: center; }
```

## transform-box: fill-box Pattern

Without fix — rotates around SVG `0 0` (top-left of entire viewBox):
```css
.icon { transform: rotate(45deg); } /* wrong origin */
```

With fix — rotates around element's own center:
```css
.icon {
  transform-box: fill-box;    /* bounding box = element geometry */
  transform-origin: center;   /* now relative to element, not viewBox */
  transform: rotate(45deg);
}
```

Thick stroke caveat — fill-box excludes stroke width:
```css
/* For a path with stroke-width: 8 — use explicit values */
.thick-path {
  transform-box: fill-box;
  transform-origin: 50% 50%; /* or specific px values if center drifts */
}
```

## Stroke-dashoffset Draw-on Pattern

```html
<svg viewBox="0 0 200 200">
  <path id="line" d="M10,100 Q100,10 190,100" fill="none" stroke="#333" stroke-width="2"/>
</svg>
```

```js
// After DOM mount — useEffect in React, DOMContentLoaded in vanilla
const path = document.getElementById('line');
const len = path.getTotalLength(); // e.g. 235.4px

// Set CSS custom property per-element (works for multiple paths with different lengths)
path.style.setProperty('--path-len', len);
```

```css
#line {
  stroke-dasharray: var(--path-len);
  stroke-dashoffset: var(--path-len); /* fully hidden */
  animation: draw 1.2s ease forwards;
}

@keyframes draw {
  to { stroke-dashoffset: 0; } /* fully drawn */
}
```

Reverse (erase effect): animate `stroke-dashoffset` from `0` to `var(--path-len)`.
Partial draw: animate to any value between 0 and path length.

### Library shortcuts
```js
// /anime-js — handles getTotalLength() automatically
import { animate, svg } from 'animejs';
const drawable = svg.createDrawable('#line');
animate(drawable, { draw: '0 1', duration: 1200, ease: 'inOutSine' });

// /gsap — DrawSVGPlugin (sequence control, scrub with ScrollTrigger)
import { gsap } from 'gsap';
import { DrawSVGPlugin } from 'gsap/DrawSVGPlugin';
gsap.registerPlugin(DrawSVGPlugin);
gsap.from('#line', { drawSVG: 0, duration: 1.2, ease: 'power2.inOut' });
```

## WAAPI on SVG — What Works and What Doesn't

```js
// ✓ CSS properties — works
path.animate([
  { opacity: 0, transform: 'scale(0)' },
  { opacity: 1, transform: 'scale(1)' }
], { duration: 600, easing: 'ease-out', fill: 'forwards' });

// ✗ SVG attributes — does NOT work with WAAPI
circle.animate([{ r: 10 }, { r: 50 }], { duration: 600 }); // r is an attribute, not a CSS property

// → Use /gsap for SVG attribute animation
gsap.to('circle', { attr: { r: 50 }, duration: 0.6 });
```

## viewBox Animation (Library Only)

```js
// /gsap — animate viewBox as attribute
gsap.to('svg', {
  attr: { viewBox: '50 50 100 100' }, // pan + zoom
  duration: 1,
  ease: 'power2.inOut'
});

// Vanilla JS — direct assignment in rAF loop
function panTo(svg, x, y, w, h, duration) {
  // Use requestAnimationFrame loop or WAAPI workaround
  // No clean CSS solution — use a library
}
```

## SVG Filter Animation (SMIL or JS Only)

```html
<!-- SMIL — encountered in the wild, do not produce -->
<feGaussianBlur stdDeviation="0">
  <animate attributeName="stdDeviation" from="0" to="10" dur="1s"/>
</feGaussianBlur>
```

```js
// JS — direct property mutation (vanilla, no library needed)
const blur = document.querySelector('feGaussianBlur');
let value = 0;
function animateBlur() {
  value += 0.5;
  blur.setAttribute('stdDeviation', value);
  if (value < 10) requestAnimationFrame(animateBlur);
}
animateBlur();

// /gsap — cleaner
gsap.to('feGaussianBlur', { attr: { stdDeviation: 10 }, duration: 1 });
```

## Performance Checklist

Report each: PASS / WARN / FAIL.

| # | Check | FAIL condition |
|---|-------|---------------|
| 1 | GPU properties only | `fill`, `stroke`, `d`, `x`, `y`, `width`, `height` animated (not `transform`/`opacity`) |
| 2 | transform-box applied | Rotating/scaling SVG element without `transform-box: fill-box` |
| 3 | transform-origin correct | Default `0 0` origin producing unexpected rotation pivot |
| 4 | stroke caveat noted | `transform-box: fill-box` used on thick-stroked element without explicit origin |
| 5 | draw calculation | getTotalLength() called before DOM mount (returns 0) |
| 6 | clip-path shape match | Different shape functions used across keyframes |
| 7 | SVG attribute injection | User-supplied data in `d`, `href`, `xlink:href`, filter `in`/`result` |
| 8 | SMIL in output | SMIL tags produced instead of CSS/JS equivalent |

## DEBUG — Common Issues

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| Element rotates from wrong point | Missing `transform-box: fill-box` | Add `transform-box: fill-box; transform-origin: center` |
| `getTotalLength()` returns 0 | Called before DOM mount | Move to `useEffect` / `DOMContentLoaded` |
| stroke-dashoffset doesn't animate | Missing `stroke-dasharray` | Set both `stroke-dasharray` and `stroke-dashoffset` to path length |
| clip-path snaps between states | Different shape functions | Match shape type: circle→circle, polygon→polygon |
| `r`/`cx`/`d` not animating via CSS | SVG attributes — not CSS properties | Route to /gsap `.attr()` or /anime-js |
| WAAPI `.animate()` has no effect on attribute | Attributes not supported in WAAPI | Use GSAP `.attr()` or direct JS mutation |
| Filter blur animation not working in CSS | `stdDeviation` is SVG attribute, not CSS | Use direct JS mutation or /gsap |
| Transform looks different in Firefox vs Chrome | `transform-origin` coordinate difference | Explicit `transform-box: fill-box` + explicit origin value |

## Research Sources

1. https://developer.mozilla.org/en-US/docs/Web/SVG/Element/animate — SMIL animate
2. https://css-tricks.com/svg-line-animation-works/ — stroke-dashoffset draw-on
3. https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API — WAAPI
4. https://www.sarasoueidan.com/blog/svg-transformations/ — transform-origin coordinate system
5. https://css-tricks.com/smil-is-dead-long-live-smil-a-guide-to-alternatives-to-smil-features/ — SMIL status
6. https://css-tricks.com/weighing-svg-animation-techniques-benchmarks/ — performance comparison
7. https://jakearchibald.com/2013/animated-line-drawing-svg/ — getTotalLength canonical pattern
8. https://www.smashingmagazine.com/2015/12/animating-clipped-elements-svg/ — clip-path animation
9. https://developer.mozilla.org/en-US/docs/Web/SVG/Element/feGaussianBlur — filter animation
10. https://www.motiontricks.com/basic-svg-viewbox-animation/ — viewBox animation approaches
