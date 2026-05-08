---
name: threejs
description: Security-first Three.js r168+ builder covering Scene, Camera, Renderer, BufferGeometry, Materials (Standard/Shader), Lights, GLTFLoader, OrbitControls, animation loop, and postprocessing. Use when building browser-based 3D scenes, loading GLTF/GLB assets, writing custom ShaderMaterial, or auditing a Three.js scene for memory leaks and security issues.
argument-hint: "[mode: scene|shader|load|review] [description] â€” add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/threejs = Three.js r168+, WebGLRenderer, core scene graph, GLTF pipeline, custom shaders, postprocessing.
React Three Fiber, server-side rendering, and native mobile are out of scope â€” use `/react` for R3F.

## Non-Negotiable
Always call `geometry.dispose()`, `material.dispose()`, `texture.dispose()` when removing objects â€” GPU memory leaks are silent and accumulate.
Never load assets from user-provided URLs without allowlist validation â€” SSRF risk in SSR contexts.
Cross-origin textures: set `loader.crossOrigin = 'anonymous'` before loading â€” omitting taints the renderer.
Never eval user-provided GLSL strings â€” compile-time only; runtime GLSL eval is arbitrary code execution.
Always call `renderer.dispose()` and `renderer.forceContextLoss()` on unmount.

## Modes

**SCENE** â€” Scene setup, cameras, geometry, materials, lights.
Boilerplate: `WebGLRenderer` â†’ `PerspectiveCamera` â†’ `Scene` â†’ lights â†’ mesh â†’ RAF loop.
Camera: `PerspectiveCamera(fov, aspect, near, far)` â€” always update `aspect` and call `updateProjectionMatrix()` on resize.
Materials: `MeshStandardMaterial` for PBR default; `MeshBasicMaterial` for unlit/debug. Set `side: THREE.FrontSide` explicitly.
Lights: `AmbientLight` as baseline; `DirectionalLight` for shadows â€” enable `castShadow` and set `shadow.mapSize` deliberately.
NON-DEV: explain scene graph (parent/child transforms) on first use.
End with: propose LOAD mode if external assets needed.

**SHADER** â€” Custom ShaderMaterial with GLSL ES 3.00.
Structure: `uniforms` object â†’ `vertexShader` string â†’ `fragmentShader` string â†’ `ShaderMaterial`.
Uniforms: update via `material.uniforms.myVar.value = x` each frame â€” never reassign the uniforms object.
Precision: always declare `precision mediump float;` â€” never rely on default (varies by device).
Never eval user-provided GLSL. Always bound loop iterations. Guard all divisors against zero.
NON-DEV: explain uniforms (values passed from JS to GPU) and varyings (passed between vertex and fragment).
End with: propose REVIEW mode when shader is working.

**LOAD** â€” GLTF/GLB asset loading with DRACOLoader and progress tracking.
Pattern: `DRACOLoader` â†’ `GLTFLoader.setDRACOLoader()` â†’ `loader.load(url, onLoad, onProgress, onError)`.
Always handle `onError` â€” silent failures leave blank scenes with no feedback.
Cross-origin: `loader.crossOrigin = 'anonymous'` before `.load()`.
URL validation: allowlist known asset origins â€” never pass user input directly to `loader.load()`.
NON-DEV: explain why Draco compression reduces file size but requires the decoder.
End with: propose REVIEW mode when load pipeline is set up.

**REVIEW** â€” Performance + security audit. Report each item: PASS / WARN / FAIL.
Flags: missing dispose calls, renderer not disposed on unmount, user URLs in loader, missing crossOrigin, unbounded draw calls, shadow map size not set, no error handler in loader, ShaderMaterial with user GLSL.
End with: propose `/qa` when clean.

## Security Gate
Every SCENE, SHADER, and LOAD response ends with this line â€” never skip it:
`Security pass: âœ“ dispose on removal âœ“ renderer disposed on unmount âœ“ no user URLs in loader âœ“ crossOrigin set âœ“ no eval GLSL`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/webgl` â€” raw WebGL when Three.js abstraction is too heavy
- `/shader-programming` â€” deep GLSL work beyond ShaderMaterial basics
- `/react` â€” React Three Fiber integration
- `/qa` â€” propose after REVIEW passes clean

## Pipeline Connections
SCENE complete â†’ propose LOAD if external assets needed
LOAD complete â†’ propose SHADER if custom materials needed
SHADER complete â†’ propose REVIEW
REVIEW clean â†’ propose `/qa`
/qa passes â†’ portable sync + commit

## Anti-patterns
âœ— Never skip `dispose()` on geometry, material, or texture removal
âœ— Never load from user-provided URLs without allowlist
âœ— Never omit `crossOrigin` on cross-origin asset loaders
âœ— Never eval user GLSL strings
âœ— Never forget to update `camera.aspect` and call `updateProjectionMatrix()` on resize
âœ— Never leave `renderer` alive after component unmount
âœ— Never skip the security gate line on SCENE, SHADER, or LOAD output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.