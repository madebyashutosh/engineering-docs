# Backup and Restore (PostgreSQL)

## Overview

This document explains how to **backup** and **restore** PostgreSQL databases using built-in tools like:

- `pg_dump` (logical backups)
- `pg_restore`
- `psql` (for SQL dumps)

> ⚠️ Backups are your last line of defense against data loss.

## Why This Matters

- Protects against accidental deletion or bad deployments
- Enables disaster recovery (server crash, corruption)
- Required for migrations and environment cloning
- Helps meet compliance and audit requirements

---

## Types of Backups

| Type            | Tool             | Why it matters                    |
| --------------- | ---------------- | --------------------------------- |
| Logical Backup  | `pg_dump`        | Flexible, portable across systems |
| Physical Backup | File system copy | Faster, but less flexible         |
| Full Backup     | Entire DB        | Complete recovery                 |
| Partial Backup  | Specific tables  | Useful for selective restore      |

---

## Logical Backup

### Full Database Backup

```bash
pg_dump -U my_app_user -d my_app_db -F c -f backup.dump
```

#### Why:

- `-F c` (custom format) → supports compression + selective restore
- Portable across environments
- Best for most applications

### Plain SQL Backup

```bash
pg_dump -U my_app_user -d my_app_db -f backup.sql
```

#### Why:

- Human-readable SQL
- Easy to inspect or modify
- Can be restored using `psql`

### Backup Specific Tables

```bash
pg_dump -U my_app_user -d my_app_db -t users -f users_backup.sql
```

#### Why:

- Useful for partial recovery
- Reduces backup size and time

## Restore Operations

### Restore from Custom Format

```bash
pg_restore -U my_app_user -d my_app_db backup.dump
```

#### Why:

- Supports parallel restore
- Allows selective restore (tables, schema, etc.)

### Restore from SQL File

```bash
psql -U my_app_user -d my_app_db -f backup.sql
```

#### Why:

- Executes SQL directly
- Simple and widely compatible

### Restore to New Database

```bash
createdb -U my_app_user new_db

pg_restore -U my_app_user -d new_db backup.dump
```

#### Why:

- Safe testing before overwriting production
- Useful for staging or debugging

## Backup Remote Databases

```bash
pg_dump -h <host> -U my_app_user -d my_app_db -F c -f backup.dump
```

#### Why:

- Works with managed services (AWS RDS, GCP Cloud SQL)
- Enables off-site backups

## Automating Backups

### Example (Cron Job)

```bash
0 2 * * * pg_dump -U my_app_user -d my_app_db -F c -f /backups/db_$(date +\%F).dump
```

#### Why:

- Ensures regular backups without manual effort
- Reduces risk of forgetting backups

---

## Important Backup Practices

### 1. Store Backups Safely

- Use cloud storage (S3, GCS, etc.)
- Keep backups in a different location than DB

#### Why:

- If server fails, local backups are lost too

### 2. Test Your Backups

```bash
pg_restore -d test_db backup.dump
```

#### Why:

- A backup is useless if it cannot be restored
- Ensures reliability during emergencies

### 3. Use Compression

```bash
pg_dump -F c
```

#### Why:

- Reduces storage cost
- Faster transfer over network

### 4. Retention Policy

- Keep last 7 daily backups
- Keep weekly/monthly snapshots

#### Why:

- Protects against delayed data corruption
- Balances storage vs safety

---

##Common Mistakes
| Issue | Why it happens | Fix |
| ------------------------------- | ------------------ | ----------------------- |
| Backup exists but restore fails | Not tested | Always test restore |
| Overwriting production DB | Restored blindly | Restore to new DB first |
| Missing roles/users | Roles not included | Backup roles separately |
| Backup too large | No compression | Use `-F c` |

---

## Backup Roles (Often Missed)
```bash
pg_dumpall --roles-only > roles.sql
```
#### Why:

- Users and permissions are NOT included in `pg_dump`
- Required for full environment restoration
### Example: Production Backup Strategy
```bash
# Backup DB
pg_dump -U my_app_user -d my_app_db -F c -f backup.dump

# Backup roles
pg_dumpall --roles-only > roles.sql
```
#### Why this setup:

- Covers both data and access control
- Enables full system recovery

## Best Practices
- Automate backups (cron/jobs) → reduces human error
- Store backups off-site → protects against infrastructure failure
- Test restores regularly → ensures reliability
- Use custom format (`-F c`) → flexible and efficient
- Separate backup and production credentials → improves security

### Notes
- Managed services (AWS RDS, GCP Cloud SQL) often provide automated backups
- Still recommended to take **manual backups for critical operations**
- Large-scale systems may use replication + point-in-time recovery (PITR)
