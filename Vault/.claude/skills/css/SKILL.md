---
name: css
description: Security-first CSS builder covering vanilla CSS, Sass/SCSS, Tailwind v4, Bootstrap v5, and CSS-in-JS. Integrates with /html and /javascript. Use when writing, styling, auditing, or selecting a CSS approach for any web project.
argument-hint: "[mode: write|framework|review|css-in-js] [description] â€” add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Tool Selection â€” Ask First If Not Clear
Before writing, confirm the right approach. Use the decision table in REFERENCE.md or ask:
- New project, modern browsers â†’ **WRITE** (vanilla CSS)
- Complex abstractions, existing Sass project â†’ **WRITE** (Sass)
- Rapid UI, design system â†’ **FRAMEWORK** (Tailwind v4)
- Non-dev operator, marketing site, MVP, "make it look nice" â†’ **FRAMEWORK** (Bootstrap v5)
- React app, component-scoped styles â†’ **CSS-IN-JS**

## Modes

**WRITE** â€” Vanilla CSS or Sass/SCSS.
Detect from context: if `.scss` files exist or Sass is in `package.json` â†’ Sass. Otherwise vanilla CSS.
Use BEM naming. Use cascade layers (`@layer`) to organise.
NON-DEV: include one-line BEM explanation + one-line `@layer` explanation with every output.
Output as external `.css` or `.scss` file â€” never inline styles.
Vanilla-first rule: recommend Sass only when mixins, loops, or conditionals are genuinely needed.
End with: propose `/javascript` if interactivity is needed.

**FRAMEWORK** â€” Tailwind v4 or Bootstrap v5.
Ask if not stated. Use decision table in REFERENCE.md.
- **Tailwind v4**: CSS-first config (`@import "tailwindcss"`). No `tailwind.config.js` (that's v3). If project has `tailwind.config.js` â†’ note: "you're on v3 â€” patterns differ."
- **Bootstrap v5**: grid + component classes, customise via CSS variables. Best for non-dev operators.
End with: propose `/html` if markup adjustments needed.

**REVIEW** â€” CSS file audit.
Scope: CSS file contents only (not HTTP headers â€” those are /html or /laravel's job).
Checks from REFERENCE.md: no `expression()`, no `url(javascript:)`, no untrusted `@import`, specificity conflicts, missing `@layer`, BEM consistency.
Report each: PASS / WARN / FAIL.
Note at end: "CSP `style-src` header must be set in deployment config â€” see /html REVIEW or /laravel."
End with: propose `/qa` when clean.

**CSS-IN-JS** â€” styled-components or Emotion. React projects only.
Gate: "CSS-in-JS is for React projects with a developer working on component-scoped styles. For web pages or non-React projects, use WRITE or FRAMEWORK instead."
Ask which library if not stated. Output: component with scoped styles + theme provider pattern.
Flag: bundle cost, SSR considerations, when vanilla CSS + modules is better.

## Security Gate
Every WRITE response ends with this line â€” never skip it:
`Security pass: âœ“ no expression() âœ“ no url(javascript:) âœ“ no untrusted @import âœ“ CSP style-src compatible`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/html` â€” CSS is proposed after WRITE mode; link stylesheet in `<head>`
- `/javascript` â€” propose after WRITE if interactive styles needed (transitions, class toggling)
- `/laravel` â€” Blade templates use same external CSS; Tailwind/Vite setup in REFERENCE.md
- `/11ty` â€” assets/ folder passthrough; Sass compile step in `.eleventy.js`

## Pipeline Connections
WRITE complete â†’ propose `/javascript` if interactivity needed
REVIEW clean â†’ propose `/qa`
/qa passes â†’ propose portable sync + commit

## Anti-patterns
âœ— Never output inline `style=""` attributes â€” always external stylesheet
âœ— Never use `!important` without flagging it as a specificity problem
âœ— Never recommend `unsafe-inline` in CSP â€” nonces or hashes only
âœ— Never use `expression()` â€” IE CSS injection vector, not valid in modern CSS
âœ— Never skip the security gate line on WRITE output
âœ— Never recommend Tailwind v3 patterns for a v4 project (check for tailwind.config.js)
âœ— Never invoke CSS-IN-JS mode for non-React projects â€” redirect to WRITE or FRAMEWORK
âœ— Never skip BEM and @layer explanation in non-dev mode output

## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.