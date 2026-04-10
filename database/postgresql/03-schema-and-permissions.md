# Schema and Permissions (PostgreSQL)

## Overview

This document explains how to manage schemas, control access, and assign permissions in PostgreSQL.

> ⚠️ Proper schema and permission design is critical for security, scalability, and maintainability.

## Why This Matters

- Prevents unauthorized data access
- Avoids accidental table conflicts
- Enables multi-service or multi-tenant architectures
- Ensures clean separation of concerns

---

## Key Concepts

| Concept       | Description                          | Why it matters                                                      |
| ------------- | ------------------------------------ | ------------------------------------------------------------------- |
| Schema        | Namespace inside a database          | Prevents naming conflicts (e.g., same table name in different apps) |
| public schema | Default schema                       | Often overused → security risk                                      |
| search_path   | Schema resolution order              | Controls how queries find tables                                    |
| Role/User     | Access identity                      | Enables fine-grained access control                                 |
| Privileges    | Access rights (SELECT, INSERT, etc.) | Protects data from misuse                                           |

## Default Behavior (Important)

PostgreSQL creates a public schema by default
All users can access and create objects in public

#### Why this is risky:

- Any user can create or modify objects
- Leads to messy structure in multi-app environments
- Harder to enforce security boundaries

---

### Step 1: Create a Custom Schema

```sql
CREATE SCHEMA my_app;
```

#### Why:

- Isolates application data logically
- Avoids pollution of `public` schema
- Enables better access control

### Step 2: Assign Schema Ownership

```sql
ALTER SCHEMA my_app OWNER TO my_app_user;
```

#### Why:

- Owner can create/modify objects inside schema
- Avoids dependency on admin user
- Keeps ownership aligned with application user

### Step 3: Restrict Public Access (Critical)

```sql
REVOKE ALL ON SCHEMA public FROM PUBLIC;
```

#### Why:

- Removes default open access
- Prevents unauthorized table creation
- Enforces explicit permission model

> 💡 `PUBLIC` means all users, not just a role

### Step 4: Grant Schema Usage

```sql
GRANT USAGE ON SCHEMA my_app TO my_app_user;
```

#### Why:

- Without `USAGE`, user cannot access schema objects
- Acts like “visibility permission”

### Step 5: Allow Table Creation

```sql
GRANT CREATE ON SCHEMA my_app TO my_app_user;
```

#### Why:

- Enables application to create tables
- Required for migrations and schema evolution

### Step 6: Set search_path

```sql
ALTER ROLE my_app_user SET search_path TO my_app;
```

#### Why:

- Allows queries like `SELECT * FROM users` instead of `my_app.users`
- Prevents accidental usage of wrong schema
- Improves developer experience

### Step 7: Default Privileges (Very Important)

```sql
ALTER DEFAULT PRIVILEGES IN SCHEMA my_app
GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO my_app_user;
```

#### Why:

- Ensures future tables automatically get correct permissions
- Prevents “it works locally but fails in prod” issues
- Critical for teams and automated migrations

### Table-Level Permissions

```sql
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA my_app TO my_app_user;
```

#### Why:

- Applies permissions to existing tables
- Complements default privileges (which apply only to future tables)

### Sequence Permissions (Often Missed)

```sql
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA my_app TO my_app_user;
```

#### Why:

- Required for auto-increment (`SERIAL`, `BIGSERIAL`)
- Without this, inserts may fail

---

## Example: Full Setup (Production Ready)

```sql
-- Create schema
CREATE SCHEMA my_app;

-- Assign ownership
ALTER SCHEMA my_app OWNER TO my_app_user;

-- Secure public schema
REVOKE ALL ON SCHEMA public FROM PUBLIC;

-- Grant schema access
GRANT USAGE, CREATE ON SCHEMA my_app TO my_app_user;

-- Set search path
ALTER ROLE my_app_user SET search_path TO my_app;

-- Existing tables
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA my_app TO my_app_user;

-- Sequences
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA my_app TO my_app_user;

-- Future tables
ALTER DEFAULT PRIVILEGES IN SCHEMA my_app
GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO my_app_user;
```

---

## Common Mistakes

| Issue                           | Why it happens                | Fix                                 |
| ------------------------------- | ----------------------------- | ----------------------------------- |
| Tables accessible to all users  | Using default `public` schema | Restrict public + use custom schema |
| Permission denied on new tables | Default privileges not set    | Use `ALTER DEFAULT PRIVILEGES`      |
| Insert fails with SERIAL        | Sequence permission missing   | Grant sequence access               |
| Wrong table used                | search_path not set           | Configure `search_path`             |

---

## Best Practices

- Use one schema per application → avoids naming conflicts and improves clarity
- Avoid using `public` schema → reduces accidental exposure
- Always configure default privileges → prevents future permission bugs
- Use least privilege principle → grant only what is needed
- Separate roles (advanced) → improves security in production
  - `app_user` → runtime queries
  - `migration_user` → schema changes

### Notes

- Managed DBs (AWS RDS, GCP Cloud SQL) support schemas but may restrict some admin actions
- Always test permissions with the actual application user
- Schema design becomes critical in microservices or multi-tenant systems
