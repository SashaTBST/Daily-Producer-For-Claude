---
name: webassembly
description: Security-first WebAssembly builder covering WASM concepts, Rustâ†’WASM via wasm-pack/wasm-bindgen, C/C++â†’WASM via Emscripten, JSâ†”WASM interop, browser and Node.js runtimes, WASI, and performance profiling. Use when compiling Rust or C to WASM, wiring JSâ†”WASM interop, debugging WASM modules, or auditing WASM for integrity and memory safety.
argument-hint: "[mode: rust|interop|debug|review] [description] â€” add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/webassembly = WASM binary format, Rustâ†’WASM (wasm-pack), C/C++â†’WASM (Emscripten), JS interop, WASI, browser + Node.js runtimes.
Writing raw WAT (WebAssembly Text Format) by hand is out of scope â€” redirect to wasm-pack or Emscripten workflows.

## Non-Negotiable
Always verify WASM module integrity (subresource integrity hash) before loading from CDN â€” WASM executes at near-native privilege.
Never load WASM from user-controlled URLs â€” supply chain attack vector.
WASM linear memory is not automatically sandboxed â€” validate all inputs before passing to WASM functions.
WASI: filesystem access is capability-based â€” never grant broader than the minimum required capability.
wasm-bindgen: validate all JS values crossing the boundary â€” null/undefined can panic in Rust WASM.
Always handle `WebAssembly.instantiate` errors â€” silent failure leaves app in broken state.

## Modes

**RUST** â€” Rustâ†’WASM via wasm-pack and wasm-bindgen.
Setup: `wasm-pack build --target web` â†’ import generated JS glue â†’ `await init()` before calling exports.
`#[wasm_bindgen]` on public functions and structs. `console_error_panic_hook` for readable panics in dev.
`wee_alloc` or default allocator â€” document choice. `#[wasm_bindgen(js_name)]` for camelCase JS API.
NON-DEV: explain that wasm-pack compiles Rust to a `.wasm` binary + JS glue file that handles the bridge.
End with: propose INTEROP mode to wire the JSâ†”WASM interface.

**INTEROP** â€” JSâ†”WASM bridge, memory passing, shared buffers.
Primitives (i32, f64) pass by value. Strings and arrays: wasm-bindgen serialises via `TextEncoder`/`TextDecoder` â€” document the copy cost.
SharedArrayBuffer: requires COOP/COEP headers (`Cross-Origin-Opener-Policy: same-origin`, `Cross-Origin-Embedder-Policy: require-corp`).
Never pass raw pointers from JS to WASM â€” use wasm-bindgen types or explicit memory views.
NON-DEV: explain that WASM memory is a flat byte array â€” JS and WASM share it via `WebAssembly.Memory`.
End with: propose DEBUG mode if interop issues arise.

**DEBUG** â€” WASM debugging tools and error diagnosis.
Browser DevTools: enable WASM source maps (`wasm-pack build --dev`). Chrome DevTools supports WASM stepping.
`console_error_panic_hook::set_once()` â€” call in `#[wasm_bindgen(start)]` for Rust panic messages.
Common errors: `RuntimeError: unreachable` (Rust panic), `LinkError` (missing import), `CompileError` (invalid binary).
`wasm-opt` and `twiggy` for size profiling. `wasm-snip` to remove unused functions.
NON-DEV: explain what each error type means in plain English before showing fix.
End with: propose REVIEW mode when module is working.

**REVIEW** â€” Security + integrity audit. Report each item: PASS / WARN / FAIL.
Flags: no SRI on CDN WASM, user-controlled WASM URL, unvalidated inputs before WASM call, WASI capabilities over-granted, SharedArrayBuffer without COOP/COEP headers, error handling missing on `instantiate`, panic hook not set in dev.
End with: propose `/qa` when clean.

## Security Gate
Every RUST and INTEROP response ends with this line â€” never skip it:
`Security pass: âœ“ SRI hash on CDN WASM âœ“ no user-controlled WASM URL âœ“ inputs validated before WASM call âœ“ WASI capabilities minimised âœ“ instantiate error handled`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/rust` â€” Rust source code before wasm-pack compilation
- `/javascript` â€” JS glue and interop layer
- `/node` â€” Node.js WASM runtime (WASI)
- `/qa` â€” propose after REVIEW passes clean

## Pipeline Connections
RUST complete â†’ propose INTEROP to wire JS bridge
INTEROP complete â†’ propose DEBUG if issues arise
DEBUG complete â†’ propose REVIEW
REVIEW clean â†’ propose `/qa`
/qa passes â†’ portable sync + commit

## Anti-patterns
âœ— Never load WASM from user-controlled URLs
âœ— Never skip SRI verification on CDN-hosted WASM modules
âœ— Never pass unvalidated JS values into WASM functions
âœ— Never grant WASI capabilities beyond minimum required
âœ— Never use SharedArrayBuffer without COOP/COEP headers
âœ— Never leave `WebAssembly.instantiate` without error handling
âœ— Never ship without `wasm-opt` optimization for production builds
âœ— Never skip the security gate line on RUST or INTEROP output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.