# SQL Skill — Reference
PostgreSQL primary / MySQL 8+ secondary / Flyway migrations | Last verified: 2026-04-30

## Research Sources
- OWASP SQL Injection Prevention: https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html
- SQL injection 2026 guide: https://hivesecurity.gitlab.io/blog/sql-injection-complete-guide-2026/
- Database migration tools 2026: https://toolradar.com/blog/best-database-migration-tools
- PostgreSQL tutorial 2026: https://thelinuxcode.com/postgresql-tutorial-2026-from-first-query-to-production-grade-patterns/
- N+1 query problem: https://blog.newtum.com/n1-query-problem-in-sql/
- Common SQL mistakes 2026: https://medium.com/@BuildAndLearn/you-think-you-know-sql-but-these-10-mistakes-prove-youre-still-writing-baby-queries-34c35892840a

---

## Parameterized Queries — Always

```sql
-- ✗ NEVER — string concatenation (SQL injection risk)
-- "SELECT * FROM users WHERE email = '" + userInput + "'"

-- ✓ PostgreSQL parameterized ($1, $2, ...)
SELECT id, email, name FROM users WHERE email = $1 AND active = $2;

-- ✓ MySQL parameterized (?)
SELECT id, email, name FROM users WHERE email = ? AND active = ?;

-- ✓ Named params (SQLAlchemy, Prisma, etc.)
SELECT id, email, name FROM users WHERE email = :email AND active = :active;
```

---

## CTEs — Readability over Subqueries

```sql
-- Complex query as readable pipeline (PostgreSQL / MySQL 8+)
WITH active_users AS (
    SELECT id, email, name, created_at
    FROM users
    WHERE active = true
      AND created_at > NOW() - INTERVAL '30 days'
),
user_order_counts AS (
    SELECT user_id, COUNT(*) AS order_count
    FROM orders
    GROUP BY user_id
)
SELECT
    u.id,
    u.email,
    u.name,
    COALESCE(o.order_count, 0) AS order_count
FROM active_users u
LEFT JOIN user_order_counts o ON u.id = o.user_id
ORDER BY order_count DESC;
```

---

## Window Functions

```sql
-- RANK within a partition — no subquery needed
SELECT
    user_id,
    order_total,
    RANK() OVER (PARTITION BY user_id ORDER BY order_total DESC) AS rank_by_total,
    ROW_NUMBER() OVER (ORDER BY created_at) AS global_row,
    SUM(order_total) OVER (PARTITION BY user_id) AS user_lifetime_value
FROM orders;
```

---

## PostgreSQL JSONB

```sql
-- Store flexible nested data
ALTER TABLE products ADD COLUMN attributes JSONB;

-- Query into JSONB
SELECT name FROM products
WHERE attributes @> '{"color": "red"}'::jsonb;  -- @> containment operator

-- Extract value
SELECT attributes->>'color' AS color FROM products WHERE id = 1;

-- Index JSONB for fast queries
CREATE INDEX idx_products_attributes ON products USING GIN (attributes);
```

---

## Upsert (PostgreSQL)

```sql
-- Insert or update if conflict on unique key
INSERT INTO user_settings (user_id, theme, notifications)
VALUES ($1, $2, $3)
ON CONFLICT (user_id)
DO UPDATE SET
    theme = EXCLUDED.theme,
    notifications = EXCLUDED.notifications,
    updated_at = NOW();
```

---

## Migration File Format (Flyway)

```sql
-- V001__create_users_table.sql
CREATE TABLE users (
    id          SERIAL PRIMARY KEY,
    email       VARCHAR(255) NOT NULL UNIQUE,
    name        VARCHAR(255) NOT NULL,
    active      BOOLEAN NOT NULL DEFAULT true,
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_users_email ON users (email);
CREATE INDEX idx_users_active_created ON users (active, created_at);

-- Rollback:
-- DROP TABLE IF EXISTS users;
```

---

## GRANT — Principle of Least Privilege

```sql
-- Create a restricted application user (never use superuser)
CREATE USER app_user WITH PASSWORD 'use-env-var-not-this';

-- Grant only what's needed
GRANT CONNECT ON DATABASE mydb TO app_user;
GRANT USAGE ON SCHEMA public TO app_user;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO app_user;
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA public TO app_user;

-- Read-only reporting user
CREATE USER reporting_user WITH PASSWORD 'use-env-var-not-this';
GRANT CONNECT ON DATABASE mydb TO reporting_user;
GRANT USAGE ON SCHEMA public TO reporting_user;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO reporting_user;
```

---

## REVIEW Checklist

| # | Check | What to look for | Report |
|---|-------|-----------------|--------|
| 1 | Parameterized queries | String concatenation or interpolation in SQL — injection risk | PASS/FAIL |
| 2 | No SELECT * | `SELECT *` in any non-exploratory query | PASS/WARN |
| 3 | FK indexes | Foreign key columns without a corresponding index | PASS/WARN |
| 4 | WHERE col indexes | Columns used in WHERE/JOIN ON without indexes | PASS/WARN |
| 5 | NULL handling | `= NULL` or `!= NULL` instead of `IS NULL` / `IS NOT NULL` | PASS/FAIL |
| 6 | Migration rollback | Migration file missing `-- Rollback:` comment | PASS/WARN |
| 7 | Least privilege | Application DB user has superuser or DBA role | PASS/FAIL |
| 8 | N+1 risk | Queries inside loops — use JOIN or batch query instead | PASS/WARN |
| 9 | Leading wildcard | `LIKE '%term'` or `LIKE '%term%'` breaks index usage | PASS/WARN |
| 10 | EXPLAIN planned | Complex query has no EXPLAIN ANALYZE verification | PASS/WARN |
