# PostgreSQL Installation (Local)

## Overview

This document provides generic steps to install PostgreSQL locally on macOS, Ubuntu/Debian, or Windows, and verify the installation.

> ⚠️ Skip this step if using a **managed database service** (e.g., AWS RDS, GCP Cloud SQL, Azure Database for PostgreSQL).

---

## Why This Matters

- PostgreSQL must be installed and functional before creating databases or users.
- Verifying the installation ensures you can run SQL commands and connect to the database.
- Proper setup prevents common errors related to authentication, PATH, or service status.

## Installation Steps

### macOS (Homebrew)

```bash
brew update
brew install postgresql
```

Start the PostgreSQL service:

```bash
brew services start postgresql
```

Check service status (optional):

```bash
brew services list
```

---

### Ubuntu / Debian

```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
```

Start the PostgreSQL service:

```bash
sudo systemctl start postgresql
sudo systemctl enable postgresql  # optional: start on boot
```

Check service status:

```bash
sudo systemctl status postgresql
```

---

### Windows

1. Download PostgreSQL installer from:
   https://www.postgresql.org/download/windows/

2. Run the installer and follow the setup wizard:
   - Set a strong password for the `postgres` user
   - Keep default port: `5432`
   - Select optional components like pgAdmin

3. Start PostgreSQL service via Services or pgAdmin.

---

## Verify Installation

Check PostgreSQL version:

```bash
psql --version
```

Login as default superuser:

```bash
psql -U postgres
```

If successful, you should see:

```bash
postgres=#
```

Test a simple SQL command:

```SQL
SELECT version();
```

Exit psql:

```SQL
\q
```

---

## Common Issues

| Issue                     | Possible Fix                             |
| ------------------------- | ---------------------------------------- |
| `psql: command not found` | Ensure PostgreSQL is added to PATH       |
| Connection refused        | Verify the PostgreSQL service is running |
| Authentication failed     | Double-check username/password and port  |

---

## Notes & Best Practices

- Default superuser: `postgres`
- Default port: `5432`
- For development, you may create additional users with limited privileges
- GUI clients like **pgAdmin**, **DBeaver**, or **TablePlus** are optional but recommended
- Keep credentials secure; avoid hardcoding passwords in scripts
