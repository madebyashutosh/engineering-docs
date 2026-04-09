# Prerequisites for PostgreSQL

## Overview

This document outlines the **essential requirements** before setting up a PostgreSQL database and user.

> ⚠️ Understanding these prerequisites ensures a smooth setup, reduces errors, and improves security.

---

## Why Prerequisites Matter

- PostgreSQL must be **installed and accessible** before creating databases or users.
- Admin access is required to perform setup tasks safely.
- Network and system checks prevent connectivity and permission issues.
- This document applies to **local installations** and **managed services** like AWS RDS, GCP Cloud SQL, or Azure Database for PostgreSQL.

---

## Required Tools

| Tool                                | Purpose                     | Recommended Version / Notes |
| ----------------------------------- | --------------------------- | --------------------------- |
| PostgreSQL                          | Database engine             | 13+                         |
| `psql`                              | CLI for database management | Installed with PostgreSQL   |
| pgAdmin (optional)                  | GUI client                  | Version 6+                  |
| DBeaver / TablePlus / any DB client | Optional GUI                | Latest stable               |

**Why:** CLI tools like `psql` allow reproducible scripts and easier automation, while GUI clients are optional for convenience.

---

## Access Requirements

Ensure you have the following:

- Database host (localhost or remote endpoint)
- Port (default: 5432)
- Admin username (e.g., `postgres`)
- Admin password

**Why:** Admin credentials are required to create new databases, users, and assign privileges.

---

## Network Requirements (for remote DB)

- Your IP must be whitelisted in the database security group.
- Port 5432 should be accessible.
- Ensure no firewall or VPN blocks the connection.

**Why:** Without network access, CLI tools or applications cannot reach the database, making setup impossible.

---

## System Requirements

- Terminal or command-line access
- Basic knowledge of SQL commands (`CREATE DATABASE`, `CREATE USER`, `GRANT`)
- Familiarity with shell commands for navigation and file execution

**Why:** Basic system skills ensure you can run scripts and troubleshoot issues efficiently.

---

## Verification Checklist

Before proceeding, confirm:

- [ ] PostgreSQL is installed OR remote DB is accessible
- [ ] You have valid admin credentials
- [ ] You can access the database host and port
- [ ] `psql` commands can connect successfully (`psql -h <host> -U <user> -d postgres`)

**Tip**: Run a test query such as `SELECT version();` to confirm connectivity.

---

## Notes

- Managed services (e.g., AWS RDS, GCP Cloud SQL) may **not require installation**, but all other prerequisites still apply.
- Always **handle credentials securely**; never hardcode passwords in scripts.
- Reference documentation:
  - [PostgreSQL Official Documentation ](https://www.postgresql.org/docs/)
  - [psql Command-Line Tool](https://www.postgresql.org/docs/current/app-psql.html)
