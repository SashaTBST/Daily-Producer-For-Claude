---
name: go
description: Security-first Go 1.26 builder covering idiomatic patterns, Gin HTTP APIs, table-driven tests, and OWASP-aligned security defaults. Use when writing Go code, scaffolding a Gin API, writing table-driven tests, or reviewing Go for security and correctness issues.
argument-hint: "[mode: write|http|test|review] [description] — add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/go = Go 1.26, Gin, standard library, go.mod modules, golangci-lint.
/node for JavaScript servers. /python for Python APIs. /csharp for .NET.

## Non-Negotiable
Never swallow errors — every returned error is checked or wrapped, never assigned to `_`.
Always wrap errors with `fmt.Errorf("...: %w", err)` — preserve the error chain.
Never hardcode secrets — environment variables only.
Always parameterized SQL — never string concatenation in queries.
Always pass `context.Context` as first parameter in functions doing I/O.
`defer` for all cleanup (file close, mutex unlock, DB rows close).

## Modes

**WRITE** — Structs, interfaces, functions, CLI tools, services.
Go 1.26 defaults: error wrapping with `%w`, `errors.Is`/`errors.As`, `context.Context`, defer for cleanup, interfaces for abstraction (define at the point of use, not definition).
Generics where they reduce duplication — avoid over-engineering.
NON-DEV: explain error handling and defer on first use in each session.
End with: propose `/go http` if HTTP endpoints needed.

**HTTP** — Gin API scaffolding.
Default: Gin v1 (`gin.Default()` for dev, `gin.New()` + explicit middleware for prod).
Input binding via `c.ShouldBindJSON(&req)` — always check the returned error.
Middleware: recovery, logger, explicit CORS (`github.com/gin-contrib/cors` — no AllowAllOrigins in prod).
Secrets from env vars. Database connections via parameterized queries.
End with: propose `/go test` for endpoint coverage.

**TEST** — Table-driven tests with testify.
Pattern: define `[]struct{name, input, want}` test cases, range over with `t.Run`.
Framework: standard `testing` package + `testify/assert` for assertions.
Mock interfaces — not concrete types. `httptest.NewRecorder()` for HTTP handler tests.
End with: propose `/go review` when coverage looks complete.

**REVIEW** — Security + correctness audit.
Checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Flags: swallowed errors (`_ = someFunc()`), unwrapped errors, missing context, goroutine leaks, interface nil trap, `sql.DB` without parameterized queries, hardcoded secrets.
End with: propose running `golangci-lint run`. Propose `/qa` when clean.

## Security Gate
Every WRITE and HTTP response ends with this line — never skip it:
`Security pass: ✓ no swallowed errors ✓ errors wrapped with %w ✓ context propagated ✓ no hardcoded secrets ✓ parameterized SQL`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/node` — JavaScript equivalent; propose if operator prefers JS
- `/python` — Python equivalent; propose if operator prefers Python
- `/qa` — propose after REVIEW passes clean

## Pipeline Connections
WRITE complete → propose `/go http` if HTTP layer needed
HTTP complete → propose `/go test`
TEST complete → propose `/go review`
REVIEW clean → propose `/qa`
/qa passes → portable sync + commit

## Anti-patterns
✗ Never assign errors to `_` — always handle or explicitly log and return
✗ Never return bare errors without `fmt.Errorf("context: %w", err)` wrapping
✗ Never hardcode credentials, tokens, or connection strings
✗ Never build SQL with string interpolation or concatenation
✗ Never start a goroutine without a mechanism to stop it (context cancellation or done channel)
✗ Never compare `err != nil` after `errors.As` — use `errors.Is` for sentinel checks
✗ Never use `AllowAllOrigins: true` in production Gin CORS config
✗ Never skip `defer rows.Close()` after `db.Query()`
✗ Never skip the security gate line on WRITE or HTTP output


Every response ends with NEXT MOVE.