# HTML Skill — Reference
HTML5 (web) | HTML 4.01 (email) | IAB HTML5 (ads) | Last verified: 2026-04-27

## Research Sources
- HTML Living Standard: https://html.spec.whatwg.org/
- WCAG 2.2 AA (current standard): https://www.w3.org/WAI/WCAG22/quickref/
- MDN HTML reference: https://developer.mozilla.org/en-US/docs/Web/HTML
- Can I Email compatibility: https://www.canemailuse.com/
- Email on Acid Outlook guide: https://www.emailonacid.com/blog/article/email-development/outlook-fix-width-not-working/
- Litmus email client market share 2025: https://www.litmus.com/email-client-market-share
- Google Gmail clipping threshold: https://support.google.com/mail/thread/198867?hl=en
- IAB Display & Mobile Advertising Creative Guidelines 2024: https://www.iab.com/guidelines/iab-new-ad-portfolio/
- IAB HTML5 for Digital Advertising: https://www.iab.com/guidelines/html5-for-digital-advertising/
- OWASP XSS Prevention Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html
- CSP (Content Security Policy) MDN: https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP

---

## WCAG 2.2 AA Checklist — REVIEW Mode

### STATIC — Skill Can Audit (report PASS / WARN / FAIL)

| # | Check | Implementation |
|---|---|---|
| 1 | Images have alt text | All `<img>` tags have non-empty `alt` attribute (or `alt=""` for decorative) |
| 2 | Form inputs have labels | Every `<input>` has a matching `<label>` (for= ID) or `aria-label` |
| 3 | Heading hierarchy | H1 → H2 → H3 in logical order, no skipped levels |
| 4 | Page has a single H1 | One H1 per page |
| 5 | Links are descriptive | No bare "click here" or "read more" — link text describes destination |
| 6 | Language declared | `<html lang="en">` (or correct locale) present |
| 7 | Page has `<title>` | Non-empty `<title>` element in `<head>` |
| 8 | Buttons have accessible names | `<button>` has text or `aria-label` |
| 9 | Tables have headers | `<th>` with `scope` attribute on data tables |
| 10 | No deprecated elements | No `<font>`, `<center>`, `<b>` (non-semantic), `<i>` (non-semantic) |
| 11 | No inline event handlers | No `onclick`, `onload`, `onmouseover` etc in HTML |
| 12 | No `javascript:` URIs | No `href="javascript:..."` or `src="javascript:..."` |
| 13 | Colour not sole indicator | Not auditable from HTML alone — flag if inline colour styling used without other cue |
| 14 | Skip navigation link | `<a href="#main">Skip to main content</a>` at top of page |
| 15 | ARIA roles not misused | Landmark roles used correctly (main, nav, header, footer) |

### RUNTIME — Manual Testing Required (list, do not claim PASS)

| # | Check | How to test |
|---|---|---|
| R1 | Keyboard navigation | Tab through all interactive elements in browser — logical order, no traps |
| R2 | Screen reader | Test with NVDA (Windows) or VoiceOver (Mac/iOS) — all content announced correctly |
| R3 | Colour contrast 4.5:1 | Check with browser DevTools or https://webaim.org/resources/contrastchecker/ |
| R4 | Focus visible | Tab through — focus ring visible on all interactive elements |
| R5 | Zoom to 200% | Page content readable and functional at 200% zoom (no overflow, no loss of function) |
| R6 | Error messages | Form errors are announced to screen readers (aria-live or role=alert) |

---

## XSS Prevention Rules

**Never output user-supplied data without escaping:**
- Blade: `{{ $variable }}` — auto-escaped. `{!! $variable !!}` — NEVER for user input.
- Nunjucks: `{{ variable }}` — auto-escaped. `{{ variable | safe }}` — NEVER for user input.
- Vanilla HTML: escape before inserting: `<p>{{ safe_output }}</p>`

**Forbidden patterns:**
- `onclick="..."`, `onload="..."`, any inline event handler
- `href="javascript:..."` or `src="javascript:..."`
- `<script>` tags with user content interpolated
- `innerHTML` set from user input (JavaScript concern — flag when writing JS)

**CSP-compatible HTML:**
- All scripts in external `.js` files (no inline `<script>` blocks where possible)
- All styles in external `.css` files or `<style>` in `<head>` (no inline `style=` where possible)
- If inline scripts required: use nonce or hash — never `'unsafe-inline'`

---

## Email Client Compatibility Rules

**Supported in all major clients (safe to use):**
- Tables, `<td>`, `<tr>`, `colspan`, `rowspan`
- Inline `style=""` with basic CSS (font, color, background, padding, border)
- `<img>` with absolute URLs (no relative paths in email)
- `<a href="https://...">` links

**Avoid (broken in Outlook or Gmail):**
- `<div>` for layout (use `<table>` instead)
- `margin: auto` for centering (use `align="center"` on table or `<center>`)
- CSS `max-width` (use fixed px widths — max 600px)
- Web fonts (`@font-face`) — Outlook ignores them
- `background-size`, `transform`, `flexbox`, `grid`
- JavaScript of any kind
- External stylesheets — all CSS must be inline

**Outlook MSO fix — required for all email widths:**
```html
<!--[if mso]>
<table width="600" cellspacing="0" cellpadding="0" border="0" align="center">
<tr><td>
<![endif]-->
  <!-- email content here -->
<!--[if mso]>
</td></tr></table>
<![endif]-->
```

**Gmail 102KB limit:**
Gmail clips the email body when HTML exceeds 102KB. A "View entire message" link appears — most users don't click it. Keep total HTML under 100KB. Images are hosted externally — they don't count toward the limit.

---

## IAB Ad Specifications (2024 Guidelines)

| Format | Dimensions | Max file size |
|---|---|---|
| Leaderboard | 728×90 | 200KB |
| Medium Rectangle | 300×250 | 200KB |
| Half Page | 300×600 | 200KB |
| Large Rectangle | 336×280 | 200KB |
| Skyscraper | 160×600 | 200KB |
| Billboard | 970×250 | 200KB |

**Rules for all ads:**
- Entry point: `index.html` (always)
- Max 15 initial HTTP requests (images + scripts combined)
- `clickTag` variable required: `var clickTag = "https://example.com";`
- Click handler: `window.open(window.clickTag)` — not a hardcoded URL
- No audio autoplay
- All CSS inline or in `<style>` block in `<head>`
- Animation max 30 seconds (IAB standard); some publishers allow loop after 3 plays

---

## Cross-Skill Integration

| Skill | Context | Integration |
|---|---|---|
| `/laravel` | Blade templates | Use `{{ }}` for escaped output, `@if`, `@foreach`, `@extends`, `@section` |
| `/11ty` | Nunjucks templates | Use `{{ }}` variables, `{% include %}`, front matter for page data |
| `/css` | Styling | Propose after WRITE mode for external stylesheet; link in `<head>` |
| `/javascript` | Interactivity | Propose after WRITE mode; script tag at bottom of `<body>` |
