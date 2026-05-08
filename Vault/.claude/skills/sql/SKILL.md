---
name: sql
description: Security-first SQL builder covering parameterized queries, schema migrations, query optimization, and OWASP injection prevention. Primary dialect PostgreSQL; MySQL/SQLite secondary. Use when writing SQL queries, designing schemas, reviewing SQL for injection risk, or optimizing slow queries.
argument-hint: "[mode: write|migrate|review|optimize] [description] â€” add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/sql = SQL queries, schema design, migrations, indexes, query optimization.
Primary: PostgreSQL. Secondary: MySQL 8+ / SQLite. MSSQL: reference only.
/nosql for MongoDB/Valkey. /node, /python, /go for ORM-layer code.

## Non-Negotiable
Parameterized queries always â€” never string concatenation or interpolation in SQL.
Never `SELECT *` â€” always name the columns you need.
`GRANT` principle of least privilege â€” application user never gets superuser access.
Every foreign key has an index. Every `WHERE` column used in production queries has an index.
Schema changes via migration files (Flyway/Alembic/golang-migrate) â€” never manual ALTER in production.

## Modes

**WRITE** â€” Queries, schemas, views, CTEs, stored procedures.
PostgreSQL defaults: CTEs (`WITH` clauses for readability), window functions, JSONB for flexible data, `ON CONFLICT DO UPDATE` for upserts, `RETURNING` to avoid second queries.
NULL semantics: always use `IS NULL` / `IS NOT NULL` â€” never `= NULL`.
NON-DEV: explain CTEs and window functions on first use in each session.
End with: propose `/sql migrate` for schema changes, `/sql optimize` after writing complex queries.

**MIGRATE** â€” Schema migration files.
Output: numbered migration file (`V001__description.sql` for Flyway, `001_description.sql` for others).
Always include rollback: `-- Rollback:` comment with the inverse operation.
Destructive migrations (DROP, ALTER..NOT NULL) require a data check comment.
End with: propose `/sql review` to audit the migration for safety.

**REVIEW** â€” SQL security + correctness audit.
Checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Flags: string-concatenated queries (injection risk), `SELECT *`, missing indexes on FK/WHERE cols, missing NULL checks, overly permissive GRANTs.
End with: propose `/sql optimize` if performance issues found. Propose `/qa` when clean.

**OPTIMIZE** â€” Query performance analysis.
Request `EXPLAIN ANALYZE` output for slow queries. Identify: sequential scans on large tables, missing indexes, N+1 patterns, unneeded sort operations.
Index recommendations: composite, partial, covering â€” with rationale.
End with: propose running the query with `EXPLAIN (ANALYZE, BUFFERS)` to verify.

## Security Gate
Every WRITE and MIGRATE response ends with this line â€” never skip it:
`Security pass: âœ“ parameterized queries âœ“ no SELECT * âœ“ principle of least privilege âœ“ indexes present âœ“ NULL handled correctly`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/nosql` â€” document/cache alternative; propose if schema-less data model fits better
- `/node` / `/python` / `/go` â€” ORM layer on top of this SQL
- `/qa` â€” propose after REVIEW passes clean

## Anti-patterns
âœ— Never use string concatenation or interpolation to build SQL queries
âœ— Never use `SELECT *` in production queries â€” name every column
âœ— Never run schema changes manually in production â€” use migration files
âœ— Never grant application users superuser or DBA roles
âœ— Never use `WHERE x = NULL` â€” always `WHERE x IS NULL`
âœ— Never add a foreign key without adding an index on that column
âœ— Never skip a rollback comment in migration files
âœ— Never skip the security gate line on WRITE or MIGRATE output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.