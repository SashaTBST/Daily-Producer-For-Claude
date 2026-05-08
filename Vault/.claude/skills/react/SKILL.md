---
name: react
description: React 19 component and hook types builder. Covers prop types, generic components, typed hooks (useState/useRef/useActionState/useOptimistic), event handlers, context, and form Actions. Use when writing React components in TypeScript, typing hooks, building forms, or auditing React code for type safety and XSS risks.
argument-hint: "[mode: write|hooks|forms|review] [description] â€” add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/react = component types, prop interfaces, hooks typing, event handlers, context, form Actions.
JavaScript logic â†’ `/javascript`. tsconfig â†’ `/typescript`. Node server â†’ `/node`. CSS â†’ `/css`.
/react writes the React type layer; /javascript handles runtime logic.

## Non-Negotiable
`strict: true` always (enforced via tsconfig â€” see `/typescript CONFIG`).
No `dangerouslySetInnerHTML` with unsanitized data â€” DOMPurify required for third-party HTML.
Dynamic `href`/`src` must be validated against `https://` prefix or allow-list â€” React does NOT sanitize URLs.
React 19 default: ref as plain prop, not `forwardRef`. `<Context>` not `<Context.Provider>`.
NON-DEV: plain-English comment on any unfamiliar type construct.

## Modes

**WRITE** â€” Component prop interfaces, generic components, event handler types, ref-as-prop.
Function declaration preferred over `React.FC` â€” better generic support, no implicit children.
Generic components: use `<T,>` trailing comma in `.tsx` (avoids JSX parse conflict â€” explained in REFERENCE.md).
`React.ComponentProps<'button'>` for extending native element props.
`React.FC` shown as legacy with migration note when encountered.
End with: propose `/hooks` if custom hook typing needed, `/forms` if form logic needed.

**HOOKS** â€” Typed hooks cheatsheet.
useState<T>, useRef<HTMLElement | null>, useCallback, useMemo, useContext, useReducer.
React 19 hooks: useActionState, useOptimistic, useFormStatus â€” flagged with [React 19] version marker.
React 18 fallback (useFormState) noted when relevant.
Context: `createContext<T | null>(null)` with null guard in consumer â€” never `null!` assertion.
End with: propose `/forms` for form Action patterns.

**FORMS** â€” React 19 form Actions + useActionState, controlled components, validation.
Native React 19 form Actions as default (action prop + useActionState).
Controlled component pattern for real-time validation.
One-liner: "For complex schemas: React Hook Form or Zod â€” ask for it."
End with: propose `/typescript REVIEW` for type safety audit.

**REVIEW** â€” Type safety + XSS audit.
Checks from REFERENCE.md. Report each: PASS / WARN / FAIL.
End with: propose `/qa` when clean.

## Security Gate
Every WRITE and FORMS response ends with this line â€” never skip it:
`Security pass: âœ“ no dangerouslySetInnerHTML with user data âœ“ dynamic href/src validated âœ“ strict: true âœ“ no unguarded type assertions âœ“ explicit prop types`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/javascript` â€” runtime logic; propose after WRITE when JS needed
- `/typescript` â€” tsconfig, utility types, migration
- `/node` â€” API/server types consumed by React components
- `/css` â€” CSS Modules types (`.module.css.d.ts`), Tailwind in React

## Pipeline Connections
WRITE complete â†’ propose `/javascript` or `/hooks` as needed
FORMS complete â†’ propose `/typescript REVIEW`
REVIEW clean â†’ propose `/qa`

## Anti-patterns
âœ— Never use `React.FC` as default â€” use function declaration with explicit return type
âœ— Never use `dangerouslySetInnerHTML` without DOMPurify sanitization
âœ— Never leave dynamic href/src unvalidated â€” React does not sanitize URLs
âœ— Never use `null!` in createContext â€” use `T | null` with null guard
âœ— Never use `forwardRef` in new React 19 code â€” pass ref as prop
âœ— Never use `<Context.Provider>` in new React 19 code â€” use `<Context>`
âœ— Never put runtime JS logic in /react â€” redirect to /javascript
âœ— Never skip the security gate line on WRITE/FORMS output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.