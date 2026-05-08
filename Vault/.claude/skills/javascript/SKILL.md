---
name: javascript
description: Security-first JavaScript builder for browser JS and vanilla Node scripts. Covers DOM, fetch, async/await, ES modules, form validation, Vite, ESLint 9, and Vitest. Not for JSX (use /react), TypeScript types (use /typescript), or Node servers (use /node). Use when writing, testing, or auditing vanilla JavaScript.
argument-hint: "[mode: write|setup|test|review] [description] â€” add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/javascript = browser JS + vanilla Node scripts.
JSX in file â†’ `/react`. TypeScript types needed â†’ `/typescript`. Node server/API â†’ `/node`.
Overlap is fine â€” /javascript writes the JS, other skills handle their layer.

## Version
Default: ES2024. Note browser support on any feature below 95% support.
NON-DEV: comment ES2024 features with browser support (e.g. `// Chrome 117+, Firefox 119+, Safari 17+`).
Temporal API and Decorators: always flag as requiring polyfill regardless of mode.

## Modes

**WRITE** â€” DOM manipulation, fetch/API calls, async/await, event handling, form validation, ES modules.
Never `innerHTML` with user-supplied data â€” use `textContent` or `createElement`.
Use DOMPurify when sanitising third-party HTML â€” pin to latest stable, run `npm audit`.
NON-DEV: plain-English comment on AbortController and any unfamiliar API.
Use named exports. Use `async/await` + `Promise.all` for parallel operations.
MutationObserver for DOM change detection (Mutation Events removed Chrome 127+).
End with: propose `/css` for styling, `/typescript` to add types, `/test` for coverage.

**SETUP** â€” New project scaffold: Vite + ESLint 9 (flat config) + Prettier + Vitest + package.json.
Output all files in one response with numbered terminal command sequence.
Default: Vite (modern). Flag Webpack only if legacy/complex customisation stated.
NON-DEV: include "how to start the project" block as comments above the terminal commands.

**TEST** â€” Vitest test scaffolding.
Opens with: "Using Vitest (the modern standard). If your project already uses Jest, say so â€” the patterns are nearly identical."
Co-locate test files as `[name].test.js` or under `__tests__/`.
Async test patterns. `vi.fn()` for mocks. `vi.spyOn()` for spies.

**REVIEW** â€” Security audit.
First: ask if project has `package.json`/npm or is standalone script tags.
- npm project: full checklist including `npm audit` supply chain check.
- Standalone: skip npm audit; check CDN scripts for SRI integrity attributes.
Full checklist in REFERENCE.md. Report each: PASS / WARN / FAIL.
End with: `npm audit` command (if npm) + propose `/qa` when clean.

## Security Gate
Every WRITE response ends with this line â€” never skip it:
`Security pass: âœ“ no innerHTML with user data âœ“ no eval() âœ“ textContent/createElement used âœ“ fetch errors handled âœ“ CSP script-src compatible`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/html` â€” script tag at bottom of `<body>`; propose after WRITE
- `/css` â€” propose after WRITE for styling
- `/typescript` â€” propose after WRITE to add types
- `/react` â€” for JSX/component work
- `/node` â€” for server-side Node APIs

## Pipeline Connections
WRITE complete â†’ propose `/css`, `/typescript`, or `/test`
REVIEW clean â†’ propose `/qa`
/qa passes â†’ propose portable sync + commit

## Anti-patterns
âœ— Never use `innerHTML` with user-supplied or third-party data â€” XSS vector
âœ— Never use `eval()` or `new Function(string)` â€” remote code execution
âœ— Never use `document.write()` â€” deprecated, XSS risk
âœ— Never mutate `Object.prototype` or `Array.prototype` â€” prototype pollution
âœ— Never use default exports â€” named exports only for tree-shaking and refactoring
âœ— Never use sequential `await` in a loop when operations are independent â€” use `Promise.all()`
âœ— Never ignore fetch errors â€” check both network errors AND `response.ok`
âœ— Never use Mutation Events (`DOMNodeInserted` etc.) â€” removed Chrome 127+ July 2024
âœ— Never skip the security gate line on WRITE output
âœ— Never recommend Webpack for a new project â€” Vite is the default


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.