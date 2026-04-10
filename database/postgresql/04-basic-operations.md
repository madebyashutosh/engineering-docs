# Basic Operations (PostgreSQL)

## Overview

This document covers **core database operations** in PostgreSQL, including:

- Creating tables
- Performing CRUD operations (Create, Read, Update, Delete)
- Using indexes for performance

> ⚠️ These are the **fundamental building blocks** of any backend system.

## Why This Matters

- Every application relies on storing and retrieving data efficiently
- Poor table design leads to performance issues and bugs
- Understanding CRUD helps avoid data inconsistency and corruption

---

## Table Design Basics

### Create a Table

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  name TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### Why:

- `PRIMARY KEY` → uniquely identifies each row
- `UNIQUE` → prevents duplicate emails
- `NOT NULL` → enforces required fields
- `DEFAULT` → avoids manual value assignment

---

## Data Types (Common)

| Type        | Example           | Why it matters                    |
| ----------- | ----------------- | --------------------------------- |
| `SERIAL`    | Auto-increment ID | Simplifies primary key generation |
| `TEXT`      | Name, email       | Flexible string storage           |
| `INTEGER`   | Age, counts       | Efficient numeric storage         |
| `BOOLEAN`   | true/false        | Clear logical flags               |
| `TIMESTAMP` | created_at        | Tracks time-based events          |

---

## CREATE (Insert Data)

```sql
INSERT INTO users (email, name)
VALUES ('john@example.com', 'John Doe');
```

#### Why:

- Explicit column selection prevents schema mismatch issues
- Safer during schema changes

## READ (Query Data)

### Basic Query

```sql
SELECT * FROM users;
```

#### Why:

- Retrieves all rows (use carefully in production)

### Filtered Query

```sql
SELECT * FROM users WHERE email = 'john@example.com';
```

#### Why:

- Reduces unnecessary data transfer
- Improves performance

### Select Specific Columns

```sql
SELECT id, email FROM users;
```

#### Why:

- Avoids over-fetching data
- Reduces memory and network usage

## UPDATE (Modify Data)

```sql
UPDATE users
SET name = 'John Updated'
WHERE email = 'john@example.com';
```

#### Why:

- `WHERE` clause prevents updating all rows accidentally
- Critical for data safety

> ⚠️ Missing `WHERE` = updates entire table (common production bug)

## DELETE (Remove Data)

```sql
DELETE FROM users
WHERE email = 'john@example.com';
```

#### Why:

- Removes unwanted data
- Must always be used carefully with WHERE

## Indexes (Performance Boost)

### Create Index

```sql
CREATE INDEX idx_users_email ON users(email);
```

#### Why:

- Speeds up search queries
- Essential for large datasets

### When to Use Indexes

- Columns used in `WHERE`
- Columns used in `JOIN`
- Frequently searched fields (e.g., email, username)

#### Why:

- Without indexes → full table scan (slow)
- With indexes → fast lookups (logarithmic time)

### When NOT to Use Indexes

- Small tables
- Columns with frequent writes

##### Why:

- Indexes slow down INSERT/UPDATE
- Adds storage overhead

## Constraints (Data Integrity)

### Unique Constraint

```sql
ALTER TABLE users ADD CONSTRAINT unique_email UNIQUE (email);
```

#### Why:

- Prevents duplicate data
- Ensures business rules at DB level

### Foreign Key

```sql
CREATE TABLE orders (
id SERIAL PRIMARY KEY,
user_id INTEGER REFERENCES users(id)
);
```

#### Why:

- Maintains relationship integrity
- Prevents orphan records

---

## Example Workflow

```sql
-- Create table
CREATE TABLE users (
id SERIAL PRIMARY KEY,
email TEXT UNIQUE NOT NULL,
name TEXT
);

-- Insert
INSERT INTO users (email, name) VALUES ('a@test.com', 'A');

-- Read
SELECT * FROM users WHERE email = 'a@test.com';

-- Update
UPDATE users SET name = 'Updated A' WHERE email = 'a@test.com';

-- Delete
DELETE FROM users WHERE email = 'a@test.com';
```

---

## Common Mistakes

| Issue                    | Why it happens  | Fix                            |
| ------------------------ | --------------- | ------------------------------ |
| Full table update/delete | Missing `WHERE` | Always double-check conditions |
| Slow queries             | No indexes      | Add indexes on filter columns  |
| Duplicate data           | No constraints  | Use `UNIQUE`                   |
| Orphan records           | No foreign keys | Use `REFERENCES`               |

---

## Best Practices

- Always use `WHERE` in UPDATE/DELETE → prevents accidental data loss
- Avoid `SELECT *` in production → improves performance and clarity
- Add indexes strategically → balance read vs write performance
- Use constraints for validation → database should enforce rules, not just code
- Keep tables normalized (basic level) → reduces redundancy and inconsistency

### Notes

- PostgreSQL supports advanced features like:
  - JSON (`JSONB`)
  - Full-text search
  - Window functions
- These are powerful but should be used when needed
