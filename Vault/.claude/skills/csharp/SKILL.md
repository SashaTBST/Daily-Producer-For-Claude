---
name: csharp
description: Security-first C# 14 / .NET 10 builder covering classes, records, Minimal APIs, xUnit testing, and OWASP-aligned security patterns. Use when writing C# code, scaffolding ASP.NET Core APIs, writing xUnit tests, or reviewing C# for security issues.
argument-hint: "[mode: write|api|test|review] [description] — add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/csharp = C# 14 + .NET 10 — classes, records, services, Minimal APIs, xUnit tests, security review.
Unity is out of scope — Unity uses Mono, not .NET. Use /csharp for .NET only.
/node handles JavaScript servers. /csharp handles .NET servers.

## Non-Negotiable
`#nullable enable` always — at the top of every file. No exceptions.
Never hardcode secrets — environment variables or Azure Key Vault only.
Never use BinaryFormatter — removed in .NET 9+. Use System.Text.Json.
Never use string concatenation in SQL — EF Core parameterized queries only.
Always `await` async calls — fire-and-forget silently swallows exceptions.
`using` or `IAsyncDisposable` for any resource that implements IDisposable.

## Modes

**WRITE** — Classes, records, interfaces, services, console apps.
C# 14 defaults: primary constructors, records for immutable data, `#nullable enable` at file top.
Async-first: return `Task<T>` or `ValueTask<T>`. Avoid `void` for async methods.
NON-DEV: explain nullable (`?`) and async (`await`) on first use in each session.
End with: propose `/csharp api` if an HTTP endpoint is needed.

**API** — ASP.NET Core Minimal API scaffolding.
Default: Minimal APIs (not MVC). Top-level statements. No Startup.cs.
Inject services via `builder.Services`. Use `WebApplication.CreateBuilder(args)`.
Security defaults: input validation via FluentValidation, explicit CORS allow-list (never `AllowAnyOrigin` in prod), secrets from env vars.
End with: propose `/csharp test` for endpoint test coverage.

**TEST** — xUnit + Moq test scaffolding.
Framework: xUnit (`[Fact]` for single, `[Theory]` for parameterized). Moq for mocking.
Pattern: Arrange → Act → Assert. One assertion per test where possible.
Test isolation: xUnit creates a new class instance per test — no shared state.
End with: propose `/csharp review` when test coverage looks complete.

**REVIEW** — Security + pattern audit.
Checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Flags: missing `#nullable enable`, hardcoded secrets, string-concatenated SQL, bare `catch (Exception)`, missing `using` for IDisposable, unawaited Tasks.
End with: propose `/qa` when clean.

## Security Gate
Every WRITE and API response ends with this line — never skip it:
`Security pass: ✓ nullable enabled ✓ no hardcoded secrets ✓ no string SQL ✓ resources disposed ✓ async awaited`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/node` — JavaScript server equivalent; propose if operator is on a JS stack
- `/typescript` — if building a frontend that calls this API
- `/react` — if building a Blazor or API-backed React frontend
- `/qa` — propose after REVIEW passes clean

## Pipeline Connections
WRITE complete → propose `/csharp api` if HTTP layer needed
API complete → propose `/csharp test`
TEST complete → propose `/csharp review`
REVIEW clean → propose `/qa`
/qa passes → portable sync + commit

## Anti-patterns
✗ Never omit `#nullable enable` — always at file top
✗ Never hardcode connection strings, API keys, or passwords
✗ Never use BinaryFormatter — JSON serialization only
✗ Never build SQL strings with string interpolation or concatenation
✗ Never use `async void` — use `async Task` even for event handlers where possible
✗ Never leave IDisposable without `using` or explicit Dispose
✗ Never catch bare `Exception` without rethrowing or logging with context
✗ Never use `AllowAnyOrigin()` in production CORS config
✗ Never skip the security gate line on WRITE or API output
✗ Never scope Unity into this skill — redirect to game engine docs
