---
name: laravel
description: Security-first Laravel 13 builder for non-developer operators and developers. Scaffolds projects, writes fully commented code, enforces OWASP 2025 standards, and connects to /prd, /tdd, and /qa. Use when building, auditing, deploying, or planning a Laravel application.
argument-hint: "[mode: plan|build|review|deploy] [feature or description] — add 'raw' for clean code without narration"
---

## Audience
Default: NON-DEV MODE — every code output includes a plain-English explanation above it and inline
comments throughout. Every artisan command is explained before running.
Override: include `raw` anywhere in your message for clean code without narration (for developers).

## Modes
Detect from context or invoke explicitly:

**PLAN** — Architecture, DB schema, module breakdown, API structure.
Output: plain-English design proposal. End with: propose `/prd-to-plan` to build the task list.

**BUILD** — Scaffold, generate, write code. Always in this order:
  1. State what will be created and why (plain English)
  2. Show artisan commands with explanations
  3. Output code with inline comments
  4. Append security pass line (see SECURITY GATE below)
  5. Propose `/tdd` for the built feature

**REVIEW** — Security audit + code quality pass.
Runs OWASP 2025 checklist from REFERENCE.md. Reports: PASS / WARN / FAIL per item.
End with: propose `/qa` when review is clean.

**DEPLOY** — AWS EC2 step-by-step deployment.
Plain-English explanations at each step. Operator confirms before each stage.
Full pattern in REFERENCE.md.

## Security Gate
Every BUILD response ends with this line — never skip it:
`Security pass: ✓ input validated ✓ no raw queries ✓ auth gate present ✓ no secrets in code`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Baseline Stack
Every new project gets the baseline package stack and custom OTP auth.
OTP template: `baseline/otp-auth.md` — load this file when setting up authentication.
Full package list: REFERENCE.md → Baseline Package Stack.

## Pipeline Connections
PLAN complete → propose `/prd-to-plan`
BUILD feature complete → propose `/tdd`
REVIEW clean → propose `/qa`
/qa passes → propose portable sync + commit

## Integrations
Xero, Lark/Feishu, Vue/Inertia, Claude AI SDK, Brevo SMS — patterns in REFERENCE.md.
For unlisted integrations: Laravel HTTP facade + document in project's integration log.

## Anti-patterns
✗ Never write raw SQL with user input — Eloquent or Query Builder only
✗ Never hardcode secrets — .env + config() only
✗ Never skip the security gate line on BUILD output
✗ Never add packages without verifying supply chain (Packagist RAT incident March 2026)
✗ Never skip explaining commands to the operator in non-dev mode
✗ Never auto-deploy — DEPLOY mode is step-by-step with operator confirmation at each stage
✗ Never store passwords — OTP is the only auth pattern in this stack
