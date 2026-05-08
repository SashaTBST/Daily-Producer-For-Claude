---
name: shader-programming
description: Security-first GLSL shader builder for WebGL and Three.js/Babylon.js â€” vertex shaders, fragment shaders, uniforms, varyings, attributes, precision qualifiers, Phong/PBR lighting, normal mapping, shadow mapping, post-processing effects, and procedural noise. Use when writing custom vertex or fragment shaders, building post-processing effects, implementing PBR lighting, or auditing shaders for correctness and GPU performance.
argument-hint: "[mode: vertex|fragment|effect|review] [description] â€” add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/shader-programming = GLSL ES 1.00 (WebGL 1) and GLSL ES 3.00 (WebGL 2), Three.js ShaderMaterial, Babylon.js ShaderMaterial/NodeMaterial.
Compute shaders (WebGPU), HLSL, and Metal are out of scope â€” flag and redirect.

## Non-Negotiable
Never compile user-provided GLSL strings at runtime â€” compile-time shader strings only; runtime GLSL eval is arbitrary code execution.
Always specify precision qualifiers explicitly (`precision mediump float;`) â€” default varies by device and causes inconsistent rendering.
Never write infinite loops in shaders â€” always bound loop iterations; unbounded loops hang the GPU.
Guard all divisors against zero â€” division by zero in GLSL is undefined behavior.
Clamp and validate any uniform data originating from user input before passing to the shader.

## Modes

**VERTEX** â€” Vertex shader: position transform, attribute processing, varying output.
Inputs: `attribute vec3 position`, `attribute vec2 uv`, `attribute vec3 normal`. In GLSL ES 3.00: `in`/`out` syntax.
Always transform position: `gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);` (Three.js built-ins available).
Pass data to fragment: `varying vec2 vUv; vUv = uv;` â€” prefix varyings with `v` by convention.
Skinning/morphing: use Three.js `#include <skinning_vertex>` chunks rather than hand-rolling.
NON-DEV: explain the vertex shader's job (transforming 3D points to 2D screen coordinates) before code.
End with: propose FRAGMENT mode for the paired fragment shader.

**FRAGMENT** â€” Fragment shader: lighting, texturing, colour output.
Precision: `precision mediump float;` at top â€” always. Use `highp` only where needed (depth calculations).
Texturing: `uniform sampler2D map; vec4 colour = texture2D(map, vUv);` (GLSL ES 1.00) or `texture(map, vUv)` (3.00).
Lighting: implement Phong (ambient + diffuse + specular) or import Three.js lighting chunks via `#include <lights_phong_fragment>`.
Alpha: `gl_FragColor.a` â€” set explicitly; premultiplied alpha issues are common.
NON-DEV: explain the fragment shader's job (computing the colour of each pixel) before code.
End with: propose EFFECT mode for post-processing, or REVIEW when complete.

**EFFECT** â€” Post-processing effects via Three.js EffectComposer or raw framebuffers.
Three.js: `EffectComposer` â†’ `RenderPass` â†’ custom `ShaderPass`. Pass `tDiffuse` uniform for the render target.
Common effects: bloom (threshold + blur), FXAA (anti-aliasing), chromatic aberration, vignette, CRT scanlines.
Framebuffer pattern: render scene to `WebGLRenderTarget` â†’ sample in post shader as `uniform sampler2D`.
Performance: run post-processing at half resolution where quality allows; always measure GPU frame time.
NON-DEV: explain that post-processing runs a full-screen quad after the main scene renders.
End with: propose REVIEW mode when effects are assembled.

**REVIEW** â€” Correctness + performance audit. Report each item: PASS / WARN / FAIL.
Flags: missing precision qualifier, unbounded loops, division by zero without guard, user GLSL strings compiled at runtime, uniforms not validated before shader pass, highp used unnecessarily (mobile perf), alpha not set explicitly.
End with: propose `/qa` when clean.

## Security Gate
Every VERTEX, FRAGMENT, and EFFECT response ends with this line â€” never skip it:
`Security pass: âœ“ no runtime GLSL eval âœ“ precision qualifiers explicit âœ“ loops bounded âœ“ divisors guarded âœ“ uniforms validated before pass`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/webgl` â€” raw WebGL context when using shaders without Three.js
- `/threejs` â€” ShaderMaterial and EffectComposer integration
- `/babylonjs` â€” ShaderMaterial and NodeMaterial integration
- `/qa` â€” propose after REVIEW passes clean

## Pipeline Connections
VERTEX complete â†’ propose FRAGMENT for paired shader
FRAGMENT complete â†’ propose EFFECT if post-processing needed
EFFECT complete â†’ propose REVIEW
REVIEW clean â†’ propose `/qa`
/qa passes â†’ portable sync + commit

## Anti-patterns
âœ— Never compile user-provided GLSL strings at runtime
âœ— Never omit precision qualifiers â€” always declare explicitly
âœ— Never write unbounded loops in shaders
âœ— Never divide without guarding the divisor against zero
âœ— Never use `highp` globally on mobile â€” measure first
âœ— Never pass unvalidated user input as shader uniforms
âœ— Never skip the security gate line on VERTEX, FRAGMENT, or EFFECT output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.