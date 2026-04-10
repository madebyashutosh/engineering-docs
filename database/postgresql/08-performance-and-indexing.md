# Performance and Indexing (PostgreSQL)

## Overview

This document explains how to **optimize query performance** in PostgreSQL using:

- Indexes
- Query analysis
- Basic optimization techniques

> ⚠️ Performance issues usually come from poor queries or missing indexes, not PostgreSQL itself.

## Why This Matters

A system may work perfectly with small data, but as data grows:

- Queries become slower
- APIs start timing out
- Database CPU usage increases

Understanding performance early helps avoid costly fixes later.

## How PostgreSQL Executes Queries

When you run a query, PostgreSQL decides how to fetch the data:

- Scan entire table (**sequential scan**)
- Use an index (**index scan**)

Without indexes, PostgreSQL often scans the whole table, which becomes slow as data grows.

## Indexes

### What is an Index?

An index is a **data structure that helps PostgreSQL find rows faster**, similar to an index in a book.

Instead of scanning every row, PostgreSQL can jump directly to the relevant data.

### Create an Index

```sql
CREATE INDEX idx_users_email ON users(email);
```

#### What to do:

Add indexes on columns used in:

- `WHERE`
- `JOIN`
- `ORDER BY`

#### Why:

- Speeds up lookups significantly
- Reduces full table scans

### When Indexes Help (and When They Don’t)

Indexes are powerful, but not free.

They help when:

- Queries filter large datasets
- Columns have high uniqueness (e.g., email)

They don’t help much when:

- Tables are very small
- Column values repeat a lot (low selectivity)

They can hurt when:

- Too many indexes → slower INSERT/UPDATE
- Unused indexes → wasted memory

## Query Analysis (EXPLAIN)

### Analyze a Query

```sql
EXPLAIN ANALYZE SELECT \* FROM users WHERE email = 'test@example.com';
```

This shows how PostgreSQL executes your query.

#### What to Look For

- `Seq Scan` → full table scan (slow for large tables)
- `Index Scan` → using index (fast)
- Execution time

**Why**: You should never guess performance — always measure it.

## Common Optimization Patterns

### 1. Avoid `SELECT *`

```sql
SELECT id, email FROM users;
```

### Why:

- Reduces data transfer
- Improves performance

### 2. Filter Early

```sql
SELECT * FROM users WHERE email = 'test@example.com';
```

#### Why:

- Limits number of rows processed
- Improves query efficiency

### 3. Use Proper Indexes

```sql
CREATE INDEX idx_users_created_at ON users(created_at);
```

#### Why:

- Improves sorting and filtering performance

### 4. Limit Results

```sql
SELECT * FROM users LIMIT 10;
```

#### Why:

- Prevents loading unnecessary data

## Composite Indexes

```sql
CREATE INDEX idx_users_email_status ON users(email, status);
```

Used when queries filter on multiple columns.

#### Why:

- Faster than multiple single-column indexes
- Matches real query patterns

> ⚠️ Order matters: `(email, status)` ≠ `(status, email)`

## Slow Query Example

Without index:

```sql
SELECT * FROM users WHERE email = 'test@example.com';
```

> Full table scan

With index:

```sql
CREATE INDEX idx_users_email ON users(email);
```

> Fast lookup

## Monitoring Performance

### What to do:

- Check running queries
  ```sql
  SELECT * FROM pg_stat_activity;
  ```
- Identify slow queries
- Monitor CPU and memory usage

#### Why:

- Helps detect bottlenecks early
- Prevents production slowdowns

Common Mistakes

| Issue             | Why it happens        | Fix                    |
| ----------------- | --------------------- | ---------------------- |
| Slow queries      | Missing indexes       | Add indexes            |
| Too many indexes  | Over-optimization     | Keep only useful ones  |
| Wrong index order | Misunderstood queries | Match query pattern    |
| Ignoring EXPLAIN  | Guessing performance  | Always analyze queries |

## Best Practices

- Index only what you query frequently → avoids unnecessary overhead
- Use `EXPLAIN ANALYZE` regularly → data-driven optimization
- Avoid over-indexing → balances read vs write performance
- Optimize queries before scaling infra → cheaper and more effective

### Notes

- PostgreSQL supports advanced indexing:
  - B-Tree (default)
  - GIN (for JSONB, full-text search)
  - GiST (geospatial data)
- Use advanced indexes only when needed
