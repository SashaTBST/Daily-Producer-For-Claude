---
name: html
description: Security-first HTML builder for web pages, email templates, display ads, and components. Integrates with /laravel (Blade), /11ty (Nunjucks), /css, and /javascript. Use when writing, auditing, or converting any HTML output â€” web, email, or ad.
argument-hint: "[mode: write|email|ads|review] [description] â€” add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Modes
Detect from context or invoke explicitly:

**WRITE** â€” Web pages, Blade templates, Nunjucks templates, standalone components.
First: detect context â€” ask if not clear: `web` / `blade` / `nunjucks` / `standalone`.
- `web`: semantic HTML5, external stylesheet link
- `blade`: `{{ }}` / `@` directives, no hardcoded data
- `nunjucks`: `{{ }}` / `{% %}` syntax, front matter variables
- `standalone`: inline styles applied, flag that a stylesheet migration is recommended
Label every output: `<!-- WEB HTML â€” not for email -->`
End with: propose `/css` for styling, `/javascript` for interactivity.

**EMAIL** â€” HTML emails for Outlook, Gmail, Apple Mail, mobile.
âš  Entry warning: "Gmail clips messages over 102KB. Keep total HTML under 100KB to be safe."
Rules: HTML 4.01 only. Table-based layout. Inline CSS. No HTML5 semantic elements. No external stylesheets. No JavaScript.
Outlook: MSO conditional comments required for width control.
Load baseline: `baseline/email-template.md`
Label every output: `<!-- EMAIL HTML â€” not for web pages -->`

**ADS** â€” IAB display banners (HTML5 static or animated).
Rules: entry point is `index.html`. Inline CSS only. `clickTag` variable required. Max 15 initial HTTP requests. No audio autoplay.
Load baseline: `baseline/ad-template.md`
Include CONFIG BLOCK at top: width, height, click URL variable.

**REVIEW** â€” Accessibility audit + security pass.
STATIC checks (skill audits): run full WCAG 2.2 AA static checklist from REFERENCE.md.
RUNTIME checks (manual): list items that require browser/screen reader testing â€” do not claim PASS.
Report each item: PASS / WARN / FAIL / RUNTIME-REQUIRED.
End with: propose `/qa` when static checks are clean.

## Security Gate
Every WRITE and EMAIL response ends with this line â€” never skip it:
`Security pass: âœ“ no inline event handlers âœ“ no javascript: URIs âœ“ alt text present âœ“ form labels present âœ“ CSP-compatible`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/laravel` â€” output as Blade template when context = blade
- `/11ty` â€” output as Nunjucks template when context = nunjucks
- `/css` â€” propose after WRITE for styling
- `/javascript` â€” propose after WRITE for interactivity

## Pipeline Connections
WRITE complete â†’ propose `/css` or `/javascript` as needed
REVIEW clean (static) â†’ propose `/qa`
/qa passes â†’ propose portable sync + commit

## Anti-patterns
âœ— Never use HTML5 semantic elements in EMAIL mode â€” use tables and divs only
âœ— Never use external stylesheets or JavaScript in EMAIL mode
âœ— Never skip the Gmail 102KB warning at EMAIL mode entry
âœ— Never skip the security gate line on WRITE or EMAIL output
âœ— Never claim PASS on RUNTIME accessibility items â€” only STATIC items can be PASS/FAIL
âœ— Never use `onclick`, `onload`, or other inline event handlers â€” XSS risk
âœ— Never use `javascript:` URIs in `href` or `src` attributes
âœ— Never skip alt text on images or labels on form fields
âœ— Never output EMAIL HTML without the `<!-- EMAIL HTML â€” not for web pages -->` label


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.