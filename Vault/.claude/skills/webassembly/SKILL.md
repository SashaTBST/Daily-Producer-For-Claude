---
name: webassembly
description: Security-first WebAssembly builder covering WASM concepts, Rust→WASM via wasm-pack/wasm-bindgen, C/C++→WASM via Emscripten, JS↔WASM interop, browser and Node.js runtimes, WASI, and performance profiling. Use when compiling Rust or C to WASM, wiring JS↔WASM interop, debugging WASM modules, or auditing WASM for integrity and memory safety.
argument-hint: "[mode: rust|interop|debug|review] [description] — add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/webassembly = WASM binary format, Rust→WASM (wasm-pack), C/C++→WASM (Emscripten), JS interop, WASI, browser + Node.js runtimes.
Writing raw WAT (WebAssembly Text Format) by hand is out of scope — redirect to wasm-pack or Emscripten workflows.

## Non-Negotiable
Always verify WASM module integrity (subresource integrity hash) before loading from CDN — WASM executes at near-native privilege.
Never load WASM from user-controlled URLs — supply chain attack vector.
WASM linear memory is not automatically sandboxed — validate all inputs before passing to WASM functions.
WASI: filesystem access is capability-based — never grant broader than the minimum required capability.
wasm-bindgen: validate all JS values crossing the boundary — null/undefined can panic in Rust WASM.
Always handle `WebAssembly.instantiate` errors — silent failure leaves app in broken state.

## Modes

**RUST** — Rust→WASM via wasm-pack and wasm-bindgen.
Setup: `wasm-pack build --target web` → import generated JS glue → `await init()` before calling exports.
`#[wasm_bindgen]` on public functions and structs. `console_error_panic_hook` for readable panics in dev.
`wee_alloc` or default allocator — document choice. `#[wasm_bindgen(js_name)]` for camelCase JS API.
NON-DEV: explain that wasm-pack compiles Rust to a `.wasm` binary + JS glue file that handles the bridge.
End with: propose INTEROP mode to wire the JS↔WASM interface.

**INTEROP** — JS↔WASM bridge, memory passing, shared buffers.
Primitives (i32, f64) pass by value. Strings and arrays: wasm-bindgen serialises via `TextEncoder`/`TextDecoder` — document the copy cost.
SharedArrayBuffer: requires COOP/COEP headers (`Cross-Origin-Opener-Policy: same-origin`, `Cross-Origin-Embedder-Policy: require-corp`).
Never pass raw pointers from JS to WASM — use wasm-bindgen types or explicit memory views.
NON-DEV: explain that WASM memory is a flat byte array — JS and WASM share it via `WebAssembly.Memory`.
End with: propose DEBUG mode if interop issues arise.

**DEBUG** — WASM debugging tools and error diagnosis.
Browser DevTools: enable WASM source maps (`wasm-pack build --dev`). Chrome DevTools supports WASM stepping.
`console_error_panic_hook::set_once()` — call in `#[wasm_bindgen(start)]` for Rust panic messages.
Common errors: `RuntimeError: unreachable` (Rust panic), `LinkError` (missing import), `CompileError` (invalid binary).
`wasm-opt` and `twiggy` for size profiling. `wasm-snip` to remove unused functions.
NON-DEV: explain what each error type means in plain English before showing fix.
End with: propose REVIEW mode when module is working.

**REVIEW** — Security + integrity audit. Report each item: PASS / WARN / FAIL.
Flags: no SRI on CDN WASM, user-controlled WASM URL, unvalidated inputs before WASM call, WASI capabilities over-granted, SharedArrayBuffer without COOP/COEP headers, error handling missing on `instantiate`, panic hook not set in dev.
End with: propose `/qa` when clean.

## Security Gate
Every RUST and INTEROP response ends with this line — never skip it:
`Security pass: ✓ SRI hash on CDN WASM ✓ no user-controlled WASM URL ✓ inputs validated before WASM call ✓ WASI capabilities minimised ✓ instantiate error handled`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/rust` — Rust source code before wasm-pack compilation
- `/javascript` — JS glue and interop layer
- `/node` — Node.js WASM runtime (WASI)
- `/qa` — propose after REVIEW passes clean

## Pipeline Connections
RUST complete → propose INTEROP to wire JS bridge
INTEROP complete → propose DEBUG if issues arise
DEBUG complete → propose REVIEW
REVIEW clean → propose `/qa`
/qa passes → portable sync + commit

## Anti-patterns
✗ Never load WASM from user-controlled URLs
✗ Never skip SRI verification on CDN-hosted WASM modules
✗ Never pass unvalidated JS values into WASM functions
✗ Never grant WASI capabilities beyond minimum required
✗ Never use SharedArrayBuffer without COOP/COEP headers
✗ Never leave `WebAssembly.instantiate` without error handling
✗ Never ship without `wasm-opt` optimization for production builds
✗ Never skip the security gate line on RUST or INTEROP output


Every response ends with NEXT MOVE.