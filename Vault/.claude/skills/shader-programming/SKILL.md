---
name: shader-programming
description: Security-first GLSL shader builder for WebGL and Three.js/Babylon.js — vertex shaders, fragment shaders, uniforms, varyings, attributes, precision qualifiers, Phong/PBR lighting, normal mapping, shadow mapping, post-processing effects, and procedural noise. Use when writing custom vertex or fragment shaders, building post-processing effects, implementing PBR lighting, or auditing shaders for correctness and GPU performance.
argument-hint: "[mode: vertex|fragment|effect|review] [description] — add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/shader-programming = GLSL ES 1.00 (WebGL 1) and GLSL ES 3.00 (WebGL 2), Three.js ShaderMaterial, Babylon.js ShaderMaterial/NodeMaterial.
Compute shaders (WebGPU), HLSL, and Metal are out of scope — flag and redirect.

## Non-Negotiable
Never compile user-provided GLSL strings at runtime — compile-time shader strings only; runtime GLSL eval is arbitrary code execution.
Always specify precision qualifiers explicitly (`precision mediump float;`) — default varies by device and causes inconsistent rendering.
Never write infinite loops in shaders — always bound loop iterations; unbounded loops hang the GPU.
Guard all divisors against zero — division by zero in GLSL is undefined behavior.
Clamp and validate any uniform data originating from user input before passing to the shader.

## Modes

**VERTEX** — Vertex shader: position transform, attribute processing, varying output.
Inputs: `attribute vec3 position`, `attribute vec2 uv`, `attribute vec3 normal`. In GLSL ES 3.00: `in`/`out` syntax.
Always transform position: `gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);` (Three.js built-ins available).
Pass data to fragment: `varying vec2 vUv; vUv = uv;` — prefix varyings with `v` by convention.
Skinning/morphing: use Three.js `#include <skinning_vertex>` chunks rather than hand-rolling.
NON-DEV: explain the vertex shader's job (transforming 3D points to 2D screen coordinates) before code.
End with: propose FRAGMENT mode for the paired fragment shader.

**FRAGMENT** — Fragment shader: lighting, texturing, colour output.
Precision: `precision mediump float;` at top — always. Use `highp` only where needed (depth calculations).
Texturing: `uniform sampler2D map; vec4 colour = texture2D(map, vUv);` (GLSL ES 1.00) or `texture(map, vUv)` (3.00).
Lighting: implement Phong (ambient + diffuse + specular) or import Three.js lighting chunks via `#include <lights_phong_fragment>`.
Alpha: `gl_FragColor.a` — set explicitly; premultiplied alpha issues are common.
NON-DEV: explain the fragment shader's job (computing the colour of each pixel) before code.
End with: propose EFFECT mode for post-processing, or REVIEW when complete.

**EFFECT** — Post-processing effects via Three.js EffectComposer or raw framebuffers.
Three.js: `EffectComposer` → `RenderPass` → custom `ShaderPass`. Pass `tDiffuse` uniform for the render target.
Common effects: bloom (threshold + blur), FXAA (anti-aliasing), chromatic aberration, vignette, CRT scanlines.
Framebuffer pattern: render scene to `WebGLRenderTarget` → sample in post shader as `uniform sampler2D`.
Performance: run post-processing at half resolution where quality allows; always measure GPU frame time.
NON-DEV: explain that post-processing runs a full-screen quad after the main scene renders.
End with: propose REVIEW mode when effects are assembled.

**REVIEW** — Correctness + performance audit. Report each item: PASS / WARN / FAIL.
Flags: missing precision qualifier, unbounded loops, division by zero without guard, user GLSL strings compiled at runtime, uniforms not validated before shader pass, highp used unnecessarily (mobile perf), alpha not set explicitly.
End with: propose `/qa` when clean.

## Security Gate
Every VERTEX, FRAGMENT, and EFFECT response ends with this line — never skip it:
`Security pass: ✓ no runtime GLSL eval ✓ precision qualifiers explicit ✓ loops bounded ✓ divisors guarded ✓ uniforms validated before pass`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/webgl` — raw WebGL context when using shaders without Three.js
- `/threejs` — ShaderMaterial and EffectComposer integration
- `/babylonjs` — ShaderMaterial and NodeMaterial integration
- `/qa` — propose after REVIEW passes clean

## Pipeline Connections
VERTEX complete → propose FRAGMENT for paired shader
FRAGMENT complete → propose EFFECT if post-processing needed
EFFECT complete → propose REVIEW
REVIEW clean → propose `/qa`
/qa passes → portable sync + commit

## Anti-patterns
✗ Never compile user-provided GLSL strings at runtime
✗ Never omit precision qualifiers — always declare explicitly
✗ Never write unbounded loops in shaders
✗ Never divide without guarding the divisor against zero
✗ Never use `highp` globally on mobile — measure first
✗ Never pass unvalidated user input as shader uniforms
✗ Never skip the security gate line on VERTEX, FRAGMENT, or EFFECT output
