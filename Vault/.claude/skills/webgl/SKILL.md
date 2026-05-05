---
name: webgl
description: Security-first WebGL 2.0 builder for GPU-accelerated graphics in the browser. Covers shaders (GLSL ES 3.00), buffers, textures, framebuffers, instanced rendering, and Three.js integration. Use when writing raw WebGL, GLSL shaders, or low-level GPU pipelines. Not for high-level 3D scenes (use /three-js) or CSS animations (use /css).
argument-hint: "[mode: write|shader|setup|review] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments on GPU/shader concepts.
Override: include `raw` for clean output without narration (for developers).

## Scope
/webgl = raw WebGL 2.0 API + GLSL ES 3.00 shaders.
High-level 3D scene → Three.js or Babylon.js. CSS/SVG animation → `/css` or `/svg-animation`.
Overlap is fine — /webgl writes the GPU layer, other skills handle their layer.

## Version
Default: WebGL 2.0 (universally supported, Chrome 56+, Firefox 51+, Safari 15+).
WebGL 1.0: only if legacy target stated explicitly — flag it clearly.
WebGPU: flag as emerging standard, note limited support (Chrome 113+), not covered here.
NON-DEV: comment every GLSL built-in and precision qualifier with plain-English meaning.

## Modes

**WRITE** — Initialise context, create buffers, compile shaders, draw calls, render loops.
Always request WebGL 2.0: `canvas.getContext('webgl2')`. Fallback to webgl1 only if stated.
Check `gl` for null after `getContext` — surface a user-facing error, not a silent crash.
Use `requestAnimationFrame` for render loops — never `setInterval`.
Clean up: call `gl.deleteBuffer`, `gl.deleteTexture`, `gl.deleteProgram` on teardown.
NON-DEV: plain-English comment on VAOs, VBOs, attribute locations, and uniform types.
End with: propose `/javascript` for app logic, `/css` for canvas styling.

**SHADER** — Write and review GLSL ES 3.00 vertex + fragment shader pairs.
Always start with `#version 300 es`. Always declare `precision mediump float` (or higher if required).
Use `in`/`out` (not `attribute`/`varying` — WebGL 1.0 syntax). Use `uniform` blocks for shared data.
Avoid undefined behaviour: initialise all variables, avoid division by zero, clamp where needed.
NON-DEV: explain each `uniform`, `in`, and `out` variable in a comment block above the shader.
Flag any texture lookup in a vertex shader — not supported on all hardware.

**SETUP** — New project scaffold: canvas setup + WebGL2 context + shader compilation boilerplate + Vite.
Output all files in one response with numbered terminal command sequence.
Include: `compileShader`, `createProgram`, `checkError` utility functions as a reusable module.
NON-DEV: include "how to start the project" block as comments above terminal commands.

**REVIEW** — Security and correctness audit.
Check: no unvalidated external shader source, no `eval`-constructed GLSL strings, texture origin policy (CORS), `preserveDrawingBuffer` risks (fingerprinting), context loss handling.
Full checklist: context null check, shader compile errors checked, link errors checked, resource cleanup, no synchronous `gl.finish` in hot path.
Report each: PASS / WARN / FAIL.
End with: propose `/javascript` security audit + `/qa` when clean.

## Security Gate
Every WRITE and SHADER response ends with this line — never skip it:
`Security pass: ✓ context null-checked ✓ shader compile/link errors checked ✓ no eval-constructed GLSL ✓ external textures CORS-validated ✓ resource cleanup on teardown`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/javascript` — app logic, event handling, asset loading
- `/css` — canvas sizing, responsive layout
- `/svg-animation` — for 2D vector animation not needing GPU
- `/gsap` — for timeline animation layered over canvas

## Pipeline Connections
WRITE complete → propose `/javascript` for app logic or `/css` for layout
REVIEW clean → propose `/qa`
/qa passes → propose portable sync + commit

## Anti-patterns
✗ Never call `getContext` without checking the return value for null
✗ Never construct GLSL source strings from user input — shader injection risk
✗ Never use `setInterval` for render loops — use `requestAnimationFrame`
✗ Never leave shaders, buffers, or textures allocated after component teardown — GPU memory leak
✗ Never use `gl.finish` in a render loop — synchronous GPU stall, kills performance
✗ Never load cross-origin textures without CORS headers — silent black texture or security error
✗ Never use `attribute`/`varying` in WebGL 2.0 shaders — WebGL 1.0 syntax, will not compile
✗ Never skip shader compile and program link error checks — silent failures are the norm
✗ Never skip the security gate line on WRITE or SHADER output
✗ Never recommend WebGL 1.0 for a new project — WebGL 2.0 is the default


Every response ends with NEXT MOVE.