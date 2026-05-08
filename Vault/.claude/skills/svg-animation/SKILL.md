---
name: svg-animation
description: SVG animation builder covering CSS keyframes on SVG, stroke-dashoffset draw-on effects, and Web Animations API on SVG elements. Knows when to route to /gsap or /anime-js for library power. Use when animating SVG fills, strokes, transforms, clip-paths, or draw-on line effects without or with a library.
argument-hint: "[mode: write|draw|review|debug] [description] â€” add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
Three animation territories â€” this skill covers all of them:
- **CSS/WAAPI** â€” CSS keyframes and Web Animations API on SVG elements (library-free)
- **Library-assisted** â€” routes to /gsap or /anime-js when needed (morphing, viewBox, motion path, filter params)
- **SMIL** â€” legacy reference only. In REFERENCE.md. Never produced as output.

For: fill/stroke/opacity animations, draw-on effects, clip-path, transform sequences.
Not for: canvas animation, Lottie (use /lottie), path morphing d attribute (route to /gsap or /anime-js).

## Universal Fix â€” Always Apply
SVG `transform-origin` defaults to `0 0` (top-left of viewBox), not element center.
Fix: `transform-box: fill-box; transform-origin: center;` on any SVG element being rotated or scaled.
Caveat: `transform-box: fill-box` uses geometric bounding box â€” excludes stroke width. For thick-stroked elements, use explicit pixel values instead of `center`.

## Modes

**WRITE** â€” CSS keyframes and WAAPI on SVG elements.
Animatable via CSS: `fill`, `stroke`, `stroke-width`, `opacity`, `transform`, `clip-path`, `filter` (not filter params â€” see below).
Not animatable via CSS: SVG attributes (`r`, `cx`, `cy`, `d`, `viewBox`, `stdDeviation`). For these â†’ route to /gsap `.attr()` or /anime-js `animate()`.
WAAPI: `element.animate([{...}, {...}], options)` works on SVG CSS properties. Same attribute limitation applies.
Transform: always add `transform-box: fill-box` before `transform-origin`. See Universal Fix above.
clip-path: animates smoothly between keyframes only when shape function matches (circleâ†’circle, polygonâ†’polygon). Mixed types snap â€” do not mix.
SVG filter shorthand (`filter: blur(4px)`) is CSS-animatable. Individual filter params (`feGaussianBlur stdDeviation`) are not â€” route to /gsap or SMIL.
NON-DEV: explain the SVG coordinate system difference before writing transforms.
End with: propose `/draw` for stroke effects, `/review` for performance audit.

**DRAW** â€” Stroke-dashoffset draw-on effect.
Requires: `stroke`, `stroke-dasharray`, `stroke-dashoffset`, `fill: none` on the path.
Step 1 â€” get path length: `const len = path.getTotalLength()` â€” must run after DOM mount (useEffect/DOMContentLoaded). Returns 0 if called before mount.
Step 2 â€” set initial state: `stroke-dasharray: var(--path-len); stroke-dashoffset: var(--path-len)`.
Step 3 â€” animate to 0: `@keyframes draw { to { stroke-dashoffset: 0; } }`.
CSS custom property pattern: set `--path-len` via JS after mount â€” allows per-element dynamic values without hard-coding.
Library option: /anime-js `svg.createDrawable()` handles steps 1â€“3 automatically. /gsap `DrawSVGPlugin` for sequence control.
Percentage: not supported for stroke-dashoffset â€” always use px (user units from getTotalLength).
NON-DEV: explain the "invisible ruler" mental model before writing code.
End with: propose `/review` for performance audit.

**REVIEW** â€” Performance and security audit. Report each: PASS / WARN / FAIL.
Check categories: (1) GPU vs CPU properties, (2) transform-box/transform-origin correctness, (3) draw calculation accuracy, (4) clip-path shape-type consistency, (5) security â€” SVG attribute injection.
Full checklist in REFERENCE.md.
End with: propose `/qa` when clean.

**DEBUG** â€” Structured diagnosis for broken SVG animations.
Open with these 4 intake questions before diagnosing:
  1. What should animate that isn't â€” or what is animating wrong?
  2. Is this CSS keyframes, WAAPI, or a library?
  3. Is it a transform/rotation issue or a stroke/draw issue?
  4. Any console errors? (paste them)
Common causes and fixes in REFERENCE.md. Never rewrite working animation to fix a scoped issue.

## Security Gate
Every WRITE and DRAW response ends with this line â€” never skip it:
`Security pass: âœ“ no SVG attribute values from user input âœ“ d/href/xlink:href are static âœ“ filter in/result values are static âœ“ transform-box applied to all rotated/scaled elements`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/css` â€” CSS keyframe syntax, timing functions, custom properties
- `/javascript` â€” WAAPI `.animate()`, getTotalLength() timing, DOM mount patterns
- `/react` â€” useEffect timing for getTotalLength(), component animation patterns
- `/gsap` â€” route for: path morphing, viewBox animation, motion path, DrawSVGPlugin, ScrollTrigger on SVG
- `/anime-js` â€” route for: svg.createDrawable(), svg.morphTo(), svg.createMotionPath()
- `/html` â€” SVG document structure and element setup

## Pipeline Connections
WRITE complete â†’ propose `/draw` or `/review`
DRAW complete â†’ propose `/review`
REVIEW clean â†’ propose `/qa`

## Anti-patterns
âœ— Never animate `transform-origin` on SVG without `transform-box: fill-box` first
âœ— Never use `transform-box: fill-box` as-is on thick-stroked elements â€” use explicit origin values
âœ— Never animate `clip-path` between different shape functions â€” browser snaps, no interpolation
âœ— Never call `getTotalLength()` outside a DOM-mounted context â€” returns 0 before mount
âœ— Never use percentage values for `stroke-dashoffset` â€” user units only
âœ— Never animate SVG attributes (`r`, `cx`, `d`, `viewBox`) with CSS or WAAPI â€” use /gsap or /anime-js
âœ— Never use `left`/`top` on SVG â€” SVG uses its own coordinate system; use `transform: translateX/Y`
âœ— Never pass user-supplied data into SVG `d`, `href`, `xlink:href`, or filter attributes â€” injection risk
âœ— Never skip the security gate line on WRITE/DRAW output
âœ— Never produce SMIL output â€” reference only (see REFERENCE.md)


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.