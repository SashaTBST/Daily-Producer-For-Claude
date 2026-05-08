---
name: typescript
description: Strict-mode TypeScript builder covering types, interfaces, generics, utility types, tsconfig, and migration from JavaScript. Integrates with /javascript, /react, and /node. Use when writing TypeScript, configuring tsconfig, or adding types to existing JS code.
argument-hint: "[mode: write|config|migrate|review] [description] â€” add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/typescript = types, interfaces, generics, utility types, tsconfig, migration.
React component types â†’ `/react`. Node server types â†’ `/node`.
/typescript writes the TS layer; other skills handle their framework layer.

## Non-Negotiable
`strict: true` always. No exceptions. It catches the most bugs at compile time.
Enums: never in Vite projects (isolatedModules incompatible). Use `const` objects with `as const` instead.
NON-DEV: always explain why when redirecting away from enums.

## Modes

**WRITE** â€” Types, interfaces, classes, functions, generics, utility types.
TS 5.x features by default: decorators (no `experimentalDecorators` flag needed), `satisfies` operator, `const` type params.
Default error handling: `try/catch` with typed errors. Result type pattern available on request â€” full pattern in REFERENCE.md.
NON-DEV: plain-English comment on any unfamiliar type construct.
End with: propose `/javascript` if runtime logic needed, `/react` for component types, `/node` for server types.

**CONFIG** â€” tsconfig.json generation.
First: ask which target â€” `browser/Vite`, `Node`, or `library`.
Output the single matching tsconfig from REFERENCE.md.
Vite target: always append `"typecheck": "tsc --noEmit"` to package.json scripts with note:
"Run `npm run typecheck` before committing â€” Vite transpiles but does NOT type-check."

**MIGRATE** â€” Convert JavaScript to TypeScript.
Phase 1 tsconfig: `noImplicitAny: true` only â€” fix errors before Phase 2.
Phase 2 tsconfig: full `strict: true` â€” run after Phase 1 is clean.
Output: (1) converted `.ts` content, (2) rename command (`mv file.js file.ts`), (3) Phase 1 tsconfig.
Flag any enums in source â€” convert to `const` objects with `as const`.

**REVIEW** â€” Type safety audit.
Checks from REFERENCE.md. Report each: PASS / WARN / FAIL.
Flags: bare `any`, unguarded `as Type` assertions, `!` non-null without comment, missing `strict`, `@ts-ignore` without justification, missing return types on exported functions.
End with: propose `/qa` when clean.

## Security Gate
Every WRITE response ends with this line â€” never skip it:
`Security pass: âœ“ strict: true âœ“ no implicit any âœ“ no unguarded type assertions âœ“ explicit return types on exports âœ“ null handled`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/javascript` â€” adds types to JS output; propose after /javascript WRITE
- `/react` â€” component prop types, hooks types, event types
- `/node` â€” server/API types, fs/path/http module types
- `/css` â€” CSS module types (`.module.css.d.ts`)

## Pipeline Connections
WRITE complete â†’ propose `/javascript` or `/react` or `/node` as needed
REVIEW clean â†’ propose `/qa`
/qa passes â†’ propose portable sync + commit

## Anti-patterns
âœ— Never set `strict: false` or omit strict â€” always on
âœ— Never use bare `any` â€” use `unknown` then narrow, or a proper type
âœ— Never use enums in Vite projects â€” use `const` objects with `as const`
âœ— Never use `as Type` without a type guard confirming it first
âœ— Never use `!` non-null assertion without a comment explaining why it's safe
âœ— Never use `@ts-ignore` without an explanatory comment
âœ— Never skip `tsc --noEmit` in Vite projects â€” Vite does not type-check
âœ— Never skip the security gate line on WRITE output
âœ— Never put React component types in /typescript â€” redirect to /react


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.