---
name: api-design
description: Security-first REST API design skill covering endpoint design, OpenAPI 3.1 spec generation, OWASP API Security Top 10, and non-developer friendly guidance. Use when designing a new API, generating an OpenAPI spec, reviewing an existing API for design or security issues, or running an API security audit.
argument-hint: "[mode: design|openapi|review|security] [description] — add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/api-design = REST API design, OpenAPI 3.1, OWASP API Security Top 10.
GraphQL/gRPC/tRPC are out of scope — reference only. REST is the default for non-dev operators.
/node, /python, /go, /laravel for implementation. /sql or /nosql for data layer.

## Non-Negotiable
Nouns in URLs, never verbs — `/users/{id}` not `/getUser`.
Always version the API — `/v1/` prefix minimum.
Rate limiting on every public endpoint — no unbounded request paths.
Never return 200 for an error — use correct HTTP status codes.
Every endpoint requires documented auth — no public endpoints without explicit justification.
Cursor-based pagination for large collections — offset pagination breaks under data changes.

## Modes

**DESIGN** — Endpoint structure, naming, resource hierarchy.
REST defaults: plural nouns, URL versioning (`/v1/`), standard HTTP verbs (GET/POST/PUT/PATCH/DELETE).
Consistent error format: `{"error": "message", "code": "ERROR_CODE", "details": {}}`.
Idempotency: PUT is always idempotent; POST is not — use idempotency keys for payments/orders.
NON-DEV: explain when to use POST vs PUT vs PATCH on first use in each session.
End with: propose `/api-design openapi` to generate the spec.

**OPENAPI** — OpenAPI 3.1 spec generation.
Output: valid YAML spec with `openapi: 3.1.0` header.
Include: paths, request/response schemas, status codes, security schemes (Bearer/API key).
Scalar for modern docs (preferred 2026). Swagger UI for familiarity. Redoc for static.
End with: propose `/api-design security` to audit the generated spec.

**REVIEW** — API design audit.
Checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Flags: verbs in URLs, wrong status codes (200 for errors), missing versioning, missing rate limit headers, no pagination, undefined error format, over-fetching (returning unnecessary fields).
End with: propose `/api-design security` for OWASP check. Propose `/qa` when clean.

**SECURITY** — OWASP API Security Top 10 audit.
Checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Covers: BOLA (missing object-level auth), broken auth, excessive data exposure, rate limiting, mass assignment, SSRF, security misconfiguration.
End with: propose `/qa` when clean.

## Security Gate
Every DESIGN and OPENAPI response ends with this line — never skip it:
`Security pass: ✓ nouns in URLs ✓ versioned ✓ rate limiting documented ✓ correct status codes ✓ auth on all endpoints`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/node` / `/python` / `/go` / `/laravel` — implementation of this API design
- `/sql` / `/nosql` — data layer for this API
- `/qa` — propose after SECURITY passes clean

## Anti-patterns
✗ Never use verbs in REST URLs — nouns only
✗ Never return HTTP 200 for an error response
✗ Never design an API without versioning
✗ Never expose a public endpoint without rate limiting
✗ Never return more fields than the client needs — field filtering or dedicated DTOs
✗ Never use offset pagination for large or frequently-changing datasets
✗ Never document an endpoint without documenting its auth requirement
✗ Never scope in GraphQL/gRPC/tRPC unless operator explicitly asks for comparison
✗ Never skip the security gate line on DESIGN or OPENAPI output


Every response ends with NEXT MOVE.