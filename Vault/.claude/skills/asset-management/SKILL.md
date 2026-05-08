---
name: asset-management
description: Game and 3D asset pipeline builder for browser-based apps â€” loaders, caching, streaming, LOD, memory budgets, and disposal patterns using Three.js and WebGL asset tooling. Use when loading GLTFs/textures/audio, setting up a LoadingManager, implementing progressive streaming or LOD, auditing GPU memory leaks, or reviewing asset security.
argument-hint: "[mode: load|cache|stream|review] [description] â€” add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/asset-management = GLTFLoader, TextureLoader, AudioLoader, LoadingManager, texture atlases, LOD, caching strategies, memory budget management, disposal patterns, CDN integration.
Unity/Unreal asset pipelines, server-side asset processing, and non-browser runtimes are out of scope.

## Non-Negotiable
Never load from user-provided URLs without allowlist validation â€” SSRF risk.
Always set `crossOrigin = 'anonymous'` on cross-origin textures and models.
Validate file type and MIME type before loading â€” reject unexpected types.
Enforce file size limits on uploads to prevent DoS.
Always dispose: `geometry.dispose()`, `material.dispose()`, `texture.dispose()` â€” GPU memory leaks are silent and fatal.
CDN assets: apply subresource integrity (SRI) where the Fetch API allows.
Never cache signed CDN tokens in localStorage â€” in-memory only.

## Modes

**LOAD** â€” Loader setup + progress tracking.
GLTFLoader with DRACOLoader for compressed meshes. TextureLoader with `onProgress` callbacks. `LoadingManager` for coordinated multi-asset loading with progress UI.
Always handle `onError` â€” silent failures leave blank scenes with no diagnostic output.
Cross-origin: `loader.setCrossOrigin('anonymous')` before `.load()`.
URL validation: allowlist known asset origins â€” never pass user input directly to `loader.load()`.
NON-DEV: explain the loader chain and what each callback (`onLoad`, `onProgress`, `onError`) does.
End with: propose CACHE mode if assets will be loaded more than once.

**CACHE** â€” Caching strategy.
In-memory `Map` cache keyed by URL. LRU eviction when memory budget exceeded â€” track total texture memory in bytes.
Never cache to `localStorage` or `IndexedDB` without stripping signed tokens from URLs.
Cache hit â†’ skip loader, reuse existing object. Cache miss â†’ load â†’ insert â†’ evict LRU if over budget.
`THREE.Cache` (built-in): enable with `THREE.Cache.enabled = true` for automatic XHR-level caching.
NON-DEV: explain LRU eviction and why memory budget prevents crashes on low-end devices.
End with: propose STREAM mode for large scenes.

**STREAM** â€” Progressive loading + LOD.
`THREE.LOD`: add levels with `lod.addLevel(mesh, distance)` â€” lower detail at greater distance.
Load low-res first, swap high-res on proximity. Frustum culling check before loading off-screen assets.
Zone-based streaming: divide scene into zones, load on entry, dispose on exit via `zone.dispose()`.
NON-DEV: explain LOD distance tiers and why frustum culling avoids loading assets the camera can't see.
End with: propose REVIEW mode to audit the full pipeline.

**REVIEW** â€” Memory + security audit. Report each item: PASS / WARN / FAIL.
Flags: missing dispose calls, unbounded cache, user-URL loading without allowlist, missing crossOrigin, no MIME validation, no file size limit, signed CDN tokens in persistent storage, no onError handler.
End with: propose `/qa` when clean.

## Security Gate
Every LOAD, CACHE, and STREAM response ends with this line â€” never skip it:
`Security pass: âœ“ URL allowlist checked âœ“ crossOrigin set âœ“ MIME validated âœ“ size limit enforced âœ“ disposal pattern present âœ“ no tokens in persistent storage`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/threejs` â€” Three.js scene and renderer integration
- `/babylonjs` â€” Babylon.js AssetManager integration
- `/javascript` â€” base loader callback and event handling patterns
- `/qa` â€” propose after REVIEW passes clean

## Pipeline Connections
LOAD complete â†’ propose CACHE if reuse expected
CACHE complete â†’ propose STREAM for large scenes
STREAM complete â†’ propose REVIEW
REVIEW clean â†’ propose `/qa`
/qa passes â†’ portable sync + commit

## Anti-patterns
âœ— Never load user-provided URLs without allowlist validation
âœ— Never skip crossOrigin on cross-origin assets
âœ— Never load assets without MIME type validation
âœ— Never allow unbounded cache growth â€” always set a memory budget
âœ— Never store signed CDN tokens in localStorage or IndexedDB
âœ— Never skip dispose() â€” GPU memory leaks are silent and fatal
âœ— Never load entire scenes upfront â€” stream and cull
âœ— Never skip the security gate line on LOAD, CACHE, or STREAM output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.