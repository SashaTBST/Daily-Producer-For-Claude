---
name: node
description: Security-first Node.js builder for server-side JavaScript and TypeScript. Covers Express, Fastify, NestJS, Prisma, Vitest, and Bun/Deno alternatives. Not for browser JS (use /javascript) or React components (use /react). Use when building APIs, servers, middleware, CLIs, or server-side utilities.
argument-hint: "[mode: write|setup|test|review] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/node = server-side JS/TS: APIs, middleware, CLIs, background jobs, database access.
Browser JS → `/javascript`. TypeScript types → `/typescript`. React → `/react`.
CJS project: use `.cjs` extension or omit `"type":"module"` — do not force ESM migration.
Overlap is fine — /node writes the server layer, other skills handle their layer.

## Runtime
Default: **Node.js LTS** (22/24). ESM default — `"type": "module"` in package.json.
Bun: propose when operator states performance or dev-speed priority.
Deno: propose when operator states security-first or TypeScript-native priority.
Full comparison in REFERENCE.md. NON-DEV: explain runtime choice before scaffolding.

## Framework
Default: **Fastify** — schema validation, 2× Express throughput, built-in TypeScript support.
Express: propose when operator has existing Express codebase or wants minimal setup.
NestJS: developer-recommended — DI/decorators/modules require dev experience to debug.
NON-DEV: state reason for recommendation before writing code.

## Modes

**WRITE** — HTTP handlers, middleware, routes, Prisma queries, CLI scripts, utilities.
ESM imports by default. Named exports only.
Secrets: always use `process.env`. Node 20.6+: `--env-file .env`. Older: `dotenv` package. Never hardcode.
Always include a global error handler — never let raw errors reach the API response.
Prisma: `$queryRaw` tagged templates only. Never `$queryRawUnsafe` with string concatenation.
Input validation before every DB write or external call.
Error handling: try/catch on every async handler. Never swallow errors silently.
NON-DEV: plain-English comment on any unfamiliar Node API.
End with: propose `/typescript` to add types, `/test` for coverage.

**SETUP** — Project scaffold: runtime + framework + Prisma + matching test runner + package.json.
Test runner matches runtime: Vitest (Node) · bun:test (Bun) · Deno.test (Deno).
Output: folder structure, package.json, entry file, Prisma schema stub, test config.
Output all files in one response with numbered terminal command sequence.
NON-DEV: include "how to start the project" block above terminal commands.
Default stack: Node LTS + Fastify + Prisma + Vitest. State default before scaffolding.

**TEST** — Vitest (Node) / bun:test (Bun) / Deno.test (Deno) test scaffolding.
Opens with: "Using [runtime test runner] — the standard for this stack."
Co-locate test files as `[name].test.ts` or under `__tests__/`.
Unit tests: mock all external services with `vi.mock()`. Prisma: `vi.mock('@prisma/client')` — pattern in REFERENCE.md.
Integration tests: use test DB only — never hit production DB or external APIs.

**REVIEW** — Security audit. Full checklist in REFERENCE.md. Report each: PASS / WARN / FAIL.
Covers: raw query injection, SSRF, mass assignment, dependency supply chain, secrets in env, error leakage.
End with: `npm audit` (Node/Bun) or `deno check` (Deno) + propose `/qa` when clean.

## Security Gate
Every WRITE response ends with this line — never skip it:
`Security pass: ✓ no $queryRawUnsafe with concat ✓ input validated before DB write ✓ no secrets in code ✓ async errors handled ✓ no req.body spread directly to DB write`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/javascript` — client-side JS; /node handles server layer
- `/typescript` — add types to Node output; propose after every WRITE
- `/react` — frontend; /node handles the API it consumes
- `/php` — alternative server-side skill for PHP projects

## Pipeline Connections
WRITE complete → propose `/typescript` or `/test`
REVIEW clean → propose `/qa`
/qa passes → propose portable sync + commit

## Anti-patterns
✗ Never use `$queryRawUnsafe` with string concatenation — SQL injection
✗ Never trust user input before validation — DB write or external call
✗ Never expose stack traces or raw errors to API responses
✗ Never store secrets in code — use `process.env` or `--env-file`
✗ Never use CommonJS `require()` in new ESM projects
✗ Never use default exports — named exports only
✗ Never hit real DB or external APIs in unit tests
✗ Never skip the security gate line on WRITE output
✗ Never use `eval()` or `new Function(string)` — remote code execution
✗ Never recommend NestJS to a non-dev operator without flagging the complexity
