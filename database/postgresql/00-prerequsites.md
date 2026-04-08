# Prerequisites for PostgreSQL

## Overview

This document outlines the basic requirements before setting up a PostgreSQL database and user.

---

## Required Tools

- PostgreSQL (recommended version: 13+)
- `psql` (PostgreSQL CLI)
- Optional:
  - pgAdmin (GUI client)
  - DBeaver / TablePlus / any DB client

---

## Access Requirements

Ensure you have the following:

- Database host (localhost or remote endpoint)
- Port (default: 5432)
- Admin username (e.g., postgres)
- Admin password

---

## Network Requirements (for remote DB / RDS)

- Your IP should be whitelisted in the database security group
- Port 5432 should be accessible
- Ensure no firewall/VPN is blocking the connection

---

## System Requirements

- Terminal / command-line access
- Basic knowledge of SQL and shell commands

---

## Verification Checklist

Before proceeding, confirm:

- [ ] PostgreSQL is installed OR remote DB is accessible
- [ ] You have valid admin credentials
- [ ] You can access the database host and port

---

## Notes

- If using managed services (e.g., AWS RDS), installation steps may not be required.
- Ensure credentials are handled securely and not hardcoded in scripts.
