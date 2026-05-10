# website-qa — Reference

## Tool Commands

All tools run via `npx` if not globally installed. Each writes JSON to a named temp file — read, then delete.

### Quick Pass

**Lighthouse** (single page)
```bash
npx lighthouse [url] \
  --output=json \
  --output-path=./lh-report.json \
  --only-categories=performance,accessibility,best-practices,seo \
  --chrome-flags="--headless --no-sandbox --disable-gpu" \
  --quiet
```

**axe-core**
```bash
npx @axe-core/cli [url] --stdout > axe-report.json
```

**linkinator** (single page)
```bash
npx linkinator [url] --format=json --skip "^mailto" > link-report.json
```

### Full Audit (additional tools)

**unlighthouse** (site-wide — outputs to `./unlighthouse-out/reports/`)
```bash
npx unlighthouse-ci --site [url] --reporter jsonExpanded --output-path ./unlighthouse-out
```

**pa11y-ci** (create config inline)
```bash
echo '{"urls":["[url]"],"standard":"WCAG2AA","reporter":"json"}' > .pa11yci.json
npx pa11y-ci --config .pa11yci.json > pa11y-report.json
# Sitemap-based sweep:
npx pa11y-ci --sitemap [url]/sitemap.xml --config .pa11yci.json > pa11y-report.json
```

**linkinator** (recurse — all internal links)
```bash
npx linkinator [url] --recurse --format=json --skip "^mailto" > link-full-report.json
```

**Sitemap + robots.txt**
```bash
curl -s -o sitemap.xml -w "%{http_code}" [url]/sitemap.xml
curl -s [url]/robots.txt
```

**Analytics detection** (URL)
```bash
curl -s [url] | grep -iE "GTM-[A-Z0-9]+|G-[A-Z0-9]+|UA-[0-9]+-[0-9]+|googletagmanager|google-analytics"
```

**Analytics detection** (source — if repo access)
```bash
grep -r "GTM-\|G-\|UA-\|googletagmanager\|google-analytics" [repo]/src \
  --include="*.html" --include="*.njk" --include="*.js" -l
```

**Redirect chains**
```bash
curl -ILs [url]/[path] | grep -E "^HTTP|^Location"
```

## Coverage Matrix

| Category | Quick Pass | Full Audit |
|----------|-----------|-----------|
| Performance | Lighthouse (1 page) | unlighthouse (all pages) |
| Accessibility | axe + Lighthouse | pa11y-ci + axe + Lighthouse |
| SEO (meta, og, canonical) | Lighthouse | unlighthouse (all pages) |
| Broken links (on page) | linkinator | linkinator recurse |
| 301/redirect chains | — | linkinator (3xx flagged) |
| WCAG AA | axe | pa11y-ci |
| Sitemap | — | curl + validate |
| robots.txt | — | curl |
| Analytics/GTM detected | — | grep/curl |
| Security headers | Lighthouse best-practices | unlighthouse |
| Mobile emulation | Lighthouse (mobile flag) | unlighthouse |
| Button/CTA hrefs | — | linkinator |
| Search Console | Manual only | Manual only |
| JS onclick handlers | Manual only | Manual only |
| Auth-gated pages | Not reachable — document | Not reachable — document |

## Deduplication Rule

Same URL + same issue type from 2+ tools → file once.
- A11y priority: axe > pa11y > Lighthouse (most specific wins)
- Perf/SEO priority: Lighthouse > unlighthouse (single-page data is more granular)

## Report Template

```markdown
# Website QA — [Site] — [YYYY-MM-DD] — [QUICK PASS | FULL AUDIT]

URL: [url]
Tools run: [list]
Auth-gated pages excluded: [list or NONE]

## Scores
| Category | Score | Status |
|----------|-------|--------|
| Performance | — | PASS ≥80 / WARN 50–79 / FAIL <50 |
| Accessibility | — | PASS ≥90 / WARN 70–89 / FAIL <70 |
| Best Practices | — | PASS ≥90 |
| SEO | — | PASS ≥90 |

## Findings

### CRITICAL
- [ ] [description] — [tool] — [url]

### HIGH
- [ ] [description] — [tool] — [url]

### MEDIUM
- [ ] [description] — [tool] — [url]

### LOW
- [ ] [description] — [tool] — [url]

## Issues Filed
| Title | Severity | Ref |
|-------|----------|-----|
| [title] | HIGH | #[n] |

## Manual Checks (Full Audit)
- [ ] Google Search Console — verified / not verified / unknown
- [ ] Analytics data flowing — verified / not verified / unknown
- [ ] Visual design consistency across breakpoints — pass / issues noted
- [ ] Button JS onclick handlers (non-href) — spot-checked / not checked
- [ ] Auth-gated pages reviewed manually — yes / no
- [ ] Legal pages reviewed by operator — yes / pending
- [ ] sitemap.xml — found / missing at [url]/sitemap.xml
- [ ] robots.txt — found / missing [disallow rules if any]
```

## GitHub Issue Template

```
Title: [CRITICAL|HIGH|MEDIUM|LOW] [Category] — [brief description]

## What's wrong
[Behaviour — no file paths or line numbers]

## Impact
[User / SEO / accessibility / security impact]

## Where
URL: [specific page]
Device: [desktop / mobile / all]
Tool: [Lighthouse / axe / linkinator / pa11y / manual]

## Expected
[What should happen]

## Suggested fix
[Optional]

Labels: website-qa, [severity-lowercase], [category]
```

## Chrome Check (Windows PowerShell)

```powershell
where.exe chrome 2>$null; where.exe chromium 2>$null
# Common path: C:\Program Files\Google\Chrome\Application\chrome.exe
# Pass to Lighthouse:
# --chrome-path="C:\Program Files\Google\Chrome\Application\chrome.exe"
```

## Global Install (optional — faster repeated runs)

```bash
npm install -g lighthouse @axe-core/cli linkinator pa11y pa11y-ci unlighthouse
```
