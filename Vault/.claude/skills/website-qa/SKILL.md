---
name: website-qa
description: Orchestrates CLI tools (Lighthouse, axe-core, linkinator, unlighthouse, pa11y) to audit any website for performance, accessibility, SEO, broken links, security, analytics setup, sitemap, and UX quality. Two modes — Quick Pass (single-page, per-change) and Full Audit (site-wide, post-deploy). Outputs a Markdown report to the vault and files GitHub issues per finding. Use after any website push, before a major release, or when running post-deploy QA. Works with any URL; optimised for projects with code/repo access.
argument-hint: "[url] [--quick|--full]"
---

Runs structured website QA via CLI tools. Parses JSON output, applies severity tiers, writes report, files issues.

## Invocation

```
/website-qa [url] [--quick|--full]
```

If no URL: ask. If no mode: ask — "Quick Pass or Full Audit?"
On startup: create `_AI/MEMORY/qa-reports/` if not exists. Check Chrome available. Confirm GitHub repo if filing issues.

Code access: if operator provides a local repo path alongside URL — scan source files for meta tags, analytics scripts, sitemap path, inline JS handlers. URL-only → curl + CLI tools only.

## Modes

**Quick Pass** — per-change, single page. Run after every push.
Tools: Lighthouse · @axe-core/cli · linkinator (no recurse)
Covers: performance · accessibility · SEO meta · best practices · broken links on page

**Full Audit** — post-deploy, site-wide. Run after major releases.
Tools: All Quick Pass + unlighthouse · pa11y-ci · linkinator (recurse) · sitemap-validator · robots.txt · analytics detection
Covers: All Quick Pass + every page · WCAG2AA at scale · all links · sitemap valid · GTM/GA detected · redirect chains · robots.txt crawlability

## Severity Tiers

CRITICAL — broken pages, security headers absent, a11y blocker, 4xx
HIGH — SEO fail, performance < 50, missing canonical/og, WCAG AA violation
MEDIUM — performance 50–79, non-critical missing tags, UX inconsistency
LOW — cosmetic, legibility, copy

## Workflow

1. **Setup** — confirm URL, mode, repo name, GitHub-enabled (y/n). Chrome check. Create report folder.
2. **Run tools** — execute per mode (see REFERENCE.md). Each tool writes JSON to named temp file.
3. **Parse** — read temp files. Extract findings. Delete temp files.
4. **Triage** — apply severity. Dedup: same URL + issue type across tools → file once (axe > pa11y > Lighthouse for a11y).
5. **Report** — write to `_AI/MEMORY/qa-reports/[site]-[YYYY-MM-DD]-[quick|full].md`.
6. **File issues** — GitHub: `gh issue create --repo [repo]` per CRITICAL/HIGH. Markdown: append per project file. MEDIUM/LOW: report only unless requested.
7. **Summary** — scores table + issue count per severity tier.

See REFERENCE.md for tool commands, report template, issue template, manual checks list.

## Anti-patterns

- Full Audit after a minor CSS change — Quick Pass is enough
- One issue for multiple failures — one issue per discrete finding
- File paths or line numbers in issues — describe behaviour only
- Treating "analytics detected" as "analytics configured" — script presence only
- Filing issues for auth-gated pages tools can't reach — note as limitation in report
- Skipping the summary table — always output scores before closing

## QA

Before closing this skill session:
- [ ] Mode confirmed before any tool runs
- [ ] Chrome available (or tools skipped with reason noted)
- [ ] All tool outputs parsed — no silent failures
- [ ] Temp files deleted after parse
- [ ] Severity applied to every finding
- [ ] Deduplication applied across tools
- [ ] Report written to `_AI/MEMORY/qa-reports/`
- [ ] Issues filed (GitHub) or appended (Markdown)
- [ ] Summary table output with scores + issue count
- [ ] Every response ended with NEXT MOVE

See REFERENCE.md for tool commands, flags, templates.

Every response ends with NEXT MOVE.
