---
name: asset-management
description: Game and 3D asset pipeline builder for browser-based apps — loaders, caching, streaming, LOD, memory budgets, and disposal patterns using Three.js and WebGL asset tooling. Use when loading GLTFs/textures/audio, setting up a LoadingManager, implementing progressive streaming or LOD, auditing GPU memory leaks, or reviewing asset security.
argument-hint: "[mode: load|cache|stream|review] [description] — add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/asset-management = GLTFLoader, TextureLoader, AudioLoader, LoadingManager, texture atlases, LOD, caching strategies, memory budget management, disposal patterns, CDN integration.
Unity/Unreal asset pipelines, server-side asset processing, and non-browser runtimes are out of scope.

## Non-Negotiable
Never load from user-provided URLs without allowlist validation — SSRF risk.
Always set `crossOrigin = 'anonymous'` on cross-origin textures and models.
Validate file type and MIME type before loading — reject unexpected types.
Enforce file size limits on uploads to prevent DoS.
Always dispose: `geometry.dispose()`, `material.dispose()`, `texture.dispose()` — GPU memory leaks are silent and fatal.
CDN assets: apply subresource integrity (SRI) where the Fetch API allows.
Never cache signed CDN tokens in localStorage — in-memory only.

## Modes

**LOAD** — Loader setup + progress tracking.
GLTFLoader with DRACOLoader for compressed meshes. TextureLoader with `onProgress` callbacks. `LoadingManager` for coordinated multi-asset loading with progress UI.
Always handle `onError` — silent failures leave blank scenes with no diagnostic output.
Cross-origin: `loader.setCrossOrigin('anonymous')` before `.load()`.
URL validation: allowlist known asset origins — never pass user input directly to `loader.load()`.
NON-DEV: explain the loader chain and what each callback (`onLoad`, `onProgress`, `onError`) does.
End with: propose CACHE mode if assets will be loaded more than once.

**CACHE** — Caching strategy.
In-memory `Map` cache keyed by URL. LRU eviction when memory budget exceeded — track total texture memory in bytes.
Never cache to `localStorage` or `IndexedDB` without stripping signed tokens from URLs.
Cache hit → skip loader, reuse existing object. Cache miss → load → insert → evict LRU if over budget.
`THREE.Cache` (built-in): enable with `THREE.Cache.enabled = true` for automatic XHR-level caching.
NON-DEV: explain LRU eviction and why memory budget prevents crashes on low-end devices.
End with: propose STREAM mode for large scenes.

**STREAM** — Progressive loading + LOD.
`THREE.LOD`: add levels with `lod.addLevel(mesh, distance)` — lower detail at greater distance.
Load low-res first, swap high-res on proximity. Frustum culling check before loading off-screen assets.
Zone-based streaming: divide scene into zones, load on entry, dispose on exit via `zone.dispose()`.
NON-DEV: explain LOD distance tiers and why frustum culling avoids loading assets the camera can't see.
End with: propose REVIEW mode to audit the full pipeline.

**REVIEW** — Memory + security audit. Report each item: PASS / WARN / FAIL.
Flags: missing dispose calls, unbounded cache, user-URL loading without allowlist, missing crossOrigin, no MIME validation, no file size limit, signed CDN tokens in persistent storage, no onError handler.
End with: propose `/qa` when clean.

## Security Gate
Every LOAD, CACHE, and STREAM response ends with this line — never skip it:
`Security pass: ✓ URL allowlist checked ✓ crossOrigin set ✓ MIME validated ✓ size limit enforced ✓ disposal pattern present ✓ no tokens in persistent storage`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/threejs` — Three.js scene and renderer integration
- `/babylonjs` — Babylon.js AssetManager integration
- `/javascript` — base loader callback and event handling patterns
- `/qa` — propose after REVIEW passes clean

## Pipeline Connections
LOAD complete → propose CACHE if reuse expected
CACHE complete → propose STREAM for large scenes
STREAM complete → propose REVIEW
REVIEW clean → propose `/qa`
/qa passes → portable sync + commit

## Anti-patterns
✗ Never load user-provided URLs without allowlist validation
✗ Never skip crossOrigin on cross-origin assets
✗ Never load assets without MIME type validation
✗ Never allow unbounded cache growth — always set a memory budget
✗ Never store signed CDN tokens in localStorage or IndexedDB
✗ Never skip dispose() — GPU memory leaks are silent and fatal
✗ Never load entire scenes upfront — stream and cull
✗ Never skip the security gate line on LOAD, CACHE, or STREAM output


Every response ends with NEXT MOVE.