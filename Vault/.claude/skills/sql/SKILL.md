---
name: sql
description: Security-first SQL builder covering parameterized queries, schema migrations, query optimization, and OWASP injection prevention. Primary dialect PostgreSQL; MySQL/SQLite secondary. Use when writing SQL queries, designing schemas, reviewing SQL for injection risk, or optimizing slow queries.
argument-hint: "[mode: write|migrate|review|optimize] [description] — add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/sql = SQL queries, schema design, migrations, indexes, query optimization.
Primary: PostgreSQL. Secondary: MySQL 8+ / SQLite. MSSQL: reference only.
/nosql for MongoDB/Valkey. /node, /python, /go for ORM-layer code.

## Non-Negotiable
Parameterized queries always — never string concatenation or interpolation in SQL.
Never `SELECT *` — always name the columns you need.
`GRANT` principle of least privilege — application user never gets superuser access.
Every foreign key has an index. Every `WHERE` column used in production queries has an index.
Schema changes via migration files (Flyway/Alembic/golang-migrate) — never manual ALTER in production.

## Modes

**WRITE** — Queries, schemas, views, CTEs, stored procedures.
PostgreSQL defaults: CTEs (`WITH` clauses for readability), window functions, JSONB for flexible data, `ON CONFLICT DO UPDATE` for upserts, `RETURNING` to avoid second queries.
NULL semantics: always use `IS NULL` / `IS NOT NULL` — never `= NULL`.
NON-DEV: explain CTEs and window functions on first use in each session.
End with: propose `/sql migrate` for schema changes, `/sql optimize` after writing complex queries.

**MIGRATE** — Schema migration files.
Output: numbered migration file (`V001__description.sql` for Flyway, `001_description.sql` for others).
Always include rollback: `-- Rollback:` comment with the inverse operation.
Destructive migrations (DROP, ALTER..NOT NULL) require a data check comment.
End with: propose `/sql review` to audit the migration for safety.

**REVIEW** — SQL security + correctness audit.
Checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Flags: string-concatenated queries (injection risk), `SELECT *`, missing indexes on FK/WHERE cols, missing NULL checks, overly permissive GRANTs.
End with: propose `/sql optimize` if performance issues found. Propose `/qa` when clean.

**OPTIMIZE** — Query performance analysis.
Request `EXPLAIN ANALYZE` output for slow queries. Identify: sequential scans on large tables, missing indexes, N+1 patterns, unneeded sort operations.
Index recommendations: composite, partial, covering — with rationale.
End with: propose running the query with `EXPLAIN (ANALYZE, BUFFERS)` to verify.

## Security Gate
Every WRITE and MIGRATE response ends with this line — never skip it:
`Security pass: ✓ parameterized queries ✓ no SELECT * ✓ principle of least privilege ✓ indexes present ✓ NULL handled correctly`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/nosql` — document/cache alternative; propose if schema-less data model fits better
- `/node` / `/python` / `/go` — ORM layer on top of this SQL
- `/qa` — propose after REVIEW passes clean

## Anti-patterns
✗ Never use string concatenation or interpolation to build SQL queries
✗ Never use `SELECT *` in production queries — name every column
✗ Never run schema changes manually in production — use migration files
✗ Never grant application users superuser or DBA roles
✗ Never use `WHERE x = NULL` — always `WHERE x IS NULL`
✗ Never add a foreign key without adding an index on that column
✗ Never skip a rollback comment in migration files
✗ Never skip the security gate line on WRITE or MIGRATE output


Every response ends with NEXT MOVE.