---
name: rust
description: Security-first Rust 2024 edition builder covering ownership patterns, Axum web APIs, built-in testing, and supply chain security. Use when writing Rust code, scaffolding an Axum API, writing tests, or reviewing Rust for unsafe blocks and correctness issues.
argument-hint: "[mode: write|web|test|review] [description] â€” add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/rust = Rust 2024 edition, Axum, Tokio async, cargo toolchain, CLI tools.
Embedded/no_std, WASM, and FFI are out of scope â€” too much complexity for non-dev operators.
/go and /node for simpler server-side alternatives.

## Non-Negotiable
Never use `.unwrap()` in non-test code â€” use `?`, `.expect("reason")`, or match on Result/Option.
`unsafe` blocks require a comment explaining why safety is upheld â€” never uncommented unsafe.
`cargo-audit` before any production release â€” check for known CVEs in dependencies.
Integer overflow: release builds wrap silently â€” use checked arithmetic (`checked_add`, `saturating_add`) for user-controlled values.
Derive `serde` validation patterns â€” never trust deserialized input without validation.

## Modes

**WRITE** â€” Structs, enums, traits, functions, CLI tools.
Rust 2024 defaults: `Result<T, E>` and `Option<T>` (not panics), `?` operator for propagation, derive macros (`Debug`, `Clone`, `serde::Serialize/Deserialize`), iterators over imperative loops.
Error handling: `thiserror` for library errors (typed variants), `anyhow` for application errors (propagation).
NON-DEV: explain ownership and borrowing on first use. Teach `clone()` as a last resort, not a first solution.
End with: propose `/rust web` if HTTP endpoints needed.

**WEB** â€” Axum API scaffolding.
Default: Axum 0.8 + Tokio runtime. `Router` for routing, extractors for input (`Json`, `Path`, `Query`).
Validation: `validator` crate on request structs. Return typed errors via `impl IntoResponse`.
Secrets from environment variables. Never hardcode credentials.
End with: propose `/rust test` for endpoint coverage.

**TEST** â€” Built-in Rust test scaffolding.
Framework: `#[test]` + `#[tokio::test]` for async. Integration tests in `tests/` directory.
Pattern: Arrange â†’ Act â†’ Assert. `assert_eq!`, `assert!`, `assert_matches!`.
`unwrap()` is acceptable in test code only â€” panics are desirable in tests.
End with: propose `/rust review` when coverage looks complete.

**REVIEW** â€” Safety + correctness audit.
Checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Flags: `unwrap()` in non-test code, uncommented `unsafe` blocks, unchecked integer arithmetic on user input, `cargo-audit` not configured, deserialized data used without validation.
End with: propose running `cargo clippy` and `cargo audit`. Propose `/qa` when clean.

## Security Gate
Every WRITE and WEB response ends with this line â€” never skip it:
`Security pass: âœ“ no unwrap() in production paths âœ“ unsafe documented âœ“ no hardcoded secrets âœ“ input validated âœ“ cargo-audit configured`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/go` â€” simpler server alternative; propose if operator finds Rust ownership difficult
- `/node` â€” JavaScript equivalent for non-performance-critical servers
- `/qa` â€” propose after REVIEW passes clean

## Pipeline Connections
WRITE complete â†’ propose `/rust web` if HTTP layer needed
WEB complete â†’ propose `/rust test`
TEST complete â†’ propose `/rust review`
REVIEW clean â†’ propose `/qa`
/qa passes â†’ portable sync + commit

## Anti-patterns
âœ— Never use `.unwrap()` in non-test production code
âœ— Never write `unsafe` without a comment explaining the invariant being upheld
âœ— Never hardcode secrets or API keys
âœ— Never use user-controlled integers in arithmetic without checked operations
âœ— Never skip `cargo-audit` before a production release
âœ— Never use `clone()` as first solution to borrow checker errors â€” rethink ownership
âœ— Never use `panic!` or `todo!` in production paths
âœ— Never scope in embedded/no_std/WASM/FFI work â€” redirect to Rust docs
âœ— Never skip the security gate line on WRITE or WEB output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.