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

# LEC 07 - Databases (Part 2)

# Database Constraints

Constraints ensure only valid data is stored.

Common constraints

## PRIMARY KEY

- Unique
- Cannot be NULL
- Identifies each row

Example

```text
users.id
```

---

## FOREIGN KEY

Creates relationships between tables.

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

Prevents invalid references.

---

## NOT NULL

Field must always contain a value.

Example

```text
email
```

User must provide it.

---

## UNIQUE

No duplicate values.

Example

```text
email
username
```

---

## DEFAULT

Automatically assigns a value.

Example

```sql
status = 'active'
```

---

# Relationships

## One-to-One (1:1)

Example

```text
User

↓

Profile
```

Each user has one profile.

---

## One-to-Many (1:N)

Most common relationship.

Example

```text
User

↓

Many Orders
```

---

## Many-to-Many (M:N)

Example

```text
Students

⇄

Courses
```

Implemented using a **Join Table**.

```text
student_courses
```

---

# Normalization

Goal

Remove duplicate data.

Bad

```text
Order

Customer Name

Customer Phone

Customer Address

(repeated every order)
```

Good

```text
Customers

Orders
```

Orders only store

```text
customer_id
```

Benefits

- Less duplication
- Easier updates
- Better consistency

---

# Denormalization

Sometimes duplicate data intentionally.

Reason

Improve read performance.

Tradeoff

- Faster reads
- More storage
- Harder updates

---

# Indexes

Index = Shortcut for searching.

Without index

```text
Scan every row.
```

With index

```text
Jump directly to matching rows.
```

Use indexes on

- Primary Keys
- Foreign Keys
- Frequently searched columns
- Frequently sorted columns

Example

```sql
WHERE email = ?

WHERE user_id = ?
```

---

# Cost of Indexes

Indexes improve

✅ Reads

Indexes slow

❌ Inserts

❌ Updates

❌ Deletes

Reason

Index must also be updated.

Only create indexes when necessary.

---

# Transactions

A transaction is a group of operations

that either

```text
All Succeed

OR

All Fail
```

Example

Bank Transfer

```text
Subtract ₹100

↓

Add ₹100
```

If second step fails,

first step must also rollback.

---

# ACID Properties

## Atomicity

Everything

or

Nothing.

---

## Consistency

Database always remains valid.

---

## Isolation

Concurrent transactions

don't interfere.

---

## Durability

Committed data survives crashes.

---

# Concurrency

Many users access the database simultaneously.

Database ensures

- No data corruption
- Correct final state
- Safe concurrent updates

---

# Backend Database Workflow

```text
Design Schema

↓

Write Migration

↓

Run Migration

↓

Tables Created

↓

Application Uses Database
```

---

# Database Driver

Backend doesn't directly talk to PostgreSQL.

It uses a database driver.

```text
Node.js

↓

pg Driver

↓

PostgreSQL
```

Every language has its own driver.

---

# ORM (High Level)

ORM maps

```text
Objects

↓

Database Rows
```

Examples

- Prisma
- TypeORM
- Sequelize
- Hibernate

Advantages

- Faster development
- Less SQL writing

Still learn SQL well.

---

# Good Schema Design

- Give meaningful names.
- Keep tables focused.
- Avoid duplicate data.
- Define relationships.
- Use proper constraints.
- Add indexes only where needed.
- Prefer simple designs.

---

# Interview Takeaways

- Constraints enforce valid data.
- Primary Key uniquely identifies rows.
- Foreign Key creates relationships.
- One-to-Many is the most common relationship.
- Normalize to reduce duplication.
- Denormalize only for performance.
- Indexes speed up reads but slow writes.
- Transactions guarantee all-or-nothing execution.
- ACID properties are one of the most asked database interview topics.
- Always use migrations to manage schema changes.
- Learn SQL first; ORMs are built on top of SQL.


  # LEC 07 - Databases (Part 3)

# Designing a Database

Before writing APIs, design the database.

Typical workflow

```text
Requirements

↓

Entities

↓

Relationships

↓

Tables

↓

Indexes

↓

Queries
```

Never start by writing SQL.

---

# Identify Entities

For a Project Management App

```text
Users

Organizations

Projects

Tasks

Comments
```

Each entity usually becomes a table.

---

# Relationships

Example

```text
Organization

↓

Projects

↓

Tasks

↓

Comments
```

Another relationship

```text
User

↓

Many Tasks
```

Model these using **Foreign Keys**.

---

# Keep Tables Focused

Bad

```text
One huge table
```

containing

- Users
- Projects
- Tasks
- Comments

Good

Separate tables for each entity.

Benefits

- Easier maintenance
- Better performance
- Better relationships

---

# Think Before Creating Columns

Ask

- Is this always present?
- Can this change?
- Does it belong in another table?

Example

Instead of storing

```text
User Name
```

inside every task,

store

```text
user_id
```

and fetch the name using the relationship.

---

# Avoid Duplicate Data

Bad

```text
Task

Project Name

Organization Name

Owner Name
```

Good

```text
Task

project_id

owner_id
```

---

# Query Only What You Need

Bad

```sql
SELECT *
```

Good

```sql
SELECT id, name
```

Benefits

- Faster queries
- Less network usage
- Less memory

---

# Database Indexes (Practical)

Create indexes for columns used frequently in

- WHERE
- JOIN
- ORDER BY

Example

```sql
WHERE email = ?

WHERE project_id = ?

ORDER BY created_at
```

Don't index every column.

Too many indexes slow writes.

---

# Think About Read vs Write

Some applications

```text
Mostly Read
```

Example

- Blogs
- E-commerce catalogs

Need more indexes.

Others

```text
Mostly Write
```

Example

- Logging
- Analytics

Need fewer indexes.

---

# Database Drivers

Applications communicate with the database using drivers.

Example

```text
Node.js

↓

pg

↓

PostgreSQL
```

The driver sends SQL and receives results.

---

# SQL in Backend

Application

↓

Build SQL query

↓

Driver

↓

Database

↓

Result

↓

Application

↓

API Response

---

# ORMs

ORM = Object Relational Mapper.

Examples

- Prisma
- Drizzle
- TypeORM
- Sequelize

Advantages

- Less boilerplate
- Faster development
- Type safety (depending on ORM)

Disadvantages

- Can generate inefficient queries
- Harder to debug complex SQL

Learn SQL first, then use an ORM.

---

# Production Best Practices

- Design schema first.
- Keep tables normalized.
- Use constraints.
- Use migrations.
- Add indexes only after identifying bottlenecks.
- Never manually modify production databases.
- Always back up databases before risky changes.

---

# Common Interview Questions

### SQL or NoSQL?

Default answer:

> PostgreSQL unless flexibility or specific NoSQL features are required.

---

### Why use Foreign Keys?

To enforce valid relationships and maintain data integrity.

---

### Why use Migrations?

To version and safely deploy database schema changes.

---

### Why use Indexes?

To speed up searches and joins.

Tradeoff:

Slower writes.

---

### Why use Transactions?

To ensure multiple operations succeed or fail together.

---

# Final Takeaways

- Databases are the source of truth for backend applications.
- Model entities before writing SQL.
- Prefer normalized schemas.
- Use Primary Keys and Foreign Keys correctly.
- Index only important columns.
- Learn SQL thoroughly before relying on ORMs.
- PostgreSQL is an excellent default choice for most backend projects.
- 
