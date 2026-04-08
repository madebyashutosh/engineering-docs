# Engineering SOPs & Playbooks

## Overview

This repository contains **standard operating procedures (SOPs)** for common engineering tasks.

It is intended to grow into a centralized knowledge base for:

- Databases
- Infrastructure
- Development workflows

---

## Current Structure

```text
database/
  postgresql/
    00-prerequisites.md
    01-installation.md
    02-create-database-and-user.md
    03-verify-database-and-user.md
```

---

## Philosophy

- Keep SOPs **simple, clear, and reproducible**
- Prefer **terminal-first workflows**
- Use **generic, reusable patterns** (avoid environment-specific assumptions)

---

## Usage

1. Navigate to the relevant folder (e.g., `database/postgresql`)
2. Follow steps in order (based on numbering)
3. Replace placeholders with actual values

---

## Naming Conventions

### Files

- Use clear, action-based names:
  - `prerequisites.md`
  - `installation.md`
  - `create-database-and-user.md`
  - `verify-database-and-user.md`

### Placeholders

Use generic placeholders:

```text
<database_name>
<database_user>
<rds-endpoint>
```

---

## Notes

- Do not commit real credentials or endpoints
- Keep SOPs environment-agnostic where possible

---

## Future Scope

This repository will expand to include:

- More databases (MySQL, MongoDB, etc.)
- Cloud-specific setups (AWS, GCP, Azure)
- Deployment and DevOps workflows
- Monitoring, backup, and recovery SOPs
