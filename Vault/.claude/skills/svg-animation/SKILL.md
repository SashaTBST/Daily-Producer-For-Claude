---
name: svg-animation
description: SVG animation builder covering CSS keyframes on SVG, stroke-dashoffset draw-on effects, and Web Animations API on SVG elements. Knows when to route to /gsap or /anime-js for library power. Use when animating SVG fills, strokes, transforms, clip-paths, or draw-on line effects without or with a library.
argument-hint: "[mode: write|draw|review|debug] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
Three animation territories — this skill covers all of them:
- **CSS/WAAPI** — CSS keyframes and Web Animations API on SVG elements (library-free)
- **Library-assisted** — routes to /gsap or /anime-js when needed (morphing, viewBox, motion path, filter params)
- **SMIL** — legacy reference only. In REFERENCE.md. Never produced as output.

For: fill/stroke/opacity animations, draw-on effects, clip-path, transform sequences.
Not for: canvas animation, Lottie (use /lottie), path morphing d attribute (route to /gsap or /anime-js).

## Universal Fix — Always Apply
SVG `transform-origin` defaults to `0 0` (top-left of viewBox), not element center.
Fix: `transform-box: fill-box; transform-origin: center;` on any SVG element being rotated or scaled.
Caveat: `transform-box: fill-box` uses geometric bounding box — excludes stroke width. For thick-stroked elements, use explicit pixel values instead of `center`.

## Modes

**WRITE** — CSS keyframes and WAAPI on SVG elements.
Animatable via CSS: `fill`, `stroke`, `stroke-width`, `opacity`, `transform`, `clip-path`, `filter` (not filter params — see below).
Not animatable via CSS: SVG attributes (`r`, `cx`, `cy`, `d`, `viewBox`, `stdDeviation`). For these → route to /gsap `.attr()` or /anime-js `animate()`.
WAAPI: `element.animate([{...}, {...}], options)` works on SVG CSS properties. Same attribute limitation applies.
Transform: always add `transform-box: fill-box` before `transform-origin`. See Universal Fix above.
clip-path: animates smoothly between keyframes only when shape function matches (circle→circle, polygon→polygon). Mixed types snap — do not mix.
SVG filter shorthand (`filter: blur(4px)`) is CSS-animatable. Individual filter params (`feGaussianBlur stdDeviation`) are not — route to /gsap or SMIL.
NON-DEV: explain the SVG coordinate system difference before writing transforms.
End with: propose `/draw` for stroke effects, `/review` for performance audit.

**DRAW** — Stroke-dashoffset draw-on effect.
Requires: `stroke`, `stroke-dasharray`, `stroke-dashoffset`, `fill: none` on the path.
Step 1 — get path length: `const len = path.getTotalLength()` — must run after DOM mount (useEffect/DOMContentLoaded). Returns 0 if called before mount.
Step 2 — set initial state: `stroke-dasharray: var(--path-len); stroke-dashoffset: var(--path-len)`.
Step 3 — animate to 0: `@keyframes draw { to { stroke-dashoffset: 0; } }`.
CSS custom property pattern: set `--path-len` via JS after mount — allows per-element dynamic values without hard-coding.
Library option: /anime-js `svg.createDrawable()` handles steps 1–3 automatically. /gsap `DrawSVGPlugin` for sequence control.
Percentage: not supported for stroke-dashoffset — always use px (user units from getTotalLength).
NON-DEV: explain the "invisible ruler" mental model before writing code.
End with: propose `/review` for performance audit.

**REVIEW** — Performance and security audit. Report each: PASS / WARN / FAIL.
Check categories: (1) GPU vs CPU properties, (2) transform-box/transform-origin correctness, (3) draw calculation accuracy, (4) clip-path shape-type consistency, (5) security — SVG attribute injection.
Full checklist in REFERENCE.md.
End with: propose `/qa` when clean.

**DEBUG** — Structured diagnosis for broken SVG animations.
Open with these 4 intake questions before diagnosing:
  1. What should animate that isn't — or what is animating wrong?
  2. Is this CSS keyframes, WAAPI, or a library?
  3. Is it a transform/rotation issue or a stroke/draw issue?
  4. Any console errors? (paste them)
Common causes and fixes in REFERENCE.md. Never rewrite working animation to fix a scoped issue.

## Security Gate
Every WRITE and DRAW response ends with this line — never skip it:
`Security pass: ✓ no SVG attribute values from user input ✓ d/href/xlink:href are static ✓ filter in/result values are static ✓ transform-box applied to all rotated/scaled elements`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/css` — CSS keyframe syntax, timing functions, custom properties
- `/javascript` — WAAPI `.animate()`, getTotalLength() timing, DOM mount patterns
- `/react` — useEffect timing for getTotalLength(), component animation patterns
- `/gsap` — route for: path morphing, viewBox animation, motion path, DrawSVGPlugin, ScrollTrigger on SVG
- `/anime-js` — route for: svg.createDrawable(), svg.morphTo(), svg.createMotionPath()
- `/html` — SVG document structure and element setup

## Pipeline Connections
WRITE complete → propose `/draw` or `/review`
DRAW complete → propose `/review`
REVIEW clean → propose `/qa`

## Anti-patterns
✗ Never animate `transform-origin` on SVG without `transform-box: fill-box` first
✗ Never use `transform-box: fill-box` as-is on thick-stroked elements — use explicit origin values
✗ Never animate `clip-path` between different shape functions — browser snaps, no interpolation
✗ Never call `getTotalLength()` outside a DOM-mounted context — returns 0 before mount
✗ Never use percentage values for `stroke-dashoffset` — user units only
✗ Never animate SVG attributes (`r`, `cx`, `d`, `viewBox`) with CSS or WAAPI — use /gsap or /anime-js
✗ Never use `left`/`top` on SVG — SVG uses its own coordinate system; use `transform: translateX/Y`
✗ Never pass user-supplied data into SVG `d`, `href`, `xlink:href`, or filter attributes — injection risk
✗ Never skip the security gate line on WRITE/DRAW output
✗ Never produce SMIL output — reference only (see REFERENCE.md)
