# Create Database and User (PostgreSQL RDS - Terminal Only)

## Overview

This step covers:

* Connecting to RDS via terminal
* Creating a development database
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
CREATE DATABASE <project_name>_dev;
```

---

## 2. Create User

```sql
CREATE USER <project_name>_dev_user WITH PASSWORD 'your_password';
```

---

## 3. Grant Database Privileges

```sql
GRANT ALL PRIVILEGES ON DATABASE <project_name>_dev TO <project_name>_dev_user;
```

---

## 4. Change Database Owner

```sql
ALTER DATABASE <project_name>_dev OWNER TO <project_name>_dev_user;
```

---

## 5. Connect to New Database

```sql
\c <project_name>_dev
```

---

## 6. Grant Schema Permissions

```sql
GRANT ALL ON SCHEMA public TO <project_name>_dev_user;
```

---

## 7. Exit

```sql
\q
```

---

## Notes

* Replace `<project_name>` with your actual project name
* Ensure your IP is allowed in RDS security group
* Install `psql` if not available:

  * Mac: `brew install postgresql`
  * Ubuntu: `sudo apt install postgresql-client`

