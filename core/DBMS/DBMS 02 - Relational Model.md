# DBMS 02 - Relational Model

## Navigation

- [1. Why Do We Need A Relational Model?](#1-why-do-we-need-a-relational-model)
- [2. What Is A Relation?](#2-what-is-a-relation)
- [3. What Is A Tuple?](#3-what-is-a-tuple)
- [4. What Is An Attribute?](#4-what-is-an-attribute)
- [5. What Is A Domain?](#5-what-is-a-domain)
- [6. Degree Of A Relation](#6-degree-of-a-relation)
- [7. Cardinality Of A Relation](#7-cardinality-of-a-relation)
- [8. NULL Values](#8-null-values)
- [9. Super Key](#9-super-key)
- [10. Candidate Key](#10-candidate-key)
- [11. Primary Key](#11-primary-key)
- [12. Alternate Key](#12-alternate-key)
- [13. Composite Key](#13-composite-key)
- [14. Foreign Key](#14-foreign-key)
- [15. Integrity Constraints](#15-integrity-constraints)
- [16. Interview Questions](#16-interview-questions)
- [17. Final Mental Model](#17-final-mental-model)

---

# 1. Why Do We Need A Relational Model?

In DBMS01,

we designed the database

using an

ER Model.

Example:

```text
Student

Studies

Course
```

This is called

a logical design.

Question:

Can a DBMS

store

ER diagrams

directly?

```text
No.
```

A DBMS understands

only tables.

So,

every ER Model

must eventually

be converted into

tables.

This way of organizing

data into tables

is called

the:

```text
Relational Model
```

---

## Why Is It Called "Relational"?

The word

"Relation"

has a special meaning

in DBMS.

It simply means:

```text
Table
```

Example:

```text
Student
```

| RollNo | Name | Age |
|--------|------|-----|
| 101 | Arbaaz | 21 |
| 102 | Rahul | 20 |

This table

is called

a Relation.

Later,

we'll learn

how different

relations

are connected

using

Foreign Keys.

---

## From ER Model To Relational Model

ER Model

```text
Student

↓

Studies

↓

Course
```

becomes

```text
Student Table

Course Table
```

Later,

the relationship

will be represented

using

keys.

---

## Definition

The Relational Model is

a way of

organizing

data

using

relations

(tables).

---

## Why Is It Popular?

The Relational Model is:

- Simple
- Easy to understand
- Easy to query
- Easy to maintain
- Supported by almost every DBMS

Examples:

```text
MySQL

PostgreSQL

Oracle

SQL Server
```

all use

the Relational Model.

---

## Memory Trick

```text
ER Model

↓

Tables

↓

Relational Model

↓

SQL
```

---

## Interview Question

### Why Do We Need

The Relational Model?

```text
To organize

data

into tables

that a DBMS

can store

and manage.
```

---

# Bridge

If the Relational Model

stores

data in tables,

the next question is:

```text
What exactly

is a table

called

in DBMS?
```

Answer:

```text
Relation
```
# 2. What Is A Relation?

In the Relational Model,

everything is stored

inside a table.

In DBMS,

a table is called a:

```text
Relation
```

Example:

### Student Relation

| RollNo | Name | Age | Department |
|--------|------|-----|------------|
| 101 | Arbaaz | 21 | CSE |
| 102 | Rahul | 20 | ECE |
| 103 | Priya | 22 | CSE |

This entire table is called a:

```text
Relation
```

---

## Definition

A Relation is:

```text
A table

that stores

related data.
```

Think of it as:

```text
Relation

=

Table
```

Throughout DBMS,

you can almost

replace the word

"Relation"

with

"Table".

---

# 3. What Is A Tuple?

Look at the table again.

| RollNo | Name | Age | Department |
|--------|------|-----|------------|
| 101 | Arbaaz | 21 | CSE |
| 102 | Rahul | 20 | ECE |
| 103 | Priya | 22 | CSE |

Each horizontal row

represents

one student.

One row is called a:

```text
Tuple
```

Example:

```text
101

Arbaaz

21

CSE
```

This complete row

is one tuple.

---

## Definition

A Tuple is:

```text
One row

of a relation.
```

---

## Example

Student Relation

| RollNo | Name | Age |
|--------|------|-----|
| 101 | Arbaaz | 21 |

This entire row

is one tuple.

---

# 4. What Is An Attribute?

Look at the same table.

| RollNo | Name | Age | Department |
|--------|------|-----|------------|

Each column

describes

one property

of the student.

Each column

is called an:

```text
Attribute
```

Example:

```text
RollNo

Name

Age

Department
```

These are attributes.

---

## Definition

An Attribute is:

```text
A column

of a relation.
```

Notice the difference

between ER Model

and Relational Model.

ER Model:

```text
Attribute

↓

Property

of an Entity
```

Relational Model:

```text
Attribute

↓

Column

of a Table
```

Both describe

the same thing,

but from

different viewpoints.

---

# 5. What Is A Domain?

Question:

Can Age contain:

```text
Arbaaz
```

No.

Can Roll Number contain:

```text
Blue
```

Again,

No.

Every attribute

accepts

only certain values.

That allowed set

is called

the:

```text
Domain
```

---

## Definition

A Domain is:

```text
The set

of valid values

an attribute

can store.
```

---

## Example

| Attribute | Domain |
|------------|---------|
| Age | 0–150 |
| Gender | Male, Female, Other |
| Department | CSE, ECE, MECH |
| RollNo | Positive Integers |

If we try:

```text
Age = Rahul
```

it violates

the domain.

---

# 6. Degree Of A Relation

Degree simply means:

```text
Number of

Attributes

(Columns)
```

Example:

| RollNo | Name | Age | Department |

Columns:

```text
RollNo

Name

Age

Department
```

Total:

```text
4
```

Degree:

```text
4
```

---

# 7. Cardinality Of A Relation

Cardinality means:

```text
Number of

Tuples

(Rows)
```

Example:

| RollNo | Name |
|--------|------|
|101|Arbaaz|
|102|Rahul|
|103|Priya|

Rows:

```text
3
```

Therefore,

Cardinality:

```text
3
```

---

## Common Interview Confusion

Many students confuse

Degree

and

Cardinality.

Remember:

| Degree | Cardinality |
|---------|-------------|
| Columns | Rows |

Easy trick:

```text
Vertical

↓

Degree

------------------

Horizontal

↓

Cardinality
```

Actually,

when you look at a table:

```text
Columns

↓

Degree

Rows

↓

Cardinality
```

---

# 8. NULL Values

Question:

Suppose

a student's

phone number

is not available.

Should we write:

```text
0
```

No.

Should we write:

```text
"Unknown"
```

No.

Instead,

DBMS uses:

```text
NULL
```

---

## What Does NULL Mean?

NULL does **not** mean:

```text
0

or

Empty String
```

NULL means:

```text
Value

is missing

unknown

or

not applicable.
```

Examples:

| RollNo | Name | Phone |
|--------|------|--------|
|101|Arbaaz|NULL|

This means

the phone number

is currently unknown.

---

## Interview Questions

### Relation means?

```text
Table
```

---

### Tuple means?

```text
Row
```

---

### Attribute means?

```text
Column
```

---

### Domain means?

```text
Allowed values

for an attribute.
```

---

### Degree means?

```text
Number of columns.
```

---

### Cardinality means?

```text
Number of rows.
```

---

### Is NULL equal to zero?

```text
No.

NULL means

Unknown

or

Missing Value.
```

---

# Bridge

Now we know

how a table

is organized.

Question:

Suppose

the Student table

contains

10,000 students.

How do we uniquely

identify

one particular student?

Answer:

```text
Keys
```

The next topic

is one of

the most important

in DBMS.

# 9. Keys

Suppose we have

the following Student table.

| RollNo | Aadhaar | Email | Name |
|--------|----------|--------|------|
|101|123456789012|a@gmail.com|Arbaaz|
|102|987654321098|b@gmail.com|Rahul|
|103|567890123456|c@gmail.com|Priya|

Question:

How do we uniquely

identify one student?

Possible answers:

```text
Roll Number

Aadhaar Number

Email
```

All three are unique.

Such attributes

are called

**Keys**.

---

# 10. Super Key

A Super Key is:

```text
Any attribute

or combination

of attributes

that uniquely

identifies

a row.
```

Example:

```text
Roll Number
```

Unique.

So,

Super Key.

---

```text
Aadhaar
```

Also unique.

Super Key.

---

```text
Email
```

Also unique.

Super Key.

---

Even this works:

```text
Roll Number

+

Name
```

Still unique.

Also a Super Key.

---

Or

```text
Roll Number

+

Email
```

Still unique.

Still a Super Key.

---

Notice something.

We are adding

extra attributes

even though

Roll Number

alone was enough.

Those extra attributes

are unnecessary,

but the combination

still uniquely identifies

the row.

Hence,

it is still

a Super Key.

---

## Summary

A Super Key

may contain

extra attributes.

---

# 11. Candidate Key

Question:

Do we really need

this?

```text
Roll Number

+

Email
```

No.

Roll Number alone

already identifies

the student.

So,

remove the unnecessary

attributes.

The remaining

minimum key

is called

the:

```text
Candidate Key
```

---

## Definition

A Candidate Key is:

```text
A minimal

Super Key.
```

Minimal means:

```text
No unnecessary

attributes.
```

---

## Example

Candidate Keys:

```text
Roll Number

Aadhaar

Email
```

Each one

alone

uniquely identifies

the student.

---

Not Candidate Keys:

```text
Roll Number

+

Email
```

Because

Email

is unnecessary.

---

# Super Key vs Candidate Key

| Super Key | Candidate Key |
|------------|---------------|
| Unique | Unique |
| May contain extra attributes | No extra attributes |
| Not necessarily minimal | Always minimal |

---

# 12. Primary Key

Suppose

there are

three Candidate Keys.

```text
Roll Number

Aadhaar

Email
```

Question:

Which one

should the DBMS use

to identify

students?

We choose

one Candidate Key.

That chosen key

is called

the:

```text
Primary Key
```

---

## Definition

A Primary Key is:

```text
The Candidate Key

chosen

to uniquely identify

every row.
```

Usually,

the simplest

and most stable

candidate key

is selected.

Example:

```text
Roll Number
```

Primary Key.

---

## Rules

A Primary Key

must:

- Be unique.
- Never be NULL.
- Never identify two rows.

---

# 13. Alternate Key

Question:

What happens

to the remaining

Candidate Keys?

Example:

```text
Candidate Keys

↓

Roll Number

Aadhaar

Email
```

Roll Number

became

Primary Key.

Remaining:

```text
Aadhaar

Email
```

These are called:

```text
Alternate Keys
```

---

## Definition

Alternate Keys are:

```text
Candidate Keys

that were

not chosen

as the

Primary Key.
```

---

# Relationship Between Keys

```text
Super Keys

↓

Candidate Keys

↓

Primary Key
```

Remaining Candidate Keys

↓

Alternate Keys

---

# 14. Composite Key

Sometimes,

one attribute

is not enough.

Example:

Course Registration.

| StudentID | CourseID |
|------------|----------|
|101|DBMS|
|101|OS|
|102|DBMS|

Question:

Can StudentID

alone

identify a row?

No.

Student 101

appears twice.

Can CourseID

alone

identify a row?

No.

DBMS

appears twice.

But together:

```text
StudentID

+

CourseID
```

become unique.

This combination

is called

a:

```text
Composite Key
```

---

## Definition

A Composite Key is:

```text
A key formed

using multiple

attributes.
```

---



```text
Super Key
├── Candidate Key (minimal Super Key)
│     └── Primary Key (chosen Candidate Key)

A Composite Key can be a Candidate Key if all its columns are necessary.
```


# 15. Foreign Key

Suppose

we have

two tables.

Student

| RollNo | Name |
|--------|------|
|101|Arbaaz|
|102|Rahul|

---

Marks

| RollNo | DBMS |
|--------|------|
|101|95|
|102|88|

Question:

How does

the Marks table

know

which student

a row belongs to?

Because both tables

contain:

```text
Roll Number
```

Here,

Roll Number

inside

Marks

is called

a:

```text
Foreign Key
```

---

## Definition

A Foreign Key is:

```text
An attribute

that refers to

the Primary Key

of another table.
```

It creates

a relationship

between tables.

---

# Why Does a Foreign Key Usually Reference a Primary Key?

## Without a Primary Key

Suppose:

```text
Student
-----------------
Name | Age
-----------------
Ali  | 20
Ali  | 22
Sara | 21
```

Now `Enrollment` stores:

```text
Enrollment
-------------------------
StudentName | Course
-------------------------
Ali         | DBMS
```

Question:

> Which Ali?

* Ali (20)?
* Ali (22)?

❌ You don't know.

---

## With a Primary Key

```text
Student
-------------------------
StudentID (PK) | Name
-------------------------
101            | Ali
102            | Ali
103            | Sara
```

```text
Enrollment
-------------------------
StudentID (FK) | Course
-------------------------
101            | DBMS
```

Now there is no confusion.

`101` points to **exactly one student**.

---

## Does a Foreign Key Have to Reference ONLY a Primary Key?

**No.**

A Foreign Key can reference **any UNIQUE key**, such as:

* ✅ Primary Key
* ✅ Candidate Key (declared `UNIQUE`)

The only requirement is that the referenced column(s) must uniquely identify a row.



## Why Is It Needed?

Without

Foreign Keys,

tables become

independent.

Using

Foreign Keys,

DBMS understands

how tables

are connected.

---

# 16. Integrity Constraints

Integrity means:

```text
Correctness

of data.
```

DBMS prevents

invalid data

using constraints.

---

## Domain Constraint

```text
Age

↓

0–150
```

Age = "Rahul"

Not allowed.

---

## Primary Key Constraint

Primary Key

must be:

```text
Unique

Not NULL
```

---

## Foreign Key Constraint


A Foreign Key

must refer

to an existing

Primary Key.

Example:

Student Table

contains:

```text
101

102
```

Marks Table

cannot contain:

```text
999
```

because

Student 999

does not exist.

---

## Entity Integrity

Primary Key

can never

be NULL.

---

## Referential Integrity

Every Foreign Key

must reference

an existing row.

---

# Interview Questions

### Difference

between

Super Key

and

Candidate Key?

```text
Super Key

↓

May contain

extra attributes.

----------------------

Candidate Key

↓

Minimal

Super Key.
```

---

### Primary Key?

```text
Chosen

Candidate Key.
```

---

### Alternate Key?

```text
Remaining

Candidate Keys.
```

---

### Composite Key?

```text
Multiple attributes

together

form a key.
```

---

### Foreign Key?

```text
References

the Primary Key

of another table.
```

---

# Final Mental Model

```text
ER Model

↓

Tables

↓

Relation

↓

Tuple

↓

Attribute

↓

Domain

↓

Degree

↓

Cardinality

↓

NULL

↓

Super Key

↓

Candidate Key

↓

Primary Key

↓

Alternate Key

↓

Composite Key

↓

Foreign Key

↓

Integrity Constraints
```
