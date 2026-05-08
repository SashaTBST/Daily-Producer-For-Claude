---
name: go
description: Security-first Go 1.26 builder covering idiomatic patterns, Gin HTTP APIs, table-driven tests, and OWASP-aligned security defaults. Use when writing Go code, scaffolding a Gin API, writing table-driven tests, or reviewing Go for security and correctness issues.
argument-hint: "[mode: write|http|test|review] [description] â€” add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/go = Go 1.26, Gin, standard library, go.mod modules, golangci-lint.
/node for JavaScript servers. /python for Python APIs. /csharp for .NET.

## Non-Negotiable
Never swallow errors â€” every returned error is checked or wrapped, never assigned to `_`.
Always wrap errors with `fmt.Errorf("...: %w", err)` â€” preserve the error chain.
Never hardcode secrets â€” environment variables only.
Always parameterized SQL â€” never string concatenation in queries.
Always pass `context.Context` as first parameter in functions doing I/O.
`defer` for all cleanup (file close, mutex unlock, DB rows close).

## Modes

**WRITE** â€” Structs, interfaces, functions, CLI tools, services.
Go 1.26 defaults: error wrapping with `%w`, `errors.Is`/`errors.As`, `context.Context`, defer for cleanup, interfaces for abstraction (define at the point of use, not definition).
Generics where they reduce duplication â€” avoid over-engineering.
NON-DEV: explain error handling and defer on first use in each session.
End with: propose `/go http` if HTTP endpoints needed.

**HTTP** â€” Gin API scaffolding.
Default: Gin v1 (`gin.Default()` for dev, `gin.New()` + explicit middleware for prod).
Input binding via `c.ShouldBindJSON(&req)` â€” always check the returned error.
Middleware: recovery, logger, explicit CORS (`github.com/gin-contrib/cors` â€” no AllowAllOrigins in prod).
Secrets from env vars. Database connections via parameterized queries.
End with: propose `/go test` for endpoint coverage.

**TEST** â€” Table-driven tests with testify.
Pattern: define `[]struct{name, input, want}` test cases, range over with `t.Run`.
Framework: standard `testing` package + `testify/assert` for assertions.
Mock interfaces â€” not concrete types. `httptest.NewRecorder()` for HTTP handler tests.
End with: propose `/go review` when coverage looks complete.

**REVIEW** â€” Security + correctness audit.
Checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Flags: swallowed errors (`_ = someFunc()`), unwrapped errors, missing context, goroutine leaks, interface nil trap, `sql.DB` without parameterized queries, hardcoded secrets.
End with: propose running `golangci-lint run`. Propose `/qa` when clean.

## Security Gate
Every WRITE and HTTP response ends with this line â€” never skip it:
`Security pass: âœ“ no swallowed errors âœ“ errors wrapped with %w âœ“ context propagated âœ“ no hardcoded secrets âœ“ parameterized SQL`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/node` â€” JavaScript equivalent; propose if operator prefers JS
- `/python` â€” Python equivalent; propose if operator prefers Python
- `/qa` â€” propose after REVIEW passes clean

## Pipeline Connections
WRITE complete â†’ propose `/go http` if HTTP layer needed
HTTP complete â†’ propose `/go test`
TEST complete â†’ propose `/go review`
REVIEW clean â†’ propose `/qa`
/qa passes â†’ portable sync + commit

## Anti-patterns
âœ— Never assign errors to `_` â€” always handle or explicitly log and return
âœ— Never return bare errors without `fmt.Errorf("context: %w", err)` wrapping
âœ— Never hardcode credentials, tokens, or connection strings
âœ— Never build SQL with string interpolation or concatenation
âœ— Never start a goroutine without a mechanism to stop it (context cancellation or done channel)
âœ— Never compare `err != nil` after `errors.As` â€” use `errors.Is` for sentinel checks
âœ— Never use `AllowAllOrigins: true` in production Gin CORS config
âœ— Never skip `defer rows.Close()` after `db.Query()`
âœ— Never skip the security gate line on WRITE or HTTP output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.