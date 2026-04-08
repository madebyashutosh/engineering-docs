# Verify Database and User (PostgreSQL RDS - Terminal Only)

## Overview

This step ensures:

- The user can connect to the database
- Required permissions are correctly assigned

---

## 1. Connect Using Created User

```bash
psql -h <rds-endpoint> -U <database_user> -d <database_name> -p 5432
```

> Enter the user password when prompted

---

## 2. Verify Connection

```sql
SELECT current_user;
```

Expected output:

```text
<database_user>
```

---

## 3. Check Tables Access

```sql
\dt
```

> Should not throw permission errors

---

## 4. Create Test Table

```sql
CREATE TABLE test_table (
  id SERIAL PRIMARY KEY,
  name TEXT
);
```

---

## 5. Insert Test Data

```sql
INSERT INTO test_table (name) VALUES ('test');
```

---

## 6. Fetch Data

```sql
SELECT * FROM test_table;
```

> Confirms read + write access

---

## 7. Cleanup (Optional)

```sql
DROP TABLE test_table;
```

---

## 8. Exit

```sql
\q
```

---

## Common Issues

### Connection refused

- Check RDS security group (allow your IP)
- Ensure correct endpoint and port

---

### Authentication failed

- Verify username/password
- Ensure correct database name

---

### Permission denied

Run this using master/admin user:

```sql
GRANT ALL ON SCHEMA public TO <database_user>;
```

---

## Notes

- Always verify using the **created user**, not master
- Ensure `psql` is installed locally
- Use environment-based naming (e.g., `myapp_dev`, `myapp_prod`)
