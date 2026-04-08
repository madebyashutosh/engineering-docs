# Create Database and User (PostgreSQL RDS - Terminal Only)

## Overview

This step covers:

* Connecting to RDS via terminal
* Creating a database
* Creating a dedicated user
* Assigning ownership and privileges

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

* `myapp_dev`, `myapp_dev_user`
* `myapp_staging`, `myapp_staging_user`
* `myapp_prod`, `myapp_prod_user`

---

## Notes

* Replace placeholders with actual values
* Ensure your IP is allowed in RDS security group
* Install `psql` if not available:

  * Mac: `brew install postgresql`
  * Ubuntu: `sudo apt install postgresql-client`
