# PostgreSQL Installation

## Overview

This document provides generic steps to install PostgreSQL locally and verify the installation.

> Skip this step if using a managed database service (e.g., AWS RDS).

---

## Installation

### macOS (using Homebrew)

```bash
brew update
brew install postgresql
```

Start PostgreSQL:

```bash
brew services start postgresql
```

---

### Ubuntu / Debian

```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
```

Start service:

```bash
sudo systemctl start postgresql
```

---

### Windows

1. Download PostgreSQL from:
   https://www.postgresql.org/download/windows/

2. Run the installer and follow the setup wizard:
   - Set a strong password for the `postgres` user
   - Keep default port: `5432`

3. Install pgAdmin (optional but recommended)

---

## Verify Installation

Check PostgreSQL version:

```bash
psql --version
```

Login to PostgreSQL:

```bash
psql -U postgres
```

If successful, you should see:

```bash
postgres=#
```

---

## Common Issues

### Command not found (`psql`)

- Ensure PostgreSQL is added to PATH

### Connection refused

- Make sure PostgreSQL service is running

### Authentication failed

- Double-check username/password

---

## Notes

- Default superuser is usually `postgres`
- Default port is `5432`
- Use `\q` to exit psql
