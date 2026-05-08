---
name: 11ty
description: Security-first Eleventy (11ty) v3.1 builder for non-developer operators and developers. Builds from scratch, migrates existing sites, and connects to /prd, /tdd, and /qa. Covers Netlify, Vercel, and S3+CloudFront deployment. Use when building, converting, auditing, or deploying an 11ty static site.
argument-hint: "[mode: plan|build|convert|deploy|review] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` anywhere in your message for clean output without narration (for developers).

## Modes
Detect from context or invoke explicitly:

**PLAN** — Architecture, folder structure, data strategy, template plan.
Output: plain-English design. End with: propose `/prd-to-plan`.

**BUILD** — Scaffold files, write templates, configure plugins. Always in this order:
  1. State what will be created and why (plain English)
  2. Show npm/terminal commands with explanations
  3. Output files with inline comments
  4. Append security gate line (see SECURITY GATE)
  5. Propose `/tdd`

**CONVERT** — Migrate an existing site to 11ty.
First: ask the source type if not stated — `wordpress` / `html` / `other-ssg`.
Then: follow the matching migration pattern in REFERENCE.md.
Hard gate: URL preservation check must be confirmed before DEPLOY is proposed.

**DEPLOY** — Deploy to Netlify, Vercel, or S3+CloudFront.
First: use the decision table in REFERENCE.md to recommend the right option.
Then: step-by-step with plain-English explanations. Operator confirms each stage.

**REVIEW** — Security audit + performance header check.
Runs CSP checklist from REFERENCE.md. Reports: PASS / WARN / FAIL per item.
End with: propose `/qa` when review is clean.

## Security Gate
Every BUILD response ends with this line — never skip it:
`Security pass: ✓ no API keys in output ✓ CSP headers defined ✓ SRI on 3rd party scripts ✓ no secrets in _data files`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Baseline
New projects: load `baseline/starter-config.md` for standard folder structure, .eleventy.js, and CSP headers.
⚠ v4 note: this skill is pinned to v3.1. If your project is on v4+, verify patterns against the v4 changelog.

## Pipeline Connections
PLAN complete → propose `/prd-to-plan`
BUILD feature complete → propose `/tdd`
REVIEW clean → propose `/qa`
/qa passes → propose portable sync + commit

## Integrations
Stripe, forms, analytics, Xero, Lark/Feishu — patterns in REFERENCE.md.
Rule: any integration needing an API key must go through a serverless function — never in static output.

## Anti-patterns
✗ Never put API keys, tokens, or secrets in templates, _data files, or built output
✗ Never skip the security gate line on BUILD output
✗ Never skip URL preservation check in CONVERT mode before proposing DEPLOY
✗ Never add npm packages without checking supply chain
✗ Never assume server-side code is available — 11ty output is static HTML
✗ Never skip explaining commands to the operator in non-dev mode
✗ Never auto-deploy — DEPLOY mode is step-by-step with operator confirmation at each stage
