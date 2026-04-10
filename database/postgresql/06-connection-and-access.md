# Connection and Access (PostgreSQL)

## Overview

This document explains how to **connect to PostgreSQL databases** in different environments:

- Local connections
- Remote connections
- Application-level connection strings
- Connection pooling and security

> ⚠️ Incorrect connection handling can lead to **downtime**, **performance issues**, or **security risks**.

## Why This Matters

- Applications must connect reliably to the database
- Poor connection handling can **crash your backend under load**
- Secure connections prevent **data leaks and unauthorized access**

---

## Connection Basics

### Using `psql` (CLI)

```bash
psql -h <host> -U <user> -d <database> -p 5432
```

#### Why:

- Useful for debugging and manual operations
- Confirms connectivity before integrating with applications

### Connection String (URI Format)

```bash
postgresql://username:password@host:5432/database
```

Example:

```bash
postgresql://my_app_user:password@localhost:5432/my_app_db
```

#### Why:

- Standard format used by most frameworks (Node.js, Python, etc.)
- Easy to pass via environment variables

### Environment Variables (Recommended)

```bash
DATABASE_URL=postgresql://my_app_user:password@localhost:5432/my_app_db
```

#### Why:

- Keeps credentials out of source code
- Easier to manage across environments (dev, staging, prod)

## Local vs Remote Connection

### Local

```bash
psql -U my_app_user -d my_app_db
```

#### Why:

- Faster, no network latency
- Ideal for development

### Remote

```bash
psql -h <remote_host> -U my_app_user -d my_app_db
```

#### Why:

- Required for production and managed DBs
- Needs proper network and security setup

## Enabling Remote Access (Self-Hosted)

### 1. Update `postgresql.conf`

```conf
listen_addresses = '*'
```

### 2. Update `pg_hba.conf`

```conf
host all all 0.0.0.0/0 md5
```

### 3. Restart PostgreSQL

```bash
sudo systemctl restart postgresql
```

#### Why:

- Allows external connections
- Without this → only local connections work

> ⚠️ Use IP restrictions instead of `0.0.0.0/0` in production

## SSL Connections (Production)

```bash
postgresql://user:password@host:5432/db?sslmode=require
```

#### Why:

- Encrypts data in transit
- Prevents man-in-the-middle attacks
- Required by most cloud providers

## Connection Pooling (Critical for Apps)

### What is Connection Pooling?

Instead of opening a new database connection for every request, connection pooling maintains a small set of reusable connections. Each request borrows a connection from the pool and returns it after use.

**Why**: Creating connections is expensive. Reusing them reduces overhead and improves performance.

### Why Pooling is Important

PostgreSQL supports a limited number of connections (around ~100 by default), and each one consumes resources. If every request creates its own connection, the system can quickly hit this limit, leading to slowdowns or failures.

Connection pooling avoids this by **reusing a fixed number of connections**, keeping the database stable even under load.

### Mental Model

Without pooling, every request opens its own connection.<br />
With pooling, requests share a controlled set of connections.

## Example (Node.js - pg)

```javascript
import pkg from "pg";

const { Pool } = pkg;

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  max: 10,
});
```

#### Why:

- Limits number of active connections
- Prevents overload under high traffic

## Common Connection Issues

| Issue                 | Why it happens                | Fix                              |
| --------------------- | ----------------------------- | -------------------------------- |
| Connection refused    | DB not running / port blocked | Start service / check firewall   |
| Too many connections  | No pooling                    | Use connection pool              |
| Authentication failed | Wrong credentials             | Verify username/password         |
| Timeout               | Network issue                 | Check host, port, security group |

## Best Practices

- Use environment variables for connection strings → prevents credential leaks
- Always enable SSL in production → secures data in transit
- Use connection pooling → avoids DB overload
- Restrict IP access (whitelisting) → improves security
- Monitor connection count → prevents performance bottlenecks

## Example: Production Connection Setup

```bash
DATABASE_URL=postgresql://my_app_user:secure_password@prod-host:5432/my_app_db?sslmode=require
```

#### Why this works well:

- Secure (SSL enabled)
- Configurable via env
- Compatible with most frameworks

### Notes

- Managed DBs (AWS RDS, GCP Cloud SQL):
  - Require SSL
  - Use security groups / authorized networks
- Tools like:
  - PgBouncer → lightweight connection pooling
  - Pgpool → advanced pooling + load balancing
