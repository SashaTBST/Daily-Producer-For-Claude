---
name: laravel
description: Security-first Laravel 13 builder for non-developer operators and developers. Scaffolds projects, writes fully commented code, enforces OWASP 2025 standards, and connects to /prd, /tdd, and /qa. Use when building, auditing, deploying, or planning a Laravel application.
argument-hint: "[mode: plan|build|review|deploy] [feature or description] â€” add 'raw' for clean code without narration"
---

## Audience
Default: NON-DEV MODE â€” every code output includes a plain-English explanation above it and inline
comments throughout. Every artisan command is explained before running.
Override: include `raw` anywhere in your message for clean code without narration (for developers).

## Modes
Detect from context or invoke explicitly:

**PLAN** â€” Architecture, DB schema, module breakdown, API structure.
Output: plain-English design proposal. End with: propose `/prd-to-plan` to build the task list.

**BUILD** â€” Scaffold, generate, write code. Always in this order:
  1. State what will be created and why (plain English)
  2. Show artisan commands with explanations
  3. Output code with inline comments
  4. Append security pass line (see SECURITY GATE below)
  5. Propose `/tdd` for the built feature

**REVIEW** â€” Security audit + code quality pass.
Runs OWASP 2025 checklist from REFERENCE.md. Reports: PASS / WARN / FAIL per item.
End with: propose `/qa` when review is clean.

**DEPLOY** â€” AWS EC2 step-by-step deployment.
Plain-English explanations at each step. Operator confirms before each stage.
Full pattern in REFERENCE.md.

## Security Gate
Every BUILD response ends with this line â€” never skip it:
`Security pass: âœ“ input validated âœ“ no raw queries âœ“ auth gate present âœ“ no secrets in code`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Baseline Stack
Every new project gets the baseline package stack and custom OTP auth.
OTP template: `baseline/otp-auth.md` â€” load this file when setting up authentication.
Full package list: REFERENCE.md â†’ Baseline Package Stack.

## Pipeline Connections
PLAN complete â†’ propose `/prd-to-plan`
BUILD feature complete â†’ propose `/tdd`
REVIEW clean â†’ propose `/qa`
/qa passes â†’ propose portable sync + commit

## Integrations
Xero, Lark/Feishu, Vue/Inertia, Claude AI SDK, Brevo SMS â€” patterns in REFERENCE.md.
For unlisted integrations: Laravel HTTP facade + document in project's integration log.

## Anti-patterns
âœ— Never write raw SQL with user input â€” Eloquent or Query Builder only
âœ— Never hardcode secrets â€” .env + config() only
âœ— Never skip the security gate line on BUILD output
âœ— Never add packages without verifying supply chain (Packagist RAT incident March 2026)
âœ— Never skip explaining commands to the operator in non-dev mode
âœ— Never auto-deploy â€” DEPLOY mode is step-by-step with operator confirmation at each stage
âœ— Never store passwords â€” OTP is the only auth pattern in this stack


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.