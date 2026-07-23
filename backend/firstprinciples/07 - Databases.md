# LEC 07 - Databases (Backend Essentials)

# Why do we need Databases?

A database stores data **persistently**.

Persistence means:

- Data survives even after the application stops.
- Data is available when the application starts again.

Example:

```text
User

↓

Creates Account

↓

Data stored in Database

↓

Server Restarts

↓

Data still exists
```

Without a database, all data would disappear when the server stops.

---

# What is a DBMS?

**DBMS (Database Management System)** is software that manages databases.

Examples:

- PostgreSQL
- MySQL
- SQLite
- MongoDB

Responsibilities:

- Store data
- CRUD operations
- Data integrity
- Security
- Concurrency handling
- Efficient querying

---

# Why not store data in files?

Problems with text files:

- Slow searching
- No schema
- No relationships
- Difficult concurrent updates
- No indexing
- Hard to scale

A DBMS solves these problems.

---

# SQL vs NoSQL

## SQL (Relational)

Stores data in

```text
Tables

↓

Rows

↓

Columns
```

Characteristics

- Fixed schema
- Strong relationships
- High data integrity
- SQL language

Examples

- PostgreSQL
- MySQL
- SQL Server

---

## NoSQL (Non-Relational)

Stores flexible documents.

Characteristics

- Flexible schema
- Easy to change structure
- Good for rapidly changing data

Example

- MongoDB

---

# SQL vs NoSQL

| SQL | NoSQL |
|------|--------|
| Fixed Schema | Flexible Schema |
| Strong Relationships | Weak/No Relationships |
| High Data Integrity | More Flexible |
| Structured Data | Semi-Structured Data |

---

# Which one should you choose?

General recommendation

```text
Choose PostgreSQL.
```

Reasons

- Industry standard
- Open source
- Reliable
- Scalable
- Excellent SQL support
- Supports JSON (so you rarely need MongoDB)

For most backend projects,

PostgreSQL is enough.

---

# Important PostgreSQL Data Types

## Integer

```text
INTEGER

BIGINT
```

Use for IDs and counts.

---

## Decimal

```text
DECIMAL
```

Use for

- Money
- Prices
- Financial calculations

Never use floating point for money.

---

## Floating Point

```text
REAL

DOUBLE PRECISION
```

Use when tiny precision errors are acceptable.

Example

- Scientific values
- Measurements

---

## Text

```text
TEXT
```

Preferred string type.

Examples

- Name
- Description
- Email

Usually prefer `TEXT` over arbitrary `VARCHAR(255)` unless you truly need a length limit.

---

## Boolean

```text
TRUE

FALSE
```

---

## Date & Time

```text
DATE

TIMESTAMP

TIMESTAMPTZ
```

`TIMESTAMPTZ` (timestamp with timezone) is usually preferred for backend systems.

---

## UUID

Globally unique identifier.

Useful for IDs in distributed systems.

---

## JSONB

Stores JSON efficiently.

Useful when some fields don't have a fixed structure.

Example

```json
{
  "theme":"dark",
  "language":"en"
}
```

---

# Database Schema

A schema defines

- Tables
- Columns
- Data types
- Constraints
- Relationships

Always design the schema before writing queries.

---

# Primary Key

Uniquely identifies a row.

Example

```text
Users

id
```

Every table should have one.

---

# Foreign Key

Creates relationship between tables.

Example

```text
Users

↓

Orders
```

```text
orders.user_id

↓

users.id
```

Used to maintain data integrity.

---

# CRUD Operations

Every resource usually supports

```text
Create

Read

Update

Delete
```

Example

```sql
INSERT

SELECT

UPDATE

DELETE
```

---

# Database Migrations

Never manually edit production databases.

Use **Migrations**.

Migration = Version-controlled SQL changes.

Example

```text
001_create_users.sql

002_create_orders.sql

003_add_status_column.sql
```

Benefits

- Version history
- Easy deployment
- Team collaboration
- Rollback support

---

# Up & Down Migrations

Up

```text
Apply changes
```

Example

```sql
CREATE TABLE users...
```

Down

```text
Undo changes
```

Example

```sql
DROP TABLE users...
```

Useful for rolling back failed deployments.

---

# Good Backend Workflow

```text
Requirements

↓

Database Schema

↓

Migrations

↓

Database

↓

API

↓

Business Logic
```

---

# Interview Takeaways

- Database provides persistent storage.
- DBMS manages storage, queries, security, and concurrency.
- SQL databases use tables and fixed schemas.
- NoSQL databases provide flexible schemas.
- PostgreSQL is a great default choice for most backend projects.
- Use `DECIMAL` for money.
- Prefer `TEXT` for general strings.
- Use `JSONB` only when flexibility is required.
- Every table should have a Primary Key.
- Use Foreign Keys to define relationships.
- Use migrations instead of manually changing production databases.
