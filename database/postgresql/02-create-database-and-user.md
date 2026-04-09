# PostgreSQL RDS: Database & User Setup Guide

## Overview & Why

This SOP guides you through creating a **dedicated PostgreSQL database and user** on an RDS instance using the terminal.

Using the **master/admin** user for daily application operations is risky: it has unrestricted access, and any accidental command could affect all databases on the instance.

##### Instead, this SOP ensures:

* **Isolation:** Each application gets its own database.
* **Security:** Dedicated users have only the permissions they need.
* **Ownership clarity:** Users can manage their database objects without touching the master.
* **Environment management:** Separate users and databases for dev, staging, and production environments.

By following this process, you reduce risk, improve maintainability, and enable safer day-to-day operations.

##### The SOP covers:

1. Connecting to RDS using the master/admin role
2. Creating a new database
3. Creating a dedicated user
4. Assigning privileges and ownership
5. Connecting to the database and setting schema permissions

> ⚠️ The master/admin user is only used for setup and administration. All regular operations should be done with the dedicated user.

---

## 0. Connect to RDS (Using Master/Admin Role)

### Command:

```bash
psql -h <rds-endpoint> -U <master_username> -d postgres -p 5432
```

> Enter the master password when prompted

> ⚠️ All commands below must be run using the master/admin user.

---

## 1. Create Database

```sql
CREATE DATABASE <database_name>;
```

---

## 2. Create User

```sql
CREATE USER <database_user> WITH PASSWORD 'your_password';
```

---

## 3. Grant Database Privileges

```sql
GRANT ALL PRIVILEGES ON DATABASE <database_name> TO <database_user>;
```

---

## 4. Change Database Owner

```sql
ALTER DATABASE <database_name> OWNER TO <database_user>;
```

---

## 5. Connect to Database

```sql
\c <database_name>
```

---

## 6. Grant Schema Permissions

```sql
GRANT ALL ON SCHEMA public TO <database_user>;
```

---

## 7. Exit

```sql
\q
```

---

## Naming Convention (Recommended)

Use environment-based naming:

```text
<project_name>_<environment>
<project_name>_<environment>_user
```

### Examples:

- `myapp_dev`, `myapp_dev_user`
- `myapp_staging`, `myapp_staging_user`
- `myapp_prod`, `myapp_prod_user`

---

## Notes

- Replace placeholders with actual values
- Ensure your IP is allowed in RDS security group
- Install `psql` if not available:
  - Mac: `brew install postgresql`
  - Ubuntu: `sudo apt install postgresql-client`
