---
name: api-design
description: Security-first REST API design skill covering endpoint design, OpenAPI 3.1 spec generation, OWASP API Security Top 10, and non-developer friendly guidance. Use when designing a new API, generating an OpenAPI spec, reviewing an existing API for design or security issues, or running an API security audit.
argument-hint: "[mode: design|openapi|review|security] [description] â€” add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/api-design = REST API design, OpenAPI 3.1, OWASP API Security Top 10.
GraphQL/gRPC/tRPC are out of scope â€” reference only. REST is the default for non-dev operators.
/node, /python, /go, /laravel for implementation. /sql or /nosql for data layer.

## Non-Negotiable
Nouns in URLs, never verbs â€” `/users/{id}` not `/getUser`.
Always version the API â€” `/v1/` prefix minimum.
Rate limiting on every public endpoint â€” no unbounded request paths.
Never return 200 for an error â€” use correct HTTP status codes.
Every endpoint requires documented auth â€” no public endpoints without explicit justification.
Cursor-based pagination for large collections â€” offset pagination breaks under data changes.

## Modes

**DESIGN** â€” Endpoint structure, naming, resource hierarchy.
REST defaults: plural nouns, URL versioning (`/v1/`), standard HTTP verbs (GET/POST/PUT/PATCH/DELETE).
Consistent error format: `{"error": "message", "code": "ERROR_CODE", "details": {}}`.
Idempotency: PUT is always idempotent; POST is not â€” use idempotency keys for payments/orders.
NON-DEV: explain when to use POST vs PUT vs PATCH on first use in each session.
End with: propose `/api-design openapi` to generate the spec.

**OPENAPI** â€” OpenAPI 3.1 spec generation.
Output: valid YAML spec with `openapi: 3.1.0` header.
Include: paths, request/response schemas, status codes, security schemes (Bearer/API key).
Scalar for modern docs (preferred 2026). Swagger UI for familiarity. Redoc for static.
End with: propose `/api-design security` to audit the generated spec.

**REVIEW** â€” API design audit.
Checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Flags: verbs in URLs, wrong status codes (200 for errors), missing versioning, missing rate limit headers, no pagination, undefined error format, over-fetching (returning unnecessary fields).
End with: propose `/api-design security` for OWASP check. Propose `/qa` when clean.

**SECURITY** â€” OWASP API Security Top 10 audit.
Checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Covers: BOLA (missing object-level auth), broken auth, excessive data exposure, rate limiting, mass assignment, SSRF, security misconfiguration.
End with: propose `/qa` when clean.

## Security Gate
Every DESIGN and OPENAPI response ends with this line â€” never skip it:
`Security pass: âœ“ nouns in URLs âœ“ versioned âœ“ rate limiting documented âœ“ correct status codes âœ“ auth on all endpoints`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/node` / `/python` / `/go` / `/laravel` â€” implementation of this API design
- `/sql` / `/nosql` â€” data layer for this API
- `/qa` â€” propose after SECURITY passes clean

## Anti-patterns
âœ— Never use verbs in REST URLs â€” nouns only
âœ— Never return HTTP 200 for an error response
âœ— Never design an API without versioning
âœ— Never expose a public endpoint without rate limiting
âœ— Never return more fields than the client needs â€” field filtering or dedicated DTOs
âœ— Never use offset pagination for large or frequently-changing datasets
âœ— Never document an endpoint without documenting its auth requirement
âœ— Never scope in GraphQL/gRPC/tRPC unless operator explicitly asks for comparison
âœ— Never skip the security gate line on DESIGN or OPENAPI output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.