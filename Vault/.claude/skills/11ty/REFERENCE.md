# 11ty Skill — Reference
Eleventy v3.1 | Last verified: 2026-04-27

## Research Sources
- Eleventy v3.1 release: https://www.11ty.dev/blog/eleventy-v3-1/
- Eleventy 2025 review: https://www.11ty.dev/blog/review-2025/
- WordPress migration (official): https://www.11ty.dev/docs/migrate/wordpress/
- Migration real-world notes: https://sixohthree.com/blog/2025/eleventy-migration-notes/
- CSP on 11ty (2025): https://nooshu.com/blog/2025/02/04/configuring-your-content-security-policy-on-your-development-environment-in-11ty/
- Security headers for static sites: https://nooshu.com/blog/2024/12/28/securing-your-static-website-with-http-response-headers/
- OWASP CSP cheat sheet: https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html
- Deployment options: https://www.11ty.dev/docs/deployment/
- Stripe + 11ty serverless: https://sia.codes/posts/serverless-ecommerce-store/
- Vercel vs Netlify 2026: https://tech-insider.org/vercel-vs-netlify-2026/
- 11ty + S3 discussion: https://github.com/11ty/eleventy/discussions/2011

---

## Baseline Package Stack
Install on every new 11ty project.

**Core**
- `@11ty/eleventy@^3.1` — pin to v3.1 (not latest) until v4 is stable
- `@11ty/eleventy-img` — image optimisation (lazy load, WebP, responsive sizes)
- `@11ty/eleventy-navigation` — hierarchical nav menus
- `@11ty/eleventy-plugin-rss` — RSS feed (include even if not used yet — SEO value)

**Security**
- `@jackdbd/eleventy-plugin-content-security-policy` — CSP header generation per page

**Dev tooling**
- No extra packages needed — 11ty's dev server is built in (`npx @11ty/eleventy --serve`)

**Supply chain rule:** verify author, stars, last commit, and npm listing before adding any package.
Pin all versions in package.json. Run `npm audit` before every deploy.

---

## CSP Security Checklist (Static Sites)
Run on every REVIEW. Report each as PASS / WARN / FAIL.

| # | Check | Implementation |
|---|---|---|
| 1 | No API keys in output | Grep built `_site/` for any key patterns — none must appear |
| 2 | No secrets in `_data/` | All external data fetched at build via env vars, not hardcoded |
| 3 | CSP headers defined | `_headers` (Netlify) / `vercel.json` / CloudFront policy — not missing |
| 4 | CSP report-only first | Deploy as `Content-Security-Policy-Report-Only` before enforcing |
| 5 | SRI on all 3rd party scripts | Every CDN-hosted script has `integrity` + `crossorigin` attributes |
| 6 | Analytics approved in CSP | GTM/GA/Plausible domains whitelisted in `script-src` |
| 7 | No inline scripts (where possible) | Use nonces or hashes if inline scripts required |
| 8 | HTTPS enforced | HSTS header present. No mixed content. |
| 9 | X-Frame-Options | Set to `DENY` or `SAMEORIGIN` to prevent clickjacking |
| 10 | Referrer-Policy | Set to `strict-origin-when-cross-origin` |

---

## Deployment Decision Table
Use this to pick the right platform before running DEPLOY mode.

| Condition | Recommended |
|---|---|
| New client site, no existing infrastructure | **Netlify** — simplest, fastest setup |
| Client or team already on Vercel | **Vercel** — zero-config for 11ty |
| Client has existing AWS / connecting to Laravel backend | **S3 + CloudFront** — matches AWS stack |
| Client needs form handling without serverless code | **Netlify** — Netlify Forms built in |
| Maximum control over caching + CDN rules | **S3 + CloudFront** |

---

## Deployment Patterns

### Netlify
Connect GitHub repo → site auto-builds on push.
Build command: `npx @11ty/eleventy`
Publish directory: `_site`
Security headers: define in `_headers` file at project root (see starter-config.md).
Forms: add `netlify` attribute to any HTML form — Netlify handles submissions automatically.
Free tier: 100 build minutes/month (2025). Sufficient for most client sites.

### Vercel
Connect GitHub repo → zero-config detection for 11ty.
Build command: `npx @11ty/eleventy`
Output directory: `_site`
Security headers: define in `vercel.json` (see starter-config.md).
Serverless functions: create in `/api/` folder — auto-deployed as Vercel Functions.

### S3 + CloudFront
Build locally or via CI → upload `_site/` to S3 bucket → CloudFront serves it.
S3 bucket: static website hosting enabled. Block all public access — serve via CloudFront only.
CloudFront: set default root object to `index.html`. Custom error page: `404.html`.
Security headers: CloudFront Response Headers Policy — attach to distribution.
CI/CD: GitHub Actions workflow recommended (aws-actions/configure-aws-credentials + s3 sync).
Matches the /laravel AWS stack — use the same account, IAM roles, and VPC where possible.

---

## Migration Patterns

### wordpress → 11ty
**Plain English:** WordPress stores your content in a database. We extract it all and turn it into static files that 11ty can build from.

Step 1 — dry run first (won't touch anything):
```bash
npx @11ty/import wordpress https://your-site.com --dryrun
```
Step 2 — run the import (downloads all posts, pages, images):
```bash
npx @11ty/import wordpress https://your-site.com
```
Step 3 — verify URL structure matches original exactly.
Step 4 — build and check for 404s:
```bash
npx @11ty/eleventy --serve
```
Step 5 — **URL preservation gate:** compare every public URL from WordPress against built output.
Only propose DEPLOY after this gate passes. Broken URLs = lost SEO for client.
Step 6 — set up redirects for any URL that changed (Netlify `_redirects` file / Vercel `vercel.json` / CloudFront).

### html → 11ty
**Plain English:** We take your existing HTML pages and turn them into reusable templates — so shared parts (header, footer, nav) only exist once instead of being copy-pasted across every page.

Step 1 — identify repeated elements across pages (header, footer, nav).
Step 2 — extract into `_includes/` as Nunjucks/Liquid partials.
Step 3 — convert page content into template files using `{% include %}`.
Step 4 — set up front matter (title, description, layout) on each page.
Step 5 — URL preservation gate: every original URL must resolve in built output.
Step 6 — test all internal links before DEPLOY.

### other-ssg → 11ty
**Plain English:** Content (markdown files, data files) can usually be moved directly. Templates need to be rewritten in Nunjucks, Liquid, or 11ty's other supported languages.

Step 1 — copy all content files (`.md`, `.json`, `.yaml`) directly into `src/`.
Step 2 — identify which template language the old SSG used.
Step 3 — rewrite templates in Nunjucks (recommended) or Liquid.
Step 4 — map old configuration options to `.eleventy.js` equivalents.
Step 5 — URL preservation gate (same as above).
Step 6 — rebuild and compare output against original.

---

## Integration Registry
Last verified: 2026-04-27

**Rule: any integration requiring an API key must use a serverless function.
Never expose API keys in templates, _data files, or built HTML output.**

### Stripe (payments / ecom)
Pattern: serverless function creates a Checkout Session, returns redirect URL.
Product catalog: fetch from Stripe API at build time (safe — read-only, public prices).
```js
// netlify/functions/create-checkout.js — runs on Netlify, never exposed to browser
const stripe = require('stripe')(process.env.STRIPE_SECRET_KEY); // key in env vars only

exports.handler = async (event) => {
  const { priceId } = JSON.parse(event.body);

  const session = await stripe.checkout.sessions.create({
    payment_method_types: ['card'],
    line_items: [{ price: priceId, quantity: 1 }],
    mode: 'payment',
    success_url: `${process.env.SITE_URL}/success`,
    cancel_url: `${process.env.SITE_URL}/cancel`,
  });

  return {
    statusCode: 200,
    body: JSON.stringify({ url: session.url }),
  };
};
```
Frontend calls this function via `fetch('/api/create-checkout', { method: 'POST', body: ... })`.

### Forms / Signups
- **Netlify Forms**: add `netlify` attribute to `<form>` tag. Zero config. Submissions in Netlify dashboard.
- **Formspree**: `<form action="https://formspree.io/f/[id]">` — no function needed, GDPR-note required.
- **Custom endpoint**: serverless function receives POST, validates, sends to CRM/email.

### Analytics
- **Google Analytics / GTM**: add script to `<head>` via `_includes/head.njk`. Whitelist in CSP `script-src`.
- **Plausible** (privacy-first, GDPR-compliant, no cookie banner needed): `<script defer data-domain="yoursite.com" src="https://plausible.io/js/script.js"></script>`
- Add SRI integrity attribute to any analytics script loaded from a CDN.

### Xero (financials)
API key must never appear in static output — serverless function required.
Pattern identical to Stripe function above: function calls Xero API, returns sanitised data.
Use `webfox/laravel-xero-oauth2` if connecting via a Laravel backend instead.

### Lark / Feishu
Same rule: serverless function only. Bearer token in env vars.
Pattern: function calls `https://open.larksuite.com/open-apis/[endpoint]`, returns data to frontend.

### CMS (Sanity, Contentlayer, Decap)
Not in baseline — too project-specific. Common pattern: fetch CMS data in `.eleventy.js` via API at build time (safe — build-time only, key in env var). Document in project's own integration log.

---

## Serverless Function Rule
If your integration needs any of these → it needs a serverless function:
- API secret key or token
- Writing data anywhere (database, CRM, email service)
- Processing payments
- Handling form submissions with any business logic

Where functions live:
- Netlify: `/netlify/functions/[name].js`
- Vercel: `/api/[name].js`
- AWS: Lambda function (connect via API Gateway, call from frontend `fetch`)

Functions run in Node.js by default. Keep them small — one job per function.
