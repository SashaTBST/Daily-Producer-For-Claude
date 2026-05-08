---
name: php
description: Security-first PHP 8.4 builder for raw PHP â€” standalone scripts, classes, APIs, CLI tools, and PHPUnit tests. Not for Laravel (use /laravel). Covers Composer, PHP-CS-Fixer, and OWASP 2025 security. Use when writing, testing, or auditing raw PHP outside a framework.
argument-hint: "[mode: write|cli|test|review] [description] â€” add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/php writes raw PHP â€” classes, scripts, APIs, CLI tools.
If your project uses Laravel â†’ use `/laravel` instead.
Plain PHP classes that happen to live in a Laravel project â†’ /php can help.

## Version
Default: PHP 8.4 (released Nov 2024, stable).
Detect from context: if `composer.json` is provided with a PHP version constraint â†’ use that version.
Legacy note: include a comment when using 8.4-only features (property hooks, asymmetric visibility, new array functions).
Feature availability table: REFERENCE.md â†’ Version Compatibility.

## Modes

**WRITE** â€” Classes, scripts, APIs, standalone files.
Use PHP 8.4 features by default: match expressions, enums, readonly properties, property hooks, named arguments, union types, nullsafe operator.
NON-DEV: include one-line comment on any 8.4-specific feature used.
New project: output `composer.json` baseline with PSR-4 autoloading. Include `composer audit` reminder.
PDO only for all database work â€” never string-concatenated SQL.
End with: propose `/test` for PHPUnit coverage, `/css` or `/javascript` if frontend needed.

**CLI** â€” Command-line PHP scripts.
Include `#!/usr/bin/env php` shebang. Use `getopt()` for option parsing.
Exit codes: 0 = success, 1 = general error, 2 = usage/argument error.
NON-DEV: include plain-English "how to run this" block at the top as a comment.
Validate all arguments before processing.

**TEST** â€” PHPUnit 11+ test scaffolding.
Note: "TEST mode writes PHPUnit tests â€” developer work. Share with your developer if needed."
Use PHP 8 Attributes only: `#[Test]`, `#[DataProvider]`. Never docblock annotations.
Data providers must be `public static`. Use `createStub()` for read-only, `createMock()` for expectations.
One test class per production class. Mirror directory structure under `tests/`.

**REVIEW** â€” OWASP 2025 security audit + code quality.
Run full checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Checks: SQL injection, XSS, CSRF, session security, password hashing, file upload, error handling, supply chain.
End with: `composer audit` command + propose `/qa` when clean.

## Security Gate
Every WRITE and CLI response ends with this line â€” never skip it:
`Security pass: âœ“ PDO prepared statements âœ“ no string-concatenated SQL âœ“ htmlspecialchars on output âœ“ no eval() âœ“ password_hash Argon2ID`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Tooling
- **Composer**: PSR-4 autoloading standard. Always commit `composer.lock`. Run `composer audit` before deploy.
- **PHP-CS-Fixer**: recommended over PHP_CodeSniffer â€” auto-fixes violations. Config in REFERENCE.md.
- **PHPUnit 11+**: PHP 8.2+ required. Attributes not docblocks.

## Pipeline Connections
WRITE complete â†’ propose `/test` for PHPUnit coverage
REVIEW clean â†’ propose `/qa`
/qa passes â†’ propose portable sync + commit

## Anti-patterns
âœ— Never write Eloquent, Artisan commands, or Blade templates â€” redirect to /laravel
âœ— Never concatenate user input into SQL strings â€” PDO prepared statements only
âœ— Never use `eval()` â€” remote code execution risk
âœ— Never use MD5/SHA1 for passwords â€” `password_hash()` with Argon2ID only
âœ— Never trust `$_FILES['type']` for file upload validation â€” always use `finfo`
âœ— Never use docblock annotations in PHPUnit â€” attributes only (`#[Test]`)
âœ— Never skip the security gate line on WRITE or CLI output
âœ— Never output PHP 8.4-only features without a version comment in non-dev mode


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.