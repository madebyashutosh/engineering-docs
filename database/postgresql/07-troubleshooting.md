# Troubleshooting (PostgreSQL)

## Overview

This document covers **common PostgreSQL issues**, how to identify them, and how to fix them effectively.

> ⚠️ Most database issues are not random — they usually come from **configuration**, **permissions**, or **connection limits**.

## Why This Matters

When something breaks in production, the database is often the first suspect.<br />
Knowing how to quickly diagnose issues helps reduce downtime and prevents guesswork.

---

## How to Approach Debugging

Before jumping into fixes, always follow a simple order:

- Check **error message**
- Verify **connection**
- Check **permissions**
- Inspect **logs**

**Why**: Most issues become obvious if you follow this sequence instead of trying random fixes.

---

## Common Issues and Fixes

### 1. Connection Refused

#### Error Example:

```text
could not connect to server: Connection refused
```

#### Why it happens:

The database is not running, or the port is not accessible.

#### What to do:

Check if PostgreSQL service is running

```bash
sudo systemctl status postgresql
```

Start the service if needed

```bash
sudo systemctl start postgresql
```

Verify port `5432` is open

### 2. Authentication Failed

#### Error Example:

```text
password authentication failed for user
```

#### Why it happens:

Credentials are incorrect or authentication rules are misconfigured.

#### What to do:

- Double-check username and password
- Verify `pg_hba.conf` configuration
- Ensure correct authentication method (`md5`, `scram-sha-256`)

### 3. Database Does Not Exist

#### Error Example:

```text
database "my_app_db" does not exist
```

#### Why it happens:

Wrong database name or database not created.

#### What to do:

- List databases

  ```bash
  \l
  ```

- Create database if missing

  ```sql
  CREATE DATABASE my_app_db;
  ```

### 4. Permission Denied

#### Error Example:

```text
permission denied for table users
```

#### Why it happens:

User does not have required privileges.

#### What to do:

- Grant required permissions

  ```sql
  GRANT SELECT, INSERT, UPDATE, DELETE ON TABLE users TO my_app_user;
  ```

- Check schema permissions as well

### 5. Too Many Connections

#### Error Example:

```text
remaining connection slots are reserved
```

#### Why it happens:

Application is opening too many connections (no pooling).

#### What to do:

- Enable connection pooling
- Reduce max connections in app
- Check active connections

```sql
SELECT * FROM pg_stat_activity;
```

### 6. Slow Queries

#### Symptoms:

- API responses are slow
- High CPU usage on DB

#### Why it happens:

Missing indexes or inefficient queries.

#### What to do:

- Analyze query

  ```sql
  EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'test@example.com';
  ```

- Add indexes if needed

  ```sql
  CREATE INDEX idx_users_email ON users(email);
  ```

### 7. psql Command Not Found

#### Error Example:

```text
psql: command not found
```

#### Why it happens:

PostgreSQL binaries are not in PATH.

#### What to do:

- Add PostgreSQL to PATH
- Verify installation

  ```bash
  psql --version
  ```

---

## Logs (Where Real Debugging Happens)

### Common Log Locations

- Ubuntu/Debian:

  ```text
  /var/log/postgresql/
  ```

- macOS (Homebrew):

  ```text
  /usr/local/var/log/postgres.log
  ```

**Why**: Logs provide the exact reason for failures — always check them for unclear errors.

---

## Quick Debug Checklist

When something breaks:

- [ ] Is PostgreSQL running?
- [ ] Can I connect via psql?
- [ ] Are credentials correct?
- [ ] Does the database exist?
- [ ] Are permissions properly set?
- [ ] Are there too many connections?

## Best Practices (for Fewer Issues)

- Use connection pooling
- Avoid using superuser in apps
- Monitor logs regularly
- Keep backups before major changes
- Test changes in staging first

**Why**: Most production issues are preventable with basic discipline.

---

### Notes

- Managed services (AWS RDS, GCP Cloud SQL) provide logs via dashboards
- Tools like `pg_stat_activity` and `EXPLAIN ANALYZE` are essential for debugging
- Always fix root cause, not just symptoms
