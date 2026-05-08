---
name: react
description: React 19 component and hook types builder. Covers prop types, generic components, typed hooks (useState/useRef/useActionState/useOptimistic), event handlers, context, and form Actions. Use when writing React components in TypeScript, typing hooks, building forms, or auditing React code for type safety and XSS risks.
argument-hint: "[mode: write|hooks|forms|review] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/react = component types, prop interfaces, hooks typing, event handlers, context, form Actions.
JavaScript logic → `/javascript`. tsconfig → `/typescript`. Node server → `/node`. CSS → `/css`.
/react writes the React type layer; /javascript handles runtime logic.

## Non-Negotiable
`strict: true` always (enforced via tsconfig — see `/typescript CONFIG`).
No `dangerouslySetInnerHTML` with unsanitized data — DOMPurify required for third-party HTML.
Dynamic `href`/`src` must be validated against `https://` prefix or allow-list — React does NOT sanitize URLs.
React 19 default: ref as plain prop, not `forwardRef`. `<Context>` not `<Context.Provider>`.
NON-DEV: plain-English comment on any unfamiliar type construct.

## Modes

**WRITE** — Component prop interfaces, generic components, event handler types, ref-as-prop.
Function declaration preferred over `React.FC` — better generic support, no implicit children.
Generic components: use `<T,>` trailing comma in `.tsx` (avoids JSX parse conflict — explained in REFERENCE.md).
`React.ComponentProps<'button'>` for extending native element props.
`React.FC` shown as legacy with migration note when encountered.
End with: propose `/hooks` if custom hook typing needed, `/forms` if form logic needed.

**HOOKS** — Typed hooks cheatsheet.
useState<T>, useRef<HTMLElement | null>, useCallback, useMemo, useContext, useReducer.
React 19 hooks: useActionState, useOptimistic, useFormStatus — flagged with [React 19] version marker.
React 18 fallback (useFormState) noted when relevant.
Context: `createContext<T | null>(null)` with null guard in consumer — never `null!` assertion.
End with: propose `/forms` for form Action patterns.

**FORMS** — React 19 form Actions + useActionState, controlled components, validation.
Native React 19 form Actions as default (action prop + useActionState).
Controlled component pattern for real-time validation.
One-liner: "For complex schemas: React Hook Form or Zod — ask for it."
End with: propose `/typescript REVIEW` for type safety audit.

**REVIEW** — Type safety + XSS audit.
Checks from REFERENCE.md. Report each: PASS / WARN / FAIL.
End with: propose `/qa` when clean.

## Security Gate
Every WRITE and FORMS response ends with this line — never skip it:
`Security pass: ✓ no dangerouslySetInnerHTML with user data ✓ dynamic href/src validated ✓ strict: true ✓ no unguarded type assertions ✓ explicit prop types`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/javascript` — runtime logic; propose after WRITE when JS needed
- `/typescript` — tsconfig, utility types, migration
- `/node` — API/server types consumed by React components
- `/css` — CSS Modules types (`.module.css.d.ts`), Tailwind in React

## Pipeline Connections
WRITE complete → propose `/javascript` or `/hooks` as needed
FORMS complete → propose `/typescript REVIEW`
REVIEW clean → propose `/qa`

## Anti-patterns
✗ Never use `React.FC` as default — use function declaration with explicit return type
✗ Never use `dangerouslySetInnerHTML` without DOMPurify sanitization
✗ Never leave dynamic href/src unvalidated — React does not sanitize URLs
✗ Never use `null!` in createContext — use `T | null` with null guard
✗ Never use `forwardRef` in new React 19 code — pass ref as prop
✗ Never use `<Context.Provider>` in new React 19 code — use `<Context>`
✗ Never put runtime JS logic in /react — redirect to /javascript
✗ Never skip the security gate line on WRITE/FORMS output
