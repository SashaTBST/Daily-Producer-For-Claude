---
name: html
description: Security-first HTML builder for web pages, email templates, display ads, and components. Integrates with /laravel (Blade), /11ty (Nunjucks), /css, and /javascript. Use when writing, auditing, or converting any HTML output — web, email, or ad.
argument-hint: "[mode: write|email|ads|review] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Modes
Detect from context or invoke explicitly:

**WRITE** — Web pages, Blade templates, Nunjucks templates, standalone components.
First: detect context — ask if not clear: `web` / `blade` / `nunjucks` / `standalone`.
- `web`: semantic HTML5, external stylesheet link
- `blade`: `{{ }}` / `@` directives, no hardcoded data
- `nunjucks`: `{{ }}` / `{% %}` syntax, front matter variables
- `standalone`: inline styles applied, flag that a stylesheet migration is recommended
Label every output: `<!-- WEB HTML — not for email -->`
End with: propose `/css` for styling, `/javascript` for interactivity.

**EMAIL** — HTML emails for Outlook, Gmail, Apple Mail, mobile.
⚠ Entry warning: "Gmail clips messages over 102KB. Keep total HTML under 100KB to be safe."
Rules: HTML 4.01 only. Table-based layout. Inline CSS. No HTML5 semantic elements. No external stylesheets. No JavaScript.
Outlook: MSO conditional comments required for width control.
Load baseline: `baseline/email-template.md`
Label every output: `<!-- EMAIL HTML — not for web pages -->`

**ADS** — IAB display banners (HTML5 static or animated).
Rules: entry point is `index.html`. Inline CSS only. `clickTag` variable required. Max 15 initial HTTP requests. No audio autoplay.
Load baseline: `baseline/ad-template.md`
Include CONFIG BLOCK at top: width, height, click URL variable.

**REVIEW** — Accessibility audit + security pass.
STATIC checks (skill audits): run full WCAG 2.2 AA static checklist from REFERENCE.md.
RUNTIME checks (manual): list items that require browser/screen reader testing — do not claim PASS.
Report each item: PASS / WARN / FAIL / RUNTIME-REQUIRED.
End with: propose `/qa` when static checks are clean.

## Security Gate
Every WRITE and EMAIL response ends with this line — never skip it:
`Security pass: ✓ no inline event handlers ✓ no javascript: URIs ✓ alt text present ✓ form labels present ✓ CSP-compatible`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/laravel` — output as Blade template when context = blade
- `/11ty` — output as Nunjucks template when context = nunjucks
- `/css` — propose after WRITE for styling
- `/javascript` — propose after WRITE for interactivity

## Pipeline Connections
WRITE complete → propose `/css` or `/javascript` as needed
REVIEW clean (static) → propose `/qa`
/qa passes → propose portable sync + commit

## Anti-patterns
✗ Never use HTML5 semantic elements in EMAIL mode — use tables and divs only
✗ Never use external stylesheets or JavaScript in EMAIL mode
✗ Never skip the Gmail 102KB warning at EMAIL mode entry
✗ Never skip the security gate line on WRITE or EMAIL output
✗ Never claim PASS on RUNTIME accessibility items — only STATIC items can be PASS/FAIL
✗ Never use `onclick`, `onload`, or other inline event handlers — XSS risk
✗ Never use `javascript:` URIs in `href` or `src` attributes
✗ Never skip alt text on images or labels on form fields
✗ Never output EMAIL HTML without the `<!-- EMAIL HTML — not for web pages -->` label
