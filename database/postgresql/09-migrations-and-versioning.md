# Migrations and Versioning (PostgreSQL)

## Overview

This document explains how to **manage database schema changes over time** using migrations and versioning.

> ⚠️ Directly modifying the database manually in production is risky. Migrations make changes **trackable**, **repeatable**, and **safe**.

## Why This Matters

In real projects, your database structure will evolve:

- New tables
- New columns
- Changes in relationships

Without a proper system:

- Different environments (dev/staging/prod) go out of sync
- Deployments break
- Data can be lost

Migrations solve this by **treating schema changes like code**.

## What is a Migration?

A migration is a **version-controlled script** that modifies the database schema.

Each migration represents a small, incremental change, such as:

- Creating a table
- Adding a column
- Updating constraints

Instead of changing the database directly, you **apply migrations in order**.

### How Migrations Work

A typical flow looks like:

- Create a migration file
- Write SQL changes
- Apply migration
- Record that it has been applied

PostgreSQL (or your migration tool) keeps track of which migrations have already run.

**Why**: This ensures the same changes are applied consistently across all environments.

### Example Migration

```sql
-- 001_create_users_table.sql

CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email TEXT UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### Why this approach:

- Clear, versioned change
- Easy to review and track
- Can be applied anywhere

### Migration Naming Convention

#### What to do:

- Use ordered prefixes:
  - `001_...`
  - `002_...`
- Add descriptive names:
  - `create_users_table`
  - `add_index_to_users_email`

#### Why:

- Ensures execution order
- Makes history readable

### Applying Migrations

#### Using `psql`

```bash
psql -U my_app_user -d my_app_db -f 001_create_users_table.sql
```

### Using Migration Tools (Recommended)

Common tools:

- Prisma
- Knex
- TypeORM
- Flyway
- Liquibase

#### Why use tools:

- Automatically track applied migrations
- Provide rollback support
- Integrate with CI/CD

## Rollbacks (Important)

A rollback reverses a migration.

Example:

```sql
DROP TABLE users;
```

#### Why:

- Allows recovery from bad deployments
- Essential for safe production releases

> ⚠️ Not all changes are easily reversible (e.g., data deletion)

## Safe Migration Practices

### What to do:

- Make small, incremental changes
- Avoid large destructive changes
- Test migrations in staging first
- Backup database before major changes

#### Why:

- Reduces risk of failure
- Easier to debug issues

## Schema Version Tracking

Most tools create a table like:

```sql
migrations (
  id,
  name,
  executed_at
);
```

#### Why:

- Tracks which migrations are applied
- Prevents duplicate execution

## Common Migration Issues

| Issue                | Why it happens       | Fix                 |
| -------------------- | -------------------- | ------------------- |
| Migration runs twice | Not tracked properly | Use migration tool  |
| Prod != Dev schema   | Manual changes       | Use migrations only |
| Deployment fails     | Breaking changes     | Test in staging     |
| Data loss            | Unsafe ALTER/DROP    | Backup first        |

## Advanced Concepts

### Idempotent Migrations

Write migrations that don’t fail if run multiple times:

```sql
CREATE TABLE IF NOT EXISTS users (...);
```

#### Why:

- Safer in distributed systems
- Prevents accidental failures

### Zero-Downtime Migrations (Intro)

For production systems:

- Avoid locking tables for long time
- Add columns without breaking existing queries

#### Why:

- Keeps application running during deployments

## Best Practices

- Treat migrations as code (version control them) → ensures consistency
- Never edit old migrations → preserves history
- Use separate migration and app users (advanced) → improves security
- Run migrations via CI/CD → avoids manual errors

### Example Workflow

```bash
# 1. Create migration
002_add_user_name.sql

# 2. Apply migration
psql -U my_app_user -d my_app_db -f 002_add_user_name.sql
```

### Notes

- Migrations are essential for:
  - Teams
  - Production systems
  - Long-term projects
- Even solo projects benefit from structured migrations
