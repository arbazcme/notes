# DBMS 03 - DBMS Architecture

## Navigation

- [1. Why Do We Need A DBMS Architecture?](#1-why-do-we-need-a-dbms-architecture)
- [2. Basic Database Communication](#2-basic-database-communication)
- [3. One-Tier Architecture](#3-one-tier-architecture)
- [4. Two-Tier Architecture](#4-two-tier-architecture)
- [5. Three-Tier Architecture](#5-three-tier-architecture)
- [6. Three Schema Architecture](#6-three-schema-architecture)
- [7. External Level](#7-external-level)
- [8. Conceptual Level](#8-conceptual-level)
- [9. Internal Level](#9-internal-level)
- [10. Mapping Between Levels](#10-mapping-between-levels)
- [11. Data Independence](#11-data-independence)
- [12. Physical Data Independence](#12-physical-data-independence)
- [13. Logical Data Independence](#13-logical-data-independence)
- [14. Interview Questions](#14-interview-questions)
- [15. Final Mental Model](#15-final-mental-model)

---

# 1. Why Do We Need A DBMS Architecture?

Suppose you built

a Student database.

Question:

```text
How does

your application

actually retrieve

student data?
```

Does the frontend

directly open

the database?

```text
No.
```

There is a proper

architecture

that controls

how data flows.

Understanding this architecture

helps us understand:

- How applications communicate with databases.
- Why backend servers exist.
- Why users never directly access database files.
- Why databases remain secure.

---

## Real-Life Example

Suppose you open Instagram.

You click:

```text
Profile
```

Do you directly

read Instagram's database?

No.

Instead,

the request travels

through multiple layers.

```text
You

↓

Instagram App

↓

Backend Server

↓

DBMS

↓

Database
```

The data then

returns

through the same path.

---

## Why Not Connect Directly?

Imagine

every user

could directly access

the database.

Anyone could:

- Delete records.
- Modify salaries.
- Read private information.

Clearly,

this is unsafe.

A proper architecture

solves this problem.

---

# 2. Basic Database Communication

Almost every application

works like this.

```text
User

↓

Frontend

↓

Backend

↓

DBMS

↓

Database
```

Let's understand

each layer.

---

## User

The person

using the application.

Examples:

```text
Instagram User

Amazon Customer

Bank Customer
```

---

## Frontend

The user interface.

Examples:

```text
Website

Mobile App

Desktop Application
```

Its job is:

- Take user input.
- Display results.

The frontend

does **not**

directly access

the database.

---

## Backend

The backend

contains

business logic.

Example:

Suppose

you login.

The backend checks:

```text
Does User Exist?

↓

Password Correct?

↓

Generate JWT?

↓

Send Response
```

Only after

all validations

does it ask

the DBMS

for data.

---

## DBMS

The DBMS receives

requests

from the backend.

Example:

```sql
SELECT *

FROM Student

WHERE RollNo = 101;
```

The DBMS:

- Searches tables.
- Uses indexes.
- Retrieves data.
- Returns results.

---

## Database

Finally,

the actual data

stored on disk.

Example:

```text
Student Table

Teacher Table

Course Table
```

Notice:

The database

does **not**

communicate

with users directly.

Everything goes through

the DBMS.

---

## Complete Flow

```text
User

↓

Frontend

↓

Backend

↓

DBMS

↓

Database

↓

DBMS

↓

Backend

↓

Frontend

↓

User
```

---

## Interview Question

### Why Doesn't The Frontend

Directly Access

The Database?

Because:

- Security
- Validation
- Business Logic
- Authentication

are handled

by the backend.

---

# Bridge

Now we know

how applications

communicate

with databases.

But another question arises.

Where is

the application

running?

Sometimes,

everything runs

on one computer.

Sometimes,

the database

is on another computer.

Sometimes,

millions of users

share one database.

These different setups

are called:

```text
Database Architectures
```

We study them next:

```text
1-Tier

↓

2-Tier

↓

3-Tier
```

# 3. One-Tier Architecture

The simplest architecture.

Everything runs

on the same computer.

```text
+---------------------------+
|         Computer          |
|                           |
|  Application              |
|       ↓                   |
|     DBMS                  |
|       ↓                   |
|    Database               |
+---------------------------+
```

The user,

application,

DBMS,

and database

all exist

on one machine.

---

## Example

A student installs

MySQL

on their laptop.

They also install

MySQL Workbench.

Everything runs locally.

```text
Laptop

↓

Workbench

↓

MySQL

↓

Database
```

---

## Advantages

- Very simple
- Easy to learn
- No network required
- Fast for a single user

---

## Disadvantages

- Not suitable for multiple users.
- Poor security.
- Cannot scale.

---

## Where Is It Used?

- Learning DBMS
- Personal projects
- Small desktop applications

---

# 4. Two-Tier Architecture

Now suppose

the database

is moved

to another computer.

The application

communicates

directly

with the DBMS.

```text
+-------------+        +----------------+
| Client      |        | Database Server|
|             |        |                |
| Application | -----> | DBMS           |
|             |        | Database       |
+-------------+        +----------------+
```

The client

contains

the application.

The server

contains

the database.

---

## Example

A company has

10 employees.

Each employee

runs

the application.

All applications

connect directly

to one database server.

```text
Employee

↓

Application

↓

Database Server
```

---

## Advantages

- Supports multiple users.
- Centralized database.
- Easier backup.
- Better than 1-Tier.

---

## Disadvantages

Business logic

is inside

every client.

If business rules change,

every application

must be updated.

Security

is also weaker

because clients

communicate

directly

with the database.

---

# Why Isn't Two-Tier Enough?

Imagine

Instagram

with

100 million users.

Would every phone

connect directly

to the database?

```text
No.
```

Problems:

- Too many connections.
- Security risks.
- Validation becomes difficult.
- Database becomes overloaded.

A better solution

was needed.

---

# 5. Three-Tier Architecture

Modern applications

use

Three-Tier Architecture.

Instead of

connecting directly,

the client

first talks

to a backend server.

```text
Client

↓

Backend Server

↓

DBMS

↓

Database
```

Diagram:

```text
+-----------+      +----------------+      +----------------+
| Client    | ---> | Backend Server | ---> | Database Server|
| (Browser) |      | Business Logic |      | DBMS + Database|
+-----------+      +----------------+      +----------------+
```

---

## Why Add A Backend?

Because

the backend

acts as

a middleman.

Instead of

100 million users

talking

to the database,

only

the backend

does.

---

## Responsibilities

The backend handles:

```text
Authentication

↓

Authorization

↓

Validation

↓

Business Logic

↓

Database Requests
```

The database

only stores

and retrieves data.

---

## Real-Life Example

Suppose you login

to Instagram.

Flow:

```text
Browser

↓

Backend

↓

DBMS

↓

Database
```

The backend checks:

```text
User Exists?

↓

Password Correct?

↓

Generate Token?

↓

Return Response
```

Only then

does it query

the database.

---

## Advantages

- High Security
- Easy Maintenance
- Better Performance
- Easy Scaling
- Business Logic in one place
- Suitable for millions of users

---

## Examples

```text
Instagram

Amazon

Flipkart

Netflix

Banking Systems

WhatsApp
```

All use

Three-Tier Architecture.

---

# Comparison

| Feature | 1-Tier | 2-Tier | 3-Tier |
|----------|--------|--------|--------|
| Machines | 1 | 2 | 3 Logical Layers |
| Multiple Users | Poor | Good | Excellent |
| Security | Low | Medium | High |
| Scalability | Poor | Medium | Excellent |
| Used Today | Rare | Small Apps | Most Modern Apps |

---

## Interview Questions

### Which architecture

is most common today?

```text
Three-Tier
```

---

### Why is

Three-Tier

preferred?

```text
Security

↓

Scalability

↓

Business Logic

↓

Easy Maintenance
```

---

# Bridge

Until now,

we studied

how applications

communicate

with databases.

Question:

Inside the DBMS,

is all data

viewed the same way

by everyone?

```text
No.
```

A user,

a programmer,

and the DBMS itself

all see

the database

differently.

To solve this,

DBMS divides

the database

into three views.

This is called:

```text
Three Schema Architecture
```
# Why Different Database Views?

A user, programmer, and DBMS all need **different information** from the same database.

### Example

Database:

```text
Student
------------------------
ID | Name | CGPA | Fees | Password
```

### Student View

* Name
* CGPA
* Fees

### Programmer View

* Writes SQL queries.
* Doesn't care how data is stored.

### DBMS View

* Disk pages
* Blocks
* Indexes
* Physical storage

## Why?

Each has a different job, so each needs a different view.

This is why DBMS uses the **Three-Schema Architecture**:

* **External Level** → User-specific views.
* **Conceptual Level** → Complete logical database.
* **Internal Level** → Physical storage details.

# 6. Three Schema Architecture

Until now,

we learned how

applications communicate

with a database.

Question:

Do all users

need to see

the database

in the same way?

```text
No.
```

Example:

A student logs into

the college portal.

Should they see:

- Other students' passwords?
- Salary records?
- Internal storage details?

```text
No.
```

Different users

need different views

of the same database.

To solve this,

DBMS divides

the database

into three levels.

This is called:

```text
Three Schema Architecture
```

---

## Why Do We Need It?

Suppose we have

one database.

```text
College Database
```

Different people

use it.

### Student

Wants to see:

```text
Name

Marks

Attendance
```

---

### Teacher

Wants to see:

```text
Student Marks

Attendance

Course Details
```

---

### Database Administrator (DBA)

Wants to see:

```text
Entire Database

Storage

Indexes

Files
```

Notice:

Everyone is using

the same database,

but everyone

needs a different view.

---

## The Three Levels

```text
External Level

↓

Conceptual Level

↓

Internal Level
```

Think of it like

three layers

of abstraction.

---

# 7. External Level

The External Level

is the

**User's View**

Each user

sees only

the data

they need.

Example:

Student Portal

```text
Name

Roll Number

Marks
```

---

Teacher Portal

```text
Students

Marks

Attendance
```

---

Account Section

```text
Fees

Payments

Scholarships
```

Although

all are using

the same database,

each user

gets

a different view.

---

## Definition

External Level

describes:

```text
How

individual users

see the database.
```

---

# 8. Conceptual Level

Now imagine

removing

all user-specific views.

What remains?

The complete

logical structure

of the database.

Example:

```text
Student

Teacher

Course

Department

Marks
```

and

their relationships.

This level

describes

the entire database,

but

does **not**

care

how it is stored

on disk.

---

## Definition

Conceptual Level

describes:

```text
The complete

logical structure

of the database.
```

Think of it as

the master blueprint.

---

# 9. Internal Level

Now go

one step deeper.

Question:

How are

the tables

actually stored?

Questions like:

```text
Which disk?

Which blocks?

Which files?

Which indexes?

B+ Tree?

Hash Index?

Compression?
```

These details

belong to

the Internal Level.

Users

never see

this level.

Even programmers

usually don't.

Only

the DBMS

manages it.

---

## Definition

Internal Level

describes:

```text
How data

is physically stored

inside

the database.
```

---

# Complete Picture

```text
External Level

(User View)

↓

Conceptual Level

(Logical Database)

↓

Internal Level

(Physical Storage)
```

---

## Real-Life Analogy

Think of a library.

### External Level

Visitor sees:

```text
Books

Shelves

Reading Room
```

---

### Conceptual Level

Library Manager sees:

```text
All Books

All Members

All Categories
```

---

### Internal Level

Storage Staff sees:

```text
Rack Numbers

Storage Rooms

Inventory System
```

Everyone

looks at

the same library,

but

at different levels.

---

# 10. Mapping Between Levels

Question:

How are

these levels

connected?

Through

Mappings.

```text
External View

↓

External-Conceptual Mapping

↓

Conceptual Schema

↓

Conceptual-Internal Mapping

↓

Internal Storage
```

Mappings

simply tell

the DBMS

how one level

corresponds

to another.

Users

never worry

about mappings.

The DBMS

handles them

automatically.

---

## Interview Questions

### Why do we need

Three Schema Architecture?

```text
To provide

different views

of the same database

while hiding

implementation details.
```

---

### Which level

does the user see?

```text
External Level
```

---

### Which level

contains

the complete database design?

```text
Conceptual Level
```

---

### Which level

stores

physical storage details?

```text
Internal Level
```

---

# Bridge

Now we understand

the three levels.

Question:

Suppose

the DBA changes

how data

is stored

inside

the Internal Level.

Should

the application

stop working?

```text
No.
```

This idea

is called:

```text
Data Independence
```

It is the biggest advantage

of

Three Schema Architecture.

# 11. Data Independence

We learned that

the database has

three levels.

```text
External Level

↓

Conceptual Level

↓

Internal Level
```

Question:

Suppose

the DBA changes

something

at one level.

Should

the other levels

also change?

```text
No.
```

This ability

is called:

```text
Data Independence
```

---

## Definition

Data Independence means:

```text
Changes

at one level

should not

affect

higher levels.
```

Simply remember:

```text
Change One Layer

↓

Other Layers

Continue Working
```

---

## Why Do We Need It?

Imagine

your application

asks for

student data.

```sql
SELECT *

FROM Student;
```

Today,

the DBMS stores

the Student table

normally.

Tomorrow,

the DBA decides to:

- Create indexes.
- Compress data.
- Move data to another disk.
- Change file organization.

Question:

Should your SQL query

stop working?

```text
No.
```

The DBMS hides

those changes.

Your application

continues working.

That is

Data Independence.

---

# 12. Physical Data Independence

Physical means:

```text
Internal Level
```

Suppose

the DBA changes:

```text
Old

↓

Normal Storage

----------------------

New

↓

B+ Tree

↓

Hash Index

↓

Compression

↓

Different Disk
```

Question:

Should the tables

or application

change?

```text
No.
```

Only the

Internal Level

changed.

Everything above it

continues working.

This is called:

```text
Physical

Data Independence
```

---

## Definition

Physical Data Independence means:

```text
Changes

to physical storage

do not affect

the logical database

or applications.
```

---

## Real-Life Example

Suppose

Google changes

how Gmail emails

are stored

inside its servers.

Question:

Do users notice?

```text
No.
```

Emails still open

normally.

Only

the internal storage

changed.

---

# 13. Logical Data Independence

Logical means:

```text
Conceptual Level
```

Now suppose

the database designer

changes

the logical structure.

Example:

Old Student Table

```text
RollNo

Name

Phone
```

Later,

a new column

is added.

```text
RollNo

Name

Phone

Email
```

Question:

Should

every application

immediately stop working?

Ideally,

```text
No.
```

Small logical changes

should not require

rewriting

every application.

This is called:

```text
Logical

Data Independence
```

---

## Definition

Logical Data Independence means:

```text
Changes

to the logical schema

should not affect

user views

or applications.
```

---

## Which Is Harder?

Question:

Which is easier?

```text
Changing Storage

or

Changing Tables?
```

Changing storage

is easier.

Changing tables

can affect

many applications.

Therefore,

```text
Physical Data Independence

↓

Easy

------------------------

Logical Data Independence

↓

Hard
```

This is a very common

interview question.

---

# Summary

| Type | Changes | Does Application Change? |
|------|----------|--------------------------|
| Physical | Storage | No |
| Logical | Tables / Schema | Ideally No |

---

# Interview Questions

### What is Data Independence?

```text
The ability

to change

one level

without affecting

higher levels.
```

---

### Physical Data Independence?

```text
Storage changes

do not affect

applications.
```

---

### Logical Data Independence?

```text
Logical schema changes

do not affect

user views.
```

---

### Which is harder?

```text
Logical

Data Independence
```

---

# 14. Final Mental Model

```text
User

↓

Frontend

↓

Backend

↓

DBMS

↓

Database

----------------------

1-Tier

2-Tier

3-Tier

----------------------

External Level

↓

Conceptual Level

↓

Internal Level

----------------------

Mappings

↓

Data Independence

↓

Physical

↓

Logical
```

---

# DBMS03 Complete ✅

## What You Learned

- Why DBMS Architecture is needed.
- How applications communicate with databases.
- 1-Tier, 2-Tier and 3-Tier architectures.
- Three Schema Architecture.
- External, Conceptual and Internal levels.
- Mapping between levels.
- Data Independence.
- Physical Data Independence.
- Logical Data Independence.

## Bridge To DBMS04

Until now,

we have learned:

```text
How databases

are designed.

↓

How databases

are organized.

↓

How applications

communicate

with databases.
```

Now comes

the most practical chapter.

Question:

```text
How do we actually

talk to

a DBMS?
```

The answer is:

```text
SQL

(Structured Query Language)
```

Everything we have learned so far

will now be used

through SQL.
