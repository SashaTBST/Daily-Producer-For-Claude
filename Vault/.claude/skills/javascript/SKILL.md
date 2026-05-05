---
name: javascript
description: Security-first JavaScript builder for browser JS and vanilla Node scripts. Covers DOM, fetch, async/await, ES modules, form validation, Vite, ESLint 9, and Vitest. Not for JSX (use /react), TypeScript types (use /typescript), or Node servers (use /node). Use when writing, testing, or auditing vanilla JavaScript.
argument-hint: "[mode: write|setup|test|review] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/javascript = browser JS + vanilla Node scripts.
JSX in file → `/react`. TypeScript types needed → `/typescript`. Node server/API → `/node`.
Overlap is fine — /javascript writes the JS, other skills handle their layer.

## Version
Default: ES2024. Note browser support on any feature below 95% support.
NON-DEV: comment ES2024 features with browser support (e.g. `// Chrome 117+, Firefox 119+, Safari 17+`).
Temporal API and Decorators: always flag as requiring polyfill regardless of mode.

## Modes

**WRITE** — DOM manipulation, fetch/API calls, async/await, event handling, form validation, ES modules.
Never `innerHTML` with user-supplied data — use `textContent` or `createElement`.
Use DOMPurify when sanitising third-party HTML — pin to latest stable, run `npm audit`.
NON-DEV: plain-English comment on AbortController and any unfamiliar API.
Use named exports. Use `async/await` + `Promise.all` for parallel operations.
MutationObserver for DOM change detection (Mutation Events removed Chrome 127+).
End with: propose `/css` for styling, `/typescript` to add types, `/test` for coverage.

**SETUP** — New project scaffold: Vite + ESLint 9 (flat config) + Prettier + Vitest + package.json.
Output all files in one response with numbered terminal command sequence.
Default: Vite (modern). Flag Webpack only if legacy/complex customisation stated.
NON-DEV: include "how to start the project" block as comments above the terminal commands.

**TEST** — Vitest test scaffolding.
Opens with: "Using Vitest (the modern standard). If your project already uses Jest, say so — the patterns are nearly identical."
Co-locate test files as `[name].test.js` or under `__tests__/`.
Async test patterns. `vi.fn()` for mocks. `vi.spyOn()` for spies.

**REVIEW** — Security audit.
First: ask if project has `package.json`/npm or is standalone script tags.
- npm project: full checklist including `npm audit` supply chain check.
- Standalone: skip npm audit; check CDN scripts for SRI integrity attributes.
Full checklist in REFERENCE.md. Report each: PASS / WARN / FAIL.
End with: `npm audit` command (if npm) + propose `/qa` when clean.

## Security Gate
Every WRITE response ends with this line — never skip it:
`Security pass: ✓ no innerHTML with user data ✓ no eval() ✓ textContent/createElement used ✓ fetch errors handled ✓ CSP script-src compatible`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/html` — script tag at bottom of `<body>`; propose after WRITE
- `/css` — propose after WRITE for styling
- `/typescript` — propose after WRITE to add types
- `/react` — for JSX/component work
- `/node` — for server-side Node APIs

## Pipeline Connections
WRITE complete → propose `/css`, `/typescript`, or `/test`
REVIEW clean → propose `/qa`
/qa passes → propose portable sync + commit

## Anti-patterns
✗ Never use `innerHTML` with user-supplied or third-party data — XSS vector
✗ Never use `eval()` or `new Function(string)` — remote code execution
✗ Never use `document.write()` — deprecated, XSS risk
✗ Never mutate `Object.prototype` or `Array.prototype` — prototype pollution
✗ Never use default exports — named exports only for tree-shaking and refactoring
✗ Never use sequential `await` in a loop when operations are independent — use `Promise.all()`
✗ Never ignore fetch errors — check both network errors AND `response.ok`
✗ Never use Mutation Events (`DOMNodeInserted` etc.) — removed Chrome 127+ July 2024
✗ Never skip the security gate line on WRITE output
✗ Never recommend Webpack for a new project — Vite is the default


Every response ends with NEXT MOVE.