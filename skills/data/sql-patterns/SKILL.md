---
name: sql-patterns
description: Common SQL patterns and anti-patterns for reliable data work
category: data
tags: [sql, databases, patterns]
---

# SQL patterns

Practical SQL patterns that avoid common pitfalls and produce correct results.

## When to use

- Writing queries for reports, analytics, or application data access
- Debugging slow or incorrect SQL
- Reviewing database schema changes

## Steps

### Use CTEs for readability

Before:
```sql
SELECT u.name, o.total
FROM users u
JOIN (
  SELECT user_id, SUM(amount) as total
  FROM orders
  WHERE status = 'completed'
  GROUP BY user_id
) o ON u.id = o.user_id
WHERE u.created_at > '2024-01-01'
```

After:
```sql
WITH completed_orders AS (
  SELECT user_id, SUM(amount) as total
  FROM orders
  WHERE status = 'completed'
  GROUP BY user_id
)
SELECT u.name, co.total
FROM users u
JOIN completed_orders co ON u.id = co.user_id
WHERE u.created_at > '2024-01-01'
```

CTEs are not slower in modern databases (PostgreSQL inlines them). Readability wins.

### Window functions instead of self-joins

Before:
```sql
SELECT u.*, latest.total
FROM users u
JOIN orders latest ON u.id = latest.user_id
WHERE latest.created_at = (
  SELECT MAX(created_at) FROM orders WHERE user_id = u.id
)
```

After:
```sql
SELECT DISTINCT ON (user_id)
  user_id, amount, created_at
FROM orders
ORDER BY user_id, created_at DESC
```

Or with window functions:
```sql
SELECT * FROM (
  SELECT *,
    ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY created_at DESC) as rn
  FROM orders
) ranked
WHERE rn = 1
```

### ALWAYS use parameterized queries

```python
# NEVER do this
cursor.execute(f"SELECT * FROM users WHERE email = '{email}'")

# ALWAYS do this
cursor.execute("SELECT * FROM users WHERE email = %s", (email,))
```

SQL injection isn't just a security risk — it's also a correctness issue with special characters.

### Pagination that works

Offset pagination breaks with inserts/deletes:
```sql
-- BAD: skips/duplicates rows if data changes between pages
SELECT * FROM posts ORDER BY created_at LIMIT 10 OFFSET 20
```

Cursor pagination is stable:
```sql
-- GOOD: stable regardless of data changes
SELECT * FROM posts
WHERE created_at < '2024-01-15T10:30:00Z'
ORDER BY created_at DESC
LIMIT 10
```

## Pitfalls

- **SELECT *** — explicitly name columns. Schema changes silently break wildcards.
- **Missing indexes on JOIN/WHERE columns** — check `EXPLAIN ANALYZE` before deploying.
- **N+1 queries** — if you're running a query in a loop, use a JOIN or batch instead.
- **Floating point for money** — use `NUMERIC`/`DECIMAL`, never `FLOAT`.
- **Not handling NULL** — `NULL != NULL`. Use `IS NULL` / `IS NOT NULL`. Use `COALESCE` for defaults.

## Verification

- ✅ Query produces correct results for edge cases (empty sets, NULLs, duplicates)
- ✅ `EXPLAIN ANALYZE` shows reasonable performance
- ✅ No SQL injection vectors (all inputs parameterized)
- ✅ Pagination is stable under concurrent writes