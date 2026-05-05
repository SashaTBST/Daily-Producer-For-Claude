# CSS Skill — Reference
Vanilla CSS 2025 | Sass | Tailwind v4 | Bootstrap 5.3 | Last verified: 2026-04-27

## Research Sources
- CSS nesting, layers, container queries (Builder.io 2024): https://www.builder.io/blog/css-2024-nesting-layers-container-queries
- Modern CSS trends 2025: https://medium.com/@mernstackdevbykevin/modern-css-trends-2025-container-queries-subgrid-cascade-layers-real-use-cases-tips-733af70eb5fb
- Why vanilla CSS is great in 2025: https://ikius.com/blog/why-vanilla-css-is-great
- Sass vs vanilla CSS 2025: https://medium.com/@erennaktas/is-css-the-new-sass-heres-what-you-need-to-know-in-2025-fef0e9a379c6
- Tailwind CSS v4 overview: https://eagerworks.com/blog/tailwind-css-v4
- Tailwind best practices 2024: https://www.uxpin.com/studio/blog/tailwind-best-practices/
- Bootstrap in 2025: https://medium.com/@christinenamatovu79/the-future-of-bootstrap-in-2025-why-its-still-a-smart-choice-for-web-developers-93abb043abde
- CSP style-src directive (MDN): https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Content-Security-Policy/style-src
- CSP hashes and nonces: https://centralcsp.com/docs/csp-hashes-nonce
- styled-components vs Emotion: https://javascript.plainenglish.io/styled-components-vs-emotion-5-factors-that-decide-the-winner-f47bb1149969
- Alternatives to styled-components 2024: https://www.somethingsblog.com/2024/10/21/7-powerful-alternatives-to-styled-components/
- ITCSS architecture: https://www.xfive.co/blog/itcss-scalable-maintainable-css-architecture
- BEM + ITCSS modern architecture: https://namastedev.com/blog/modern-css-architecture-bem-itcss-and-beyond/

---

## Tool Selection Decision Table

| Situation | Recommend | Why |
|---|---|---|
| New project, modern browsers | Vanilla CSS | No build step, native nesting/variables/layers now available |
| Complex abstractions, legacy codebase | Sass/SCSS | Mixins, loops, conditionals, partials — no native CSS equivalent |
| Rapid UI, design system, developer team | Tailwind v4 | Utility-first, fast iteration, CSS-first config |
| Non-dev operator, marketing site, MVP | Bootstrap v5 | Pre-built components, CSS variable theming, consistent fast results |
| React, component-scoped styles | Emotion or styled-components | Scoped per component, dynamic theming, React ecosystem |
| Need Bootstrap AND custom design | Bootstrap + CSS variables | Override `--bs-*` variables in your own CSS file |

**70% rule:** if 70%+ of the UI maps to standard components → use Bootstrap. If mostly custom → vanilla or Tailwind.

---

## Vanilla CSS — Modern Features (stable 2024-2025)

### CSS Nesting (Chrome 112+, Safari 16.5+, Firefox 117+)
```css
/* Plain English: write child styles inside the parent — no need to repeat the selector */
.card {
  padding: 1rem;

  /* & means "this element" — .card:hover */
  &:hover {
    background: #f5f5f5;
  }

  /* Nesting child selectors */
  & .card__title {
    font-size: 1.25rem;
  }
}
```

### Cascade Layers — @layer (95% browser support 2025)
```css
/*
  Plain English: this line sets the priority order.
  base styles lose to component styles, which lose to utility overrides.
  Prevents specificity battles and !important overuse.
*/
@layer base, components, utilities;

@layer base {
  *, *::before, *::after { box-sizing: border-box; }
  body { margin: 0; font-family: system-ui, sans-serif; }
}

@layer components {
  .btn { padding: 0.5rem 1rem; border-radius: 4px; cursor: pointer; }
  .btn--primary { background: #0066cc; color: #fff; }
}

@layer utilities {
  .mt-1 { margin-top: 0.25rem; }
  .text-center { text-align: center; }
}
```

### Container Queries (84% browser support 2025)
```css
/*
  Plain English: styles respond to the component's container size,
  not the screen size. Better for reusable components in different layouts.
*/
.card-container {
  container-type: inline-size;
}

@container (min-width: 400px) {
  .card { display: grid; grid-template-columns: 1fr 2fr; }
}
```

### Custom Properties (CSS Variables)
```css
/* Plain English: define values once, use everywhere. Change the variable = change everything. */
:root {
  --color-primary: #0066cc;
  --color-text: #333333;
  --spacing-base: 1rem;
}

.btn { background: var(--color-primary); padding: var(--spacing-base); }
```

---

## BEM Naming Convention

```css
/*
  Plain English: BEM (Block Element Modifier) is a naming system.
  It keeps styles independent so they don't accidentally affect each other.

  Block = the component (.card)
  Element = a part of the component (.card__title)
  Modifier = a variation (.card--featured)
*/

.card { }                    /* Block */
.card__title { }             /* Element */
.card__image { }             /* Element */
.card--featured { }          /* Modifier — variation of .card */
.card--featured .card__title { }  /* Element inside a modifier */
```

---

## Sass/SCSS — When to Use

**Use Sass when you need:**
- Mixins (reusable style patterns with arguments)
- Loops (`@each`, `@for`) for generating utility classes
- Conditionals (`@if`) for complex theming logic
- Partials (`_variables.scss`, `_mixins.scss`) for organised file structure

**Vanilla CSS is sufficient for:**
- Variables → CSS custom properties (`--var`)
- Nesting → native CSS nesting (Chrome 112+)
- Reuse → `@layer` + BEM components

```scss
// Sass mixin example — no native CSS equivalent
@mixin flex-center {
  display: flex;
  align-items: center;
  justify-content: center;
}

.hero { @include flex-center; min-height: 100vh; }

// Sass loop — generating classes
@each $size in sm, md, lg {
  .btn--#{$size} { padding: map-get($sizes, $size); }
}
```

---

## Tailwind v4 — Key Changes From v3

| v3 | v4 |
|---|---|
| `tailwind.config.js` | CSS-first: `@import "tailwindcss"` in your CSS file |
| Content paths in config | Auto-detection — no content array needed |
| `npx tailwindcss init` | No init command — just import and go |
| Plugin API in JS | `@plugin` directive in CSS |

**v3 detection:** if project has `tailwind.config.js` → v3. Patterns differ significantly.

**v4 setup:**
```css
/* Import Tailwind — replaces tailwind.config.js entirely */
@import "tailwindcss";

/* Custom theme tokens */
@theme {
  --color-brand: #0066cc;
  --font-sans: "Inter", sans-serif;
}
```

**Component extraction with @apply:**
```css
/* When repeating the same utility classes: extract to a component class */
.btn-primary {
  @apply px-4 py-2 bg-blue-600 text-white rounded font-semibold hover:bg-blue-700;
}
```

---

## Bootstrap v5 — Customisation Pattern

```css
/* Override Bootstrap CSS variables — no Sass required */
:root {
  --bs-primary: #0066cc;          /* changes all primary-coloured components */
  --bs-border-radius: 0.5rem;     /* changes all rounded corners */
  --bs-font-sans-serif: "Inter", system-ui, sans-serif;
}
```

Bootstrap grid reference:
```html
<!-- 12-column grid — col-* adds up to 12 per row -->
<div class="container">
  <div class="row">
    <div class="col-md-8">Main content</div>   <!-- 2/3 width on md+ -->
    <div class="col-md-4">Sidebar</div>         <!-- 1/3 width on md+ -->
  </div>
</div>
```

---

## CSP style-src — Security Rules

**Never recommend `unsafe-inline`** — it allows any inline style, defeating CSP.

**For inline `<style>` blocks — use hashes:**
```
Content-Security-Policy: style-src 'self' 'sha256-[base64-hash-of-style-content]'
```

**For dynamic inline styles — use nonces:**
```html
<!-- Server generates a new nonce per request -->
<style nonce="abc123xyz">
  .dynamic { color: red; }
</style>
```
```
Content-Security-Policy: style-src 'self' 'nonce-abc123xyz'
```

**Best practice:** external stylesheets only. `style-src 'self'` with no `unsafe-inline` — zero inline styles needed.

**`expression()` — CSS injection vector (legacy IE, still flagged by scanners):**
Never use: `width: expression(document.body.clientWidth)` — blocked by all modern browsers but still a red flag in security audits.

---

## REVIEW Checklist

Run on every REVIEW call. Report: PASS / WARN / FAIL.

| # | Check | What to look for |
|---|---|---|
| 1 | No `expression()` | Search file for `expression(` — must be zero matches |
| 2 | No `url(javascript:)` | Search for `url(javascript:` — injection vector |
| 3 | No untrusted `@import` | External `@import` URLs — flag any not from trusted CDN |
| 4 | No `!important` overuse | More than 2–3 `!important` = specificity problem — WARN |
| 5 | Cascade layers present | `@layer` declaration at top — WARN if absent on large file |
| 6 | BEM naming | Class names follow `.block__element--modifier` pattern |
| 7 | No inline styles in CSS | `style=""` attributes in HTML — out of scope, flag to /html |
| 8 | Unused `@import` | Dead imports add weight — WARN |
| 9 | Colour contrast | Cannot audit from CSS alone — flag palette for manual check |
| 10 | Custom properties defined | Variables in `:root` — WARN if hardcoded values repeated |

**Note:** CSP `style-src` header not auditable from CSS file alone. Set in deployment config — see /html REVIEW or /laravel REVIEW.

---

## CSS-in-JS — Trade-offs

| | Emotion | styled-components |
|---|---|---|
| Bundle size | ~13KB | ~16KB |
| Performance | Faster (Concurrent mode ready) | Good |
| API style | String or object syntax | Tagged template literals |
| DX | Steeper curve | More intuitive |
| SSR | Built-in | Requires setup |
| Best for | Performance-critical apps | Teams new to CSS-in-JS |

**When to switch back to vanilla CSS + CSS Modules:**
- Bundle size is a constraint
- Team unfamiliar with CSS-in-JS debugging
- No need for dynamic runtime styles — static styles belong in `.css` files

```jsx
// Emotion example
import { css } from '@emotion/react';

const buttonStyle = css`
  padding: 0.5rem 1rem;
  background: var(--color-primary);
  color: white;
  border-radius: 4px;
`;

export const Button = ({ children }) => (
  <button css={buttonStyle}>{children}</button>
);
```
