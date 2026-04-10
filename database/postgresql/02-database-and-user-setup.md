# Database and User Setup (PostgreSQL)

## Overview

This document covers how to create a database, create a user (role), and assign ownership and privileges in PostgreSQL.

> ⚠️ These steps should be performed using an admin/superuser account (e.g., postgres).

## Why This Matters

- Separating databases and users improves security and maintainability
- Applications should never use superuser accounts
- Proper ownership ensures controlled access and avoids permission issues
- Enables safe multi-environment setups (dev, staging, prod)

## Key Concepts

| Concept     | Description                     | Why it matters                            |
| ----------- | ------------------------------- | ----------------------------------------- |
| Database    | Logical container for data      | Keeps application data isolated           |
| User / Role | Entity that can login           | Enables authentication and access control |
| Ownership   | Controls DB objects             | Defines who can modify/delete resources   |
| Privileges  | Permissions like SELECT, INSERT | Prevents unauthorized access              |

> 💡 In PostgreSQL, users are roles with LOGIN privilege

### Step 1: Connect as Admin

```bash
psql -U postgres
```

Or for remote:

```bash
psql -h <host> -U <admin_user> -d postgres
```

#### Why:

- Only admin users can create databases and roles
- Ensures setup is done with full privileges
- Prevents partial/failed configurations due to permission issues

### Step 2: Create a New Database

```sql
CREATE DATABASE my_app_db;
```

#### Why:

- Each application should have its own database
- Prevents accidental data mixing between services
- Makes backup, restore, and scaling easier

### Step 3: Create a New User

```sql
CREATE USER my_app_user WITH PASSWORD 'strong_password';
```

#### Why:

- Applications should not use postgres (superuser)
- Limits damage if credentials are leaked
- Enables role-based access control

> ⚠️ Always use strong passwords and store them securely (env variables / secret manager)

### Step 4: Assign Ownership

```sql
ALTER DATABASE my_app_db OWNER TO my_app_user;
```

#### Why:

- Owner has full control over the database
- Prevents dependency on admin user for schema changes
- Allows application user to manage its own objects

### Step 5: Grant Privileges

```sql
GRANT ALL PRIVILEGES ON DATABASE my_app_db TO my_app_user;
```

#### Why:

- Ownership alone is not always sufficient for all operations
- Explicit grants avoid hidden permission issues
- Makes access rules clear and predictable

#### Step 6: Verify Setup

```bash
psql -U my_app_user -d my_app_db
```

```sql
SELECT current_user, current_database();
```

#### Why:

- Confirms authentication works
- Ensures correct database-user mapping
- Helps catch issues early (wrong password, missing privileges)

### Alternative: One-Liner Setup

```sql
CREATE DATABASE my_app_db OWNER my_app_user;
```

#### Why:

- Faster setup for scripts and automation
- Reduces manual steps
- Useful in CI/CD pipelines

> ⚠️ User must exist before running this

## Common Mistakes

| Issue                 | Why it happens          | Fix                      |
| --------------------- | ----------------------- | ------------------------ |
| Permission denied     | Privileges not granted  | Run `GRANT`              |
| Role does not exist   | User not created first  | Create user before DB    |
| Cannot login          | Missing LOGIN privilege | Use `CREATE USER`        |
| Using postgres in app | Convenience             | Security risk — avoid it |

## Best Practices

- Use **one user per application**
  - isolates access and limits blast radius
- Never use **postgres** in apps
  - superuser can drop/modify everything
- Use environment variables for credentials
  - avoids leaking secrets in code
- Naming conventions:
  - DB: `app_env_db` → easier environment identification
  - User: `app_env_user` → consistent access mapping

## Example (Production Style)

```sql
CREATE USER myapp_prod_user WITH PASSWORD 'secure_password';

CREATE DATABASE myapp_prod_db;

ALTER DATABASE myapp_prod_db OWNER TO myapp_prod_user;

GRANT ALL PRIVILEGES ON DATABASE myapp_prod_db TO myapp_prod_user;
```

#### Why this structure:

- Clear separation of concerns
- Follows least privilege principle
- Scales well across environments

### Notes

- Managed services (AWS RDS, GCP Cloud SQL) may restrict superuser access
- Some commands may require provider-specific roles
- Always follow least privilege principle
