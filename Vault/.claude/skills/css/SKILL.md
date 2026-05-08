---
name: css
description: Security-first CSS builder covering vanilla CSS, Sass/SCSS, Tailwind v4, Bootstrap v5, and CSS-in-JS. Integrates with /html and /javascript. Use when writing, styling, auditing, or selecting a CSS approach for any web project.
argument-hint: "[mode: write|framework|review|css-in-js] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Tool Selection — Ask First If Not Clear
Before writing, confirm the right approach. Use the decision table in REFERENCE.md or ask:
- New project, modern browsers → **WRITE** (vanilla CSS)
- Complex abstractions, existing Sass project → **WRITE** (Sass)
- Rapid UI, design system → **FRAMEWORK** (Tailwind v4)
- Non-dev operator, marketing site, MVP, "make it look nice" → **FRAMEWORK** (Bootstrap v5)
- React app, component-scoped styles → **CSS-IN-JS**

## Modes

**WRITE** — Vanilla CSS or Sass/SCSS.
Detect from context: if `.scss` files exist or Sass is in `package.json` → Sass. Otherwise vanilla CSS.
Use BEM naming. Use cascade layers (`@layer`) to organise.
NON-DEV: include one-line BEM explanation + one-line `@layer` explanation with every output.
Output as external `.css` or `.scss` file — never inline styles.
Vanilla-first rule: recommend Sass only when mixins, loops, or conditionals are genuinely needed.
End with: propose `/javascript` if interactivity is needed.

**FRAMEWORK** — Tailwind v4 or Bootstrap v5.
Ask if not stated. Use decision table in REFERENCE.md.
- **Tailwind v4**: CSS-first config (`@import "tailwindcss"`). No `tailwind.config.js` (that's v3). If project has `tailwind.config.js` → note: "you're on v3 — patterns differ."
- **Bootstrap v5**: grid + component classes, customise via CSS variables. Best for non-dev operators.
End with: propose `/html` if markup adjustments needed.

**REVIEW** — CSS file audit.
Scope: CSS file contents only (not HTTP headers — those are /html or /laravel's job).
Checks from REFERENCE.md: no `expression()`, no `url(javascript:)`, no untrusted `@import`, specificity conflicts, missing `@layer`, BEM consistency.
Report each: PASS / WARN / FAIL.
Note at end: "CSP `style-src` header must be set in deployment config — see /html REVIEW or /laravel."
End with: propose `/qa` when clean.

**CSS-IN-JS** — styled-components or Emotion. React projects only.
Gate: "CSS-in-JS is for React projects with a developer working on component-scoped styles. For web pages or non-React projects, use WRITE or FRAMEWORK instead."
Ask which library if not stated. Output: component with scoped styles + theme provider pattern.
Flag: bundle cost, SSR considerations, when vanilla CSS + modules is better.

## Security Gate
Every WRITE response ends with this line — never skip it:
`Security pass: ✓ no expression() ✓ no url(javascript:) ✓ no untrusted @import ✓ CSP style-src compatible`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/html` — CSS is proposed after WRITE mode; link stylesheet in `<head>`
- `/javascript` — propose after WRITE if interactive styles needed (transitions, class toggling)
- `/laravel` — Blade templates use same external CSS; Tailwind/Vite setup in REFERENCE.md
- `/11ty` — assets/ folder passthrough; Sass compile step in `.eleventy.js`

## Pipeline Connections
WRITE complete → propose `/javascript` if interactivity needed
REVIEW clean → propose `/qa`
/qa passes → propose portable sync + commit

## Anti-patterns
✗ Never output inline `style=""` attributes — always external stylesheet
✗ Never use `!important` without flagging it as a specificity problem
✗ Never recommend `unsafe-inline` in CSP — nonces or hashes only
✗ Never use `expression()` — IE CSS injection vector, not valid in modern CSS
✗ Never skip the security gate line on WRITE output
✗ Never recommend Tailwind v3 patterns for a v4 project (check for tailwind.config.js)
✗ Never invoke CSS-IN-JS mode for non-React projects — redirect to WRITE or FRAMEWORK
✗ Never skip BEM and @layer explanation in non-dev mode output