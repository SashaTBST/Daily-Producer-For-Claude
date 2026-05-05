---
name: typescript
description: Strict-mode TypeScript builder covering types, interfaces, generics, utility types, tsconfig, and migration from JavaScript. Integrates with /javascript, /react, and /node. Use when writing TypeScript, configuring tsconfig, or adding types to existing JS code.
argument-hint: "[mode: write|config|migrate|review] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/typescript = types, interfaces, generics, utility types, tsconfig, migration.
React component types → `/react`. Node server types → `/node`.
/typescript writes the TS layer; other skills handle their framework layer.

## Non-Negotiable
`strict: true` always. No exceptions. It catches the most bugs at compile time.
Enums: never in Vite projects (isolatedModules incompatible). Use `const` objects with `as const` instead.
NON-DEV: always explain why when redirecting away from enums.

## Modes

**WRITE** — Types, interfaces, classes, functions, generics, utility types.
TS 5.x features by default: decorators (no `experimentalDecorators` flag needed), `satisfies` operator, `const` type params.
Default error handling: `try/catch` with typed errors. Result type pattern available on request — full pattern in REFERENCE.md.
NON-DEV: plain-English comment on any unfamiliar type construct.
End with: propose `/javascript` if runtime logic needed, `/react` for component types, `/node` for server types.

**CONFIG** — tsconfig.json generation.
First: ask which target — `browser/Vite`, `Node`, or `library`.
Output the single matching tsconfig from REFERENCE.md.
Vite target: always append `"typecheck": "tsc --noEmit"` to package.json scripts with note:
"Run `npm run typecheck` before committing — Vite transpiles but does NOT type-check."

**MIGRATE** — Convert JavaScript to TypeScript.
Phase 1 tsconfig: `noImplicitAny: true` only — fix errors before Phase 2.
Phase 2 tsconfig: full `strict: true` — run after Phase 1 is clean.
Output: (1) converted `.ts` content, (2) rename command (`mv file.js file.ts`), (3) Phase 1 tsconfig.
Flag any enums in source — convert to `const` objects with `as const`.

**REVIEW** — Type safety audit.
Checks from REFERENCE.md. Report each: PASS / WARN / FAIL.
Flags: bare `any`, unguarded `as Type` assertions, `!` non-null without comment, missing `strict`, `@ts-ignore` without justification, missing return types on exported functions.
End with: propose `/qa` when clean.

## Security Gate
Every WRITE response ends with this line — never skip it:
`Security pass: ✓ strict: true ✓ no implicit any ✓ no unguarded type assertions ✓ explicit return types on exports ✓ null handled`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/javascript` — adds types to JS output; propose after /javascript WRITE
- `/react` — component prop types, hooks types, event types
- `/node` — server/API types, fs/path/http module types
- `/css` — CSS module types (`.module.css.d.ts`)

## Pipeline Connections
WRITE complete → propose `/javascript` or `/react` or `/node` as needed
REVIEW clean → propose `/qa`
/qa passes → propose portable sync + commit

## Anti-patterns
✗ Never set `strict: false` or omit strict — always on
✗ Never use bare `any` — use `unknown` then narrow, or a proper type
✗ Never use enums in Vite projects — use `const` objects with `as const`
✗ Never use `as Type` without a type guard confirming it first
✗ Never use `!` non-null assertion without a comment explaining why it's safe
✗ Never use `@ts-ignore` without an explanatory comment
✗ Never skip `tsc --noEmit` in Vite projects — Vite does not type-check
✗ Never skip the security gate line on WRITE output
✗ Never put React component types in /typescript — redirect to /react


Every response ends with NEXT MOVE.