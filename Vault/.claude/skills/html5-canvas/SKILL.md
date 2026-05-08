---
name: html5-canvas
description: Security-first HTML5 Canvas 2D API builder covering CanvasRenderingContext2D, shapes, paths, text, images, transforms, compositing, requestAnimationFrame animation loops, and pixel manipulation via ImageData. Use when drawing to canvas, building a 2D game loop, applying pixel filters, or reviewing canvas code for cross-origin and memory issues.
argument-hint: "[mode: draw|animate|image|review] [description] — add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/html5-canvas = CanvasRenderingContext2D, OffscreenCanvas, ImageData, requestAnimationFrame, Path2D.
WebGL, Three.js, and SVG animation are out of scope — use `/webgl`, `/threejs`, or `/svg-animation`.

## Non-Negotiable
Never use user-provided strings in `eval()` or `innerHTML` — XSS risk adjacent to canvas.
Cross-origin images taint the canvas — always set `img.crossOrigin = 'anonymous'` before `src`; never call `getImageData()` on a tainted canvas.
Never expose pixel data from cross-origin sources — `toDataURL()` and `getImageData()` throw SecurityError on tainted canvas.
Always call `cancelAnimationFrame(rafId)` on cleanup — orphaned RAF loops are memory leaks.
Sanitize any user-supplied text drawn via `fillText()` — no HTML injection path in canvas, but log intent clearly for auditability.

## Modes

**DRAW** — Shapes, paths, text, transforms, compositing.
Primitives: `fillRect`, `strokeRect`, `arc`, `bezierCurveTo`. Always `beginPath()` before a new path — forgetting it compounds prior paths.
Text: `fillText` with `font`, `textAlign`, `textBaseline` set explicitly. Measure with `measureText()`.
Transforms: `save()` / `restore()` wrap every transform block — never leave state dirty.
NON-DEV: explain the painter's model (later draws are on top) and why `save/restore` matters.
End with: propose ANIMATE mode if motion is needed.

**ANIMATE** — Game loop with requestAnimationFrame.
Pattern: `let rafId; function loop(ts) { ctx.clearRect(0,0,w,h); update(ts); draw(); rafId = requestAnimationFrame(loop); } rafId = requestAnimationFrame(loop);`
Delta time: pass timestamp from RAF, compute `dt = ts - lastTs` — never assume fixed 60 fps.
Cleanup: expose `cancelAnimationFrame(rafId)` for unmount/pause.
NON-DEV: explain why RAF syncs to display refresh and why `setInterval` is wrong for animation.
End with: propose IMAGE mode if pixel manipulation needed.

**IMAGE** — Pixel manipulation with ImageData.
`getImageData` → RGBA array → mutate → `putImageData`. Batch operations — never call per frame without caching.
OffscreenCanvas: use for heavy pixel work off the main thread (Worker + `transferControlToOffscreen`).
Cross-origin guard: always confirm `img.crossOrigin = 'anonymous'` set before loading.
NON-DEV: explain RGBA byte layout (4 bytes per pixel) and why cross-origin blocks pixel access.
End with: propose REVIEW mode when implementation is complete.

**REVIEW** — Performance + security audit. Report each item: PASS / WARN / FAIL.
Flags: missing `cancelAnimationFrame`, fixed-fps assumptions, `getImageData` on tainted canvas, `beginPath()` missing before paths, `save/restore` mismatch, large ImageData ops on main thread, missing crossOrigin attribute.
End with: propose `/qa` when clean.

## Security Gate
Every DRAW, ANIMATE, and IMAGE response ends with this line — never skip it:
`Security pass: ✓ no eval/innerHTML near canvas ✓ crossOrigin set on external images ✓ no getImageData on tainted canvas ✓ cancelAnimationFrame on cleanup`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/javascript` — base JS patterns for event handling and game loop logic
- `/webgl` — propose when 3D or GPU shaders are needed
- `/svg-animation` — propose for resolution-independent vector animation

## Pipeline Connections
DRAW complete → propose ANIMATE if motion needed
ANIMATE complete → propose IMAGE if pixel ops needed
IMAGE complete → propose REVIEW
REVIEW clean → propose `/qa`
/qa passes → portable sync + commit

## Anti-patterns
✗ Never use `setInterval` or `setTimeout` for animation — use `requestAnimationFrame`
✗ Never call `getImageData` without confirming crossOrigin is set on all source images
✗ Never forget `beginPath()` before a new path shape
✗ Never leave canvas transform state dirty — always `save/restore`
✗ Never assume 60 fps — always use delta time from RAF timestamp
✗ Never skip `cancelAnimationFrame` on component unmount or pause
✗ Never skip the security gate line on DRAW, ANIMATE, or IMAGE output
