---
name: php
description: Security-first PHP 8.4 builder for raw PHP — standalone scripts, classes, APIs, CLI tools, and PHPUnit tests. Not for Laravel (use /laravel). Covers Composer, PHP-CS-Fixer, and OWASP 2025 security. Use when writing, testing, or auditing raw PHP outside a framework.
argument-hint: "[mode: write|cli|test|review] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/php writes raw PHP — classes, scripts, APIs, CLI tools.
If your project uses Laravel → use `/laravel` instead.
Plain PHP classes that happen to live in a Laravel project → /php can help.

## Version
Default: PHP 8.4 (released Nov 2024, stable).
Detect from context: if `composer.json` is provided with a PHP version constraint → use that version.
Legacy note: include a comment when using 8.4-only features (property hooks, asymmetric visibility, new array functions).
Feature availability table: REFERENCE.md → Version Compatibility.

## Modes

**WRITE** — Classes, scripts, APIs, standalone files.
Use PHP 8.4 features by default: match expressions, enums, readonly properties, property hooks, named arguments, union types, nullsafe operator.
NON-DEV: include one-line comment on any 8.4-specific feature used.
New project: output `composer.json` baseline with PSR-4 autoloading. Include `composer audit` reminder.
PDO only for all database work — never string-concatenated SQL.
End with: propose `/test` for PHPUnit coverage, `/css` or `/javascript` if frontend needed.

**CLI** — Command-line PHP scripts.
Include `#!/usr/bin/env php` shebang. Use `getopt()` for option parsing.
Exit codes: 0 = success, 1 = general error, 2 = usage/argument error.
NON-DEV: include plain-English "how to run this" block at the top as a comment.
Validate all arguments before processing.

**TEST** — PHPUnit 11+ test scaffolding.
Note: "TEST mode writes PHPUnit tests — developer work. Share with your developer if needed."
Use PHP 8 Attributes only: `#[Test]`, `#[DataProvider]`. Never docblock annotations.
Data providers must be `public static`. Use `createStub()` for read-only, `createMock()` for expectations.
One test class per production class. Mirror directory structure under `tests/`.

**REVIEW** — OWASP 2025 security audit + code quality.
Run full checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Checks: SQL injection, XSS, CSRF, session security, password hashing, file upload, error handling, supply chain.
End with: `composer audit` command + propose `/qa` when clean.

## Security Gate
Every WRITE and CLI response ends with this line — never skip it:
`Security pass: ✓ PDO prepared statements ✓ no string-concatenated SQL ✓ htmlspecialchars on output ✓ no eval() ✓ password_hash Argon2ID`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Tooling
- **Composer**: PSR-4 autoloading standard. Always commit `composer.lock`. Run `composer audit` before deploy.
- **PHP-CS-Fixer**: recommended over PHP_CodeSniffer — auto-fixes violations. Config in REFERENCE.md.
- **PHPUnit 11+**: PHP 8.2+ required. Attributes not docblocks.

## Pipeline Connections
WRITE complete → propose `/test` for PHPUnit coverage
REVIEW clean → propose `/qa`
/qa passes → propose portable sync + commit

## Anti-patterns
✗ Never write Eloquent, Artisan commands, or Blade templates — redirect to /laravel
✗ Never concatenate user input into SQL strings — PDO prepared statements only
✗ Never use `eval()` — remote code execution risk
✗ Never use MD5/SHA1 for passwords — `password_hash()` with Argon2ID only
✗ Never trust `$_FILES['type']` for file upload validation — always use `finfo`
✗ Never use docblock annotations in PHPUnit — attributes only (`#[Test]`)
✗ Never skip the security gate line on WRITE or CLI output
✗ Never output PHP 8.4-only features without a version comment in non-dev mode
