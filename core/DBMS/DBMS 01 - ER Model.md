# DBMS 01 - ER Model

## Navigation

- [1. Why Do We Need An ER Model?](#1-why-do-we-need-an-er-model)
- [2. What Is An Entity?](#2-what-is-an-entity)
- [3. What Is An Attribute?](#3-what-is-an-attribute)
- [4. Types Of Attributes](#4-types-of-attributes)
- [5. What Is A Relationship?](#5-what-is-a-relationship)
- [6. Degree Of Relationship](#6-degree-of-relationship)
- [7. Cardinality](#7-cardinality)
- [8. Participation Constraints](#8-participation-constraints)
- [9. Strong Entity](#9-strong-entity)
- [10. Weak Entity](#10-weak-entity)
- [11. Keys In ER Model](#11-keys-in-er-model)
- [12. ER Diagram Rules](#12-er-diagram-rules)
- [13. Converting ER Model To Relational Model](#13-converting-er-model-to-relational-model)
- [14. Interview Questions](#14-interview-questions)
- [15. Final Mental Model](#15-final-mental-model)

---

# 1. Why Do We Need An ER Model?

Suppose a college asks you:

```text
Build A Student Database
```

Should you immediately start writing SQL?

```sql
CREATE TABLE Student (...);
```

**No.**

First, you need to understand:

- What data should be stored?
- How is that data connected?
- What tables are actually required?

Imagine building a house.

Do engineers start laying bricks immediately?

```text
No

↓

Blueprint

↓

Construction
```

A database follows the same idea.

```text
Requirements

↓

ER Model

↓

Tables

↓

Database
```

The **ER Model** is simply the **blueprint of a database**.

It helps us design the database **before** writing SQL.

---

## Example

Suppose we are designing a college database.

The first questions are:

```text
Should we store Students?

Should we store Teachers?

Should we store Courses?

Should we store Departments?
```

Then,

```text
Which student studies which course?

Which teacher teaches which course?
```

Once these are decided,

creating tables becomes much easier.

---

## Definition

An ER Model is:

```text
A graphical blueprint

used to design

a database

before implementation.
```

It describes:

- What data exists.
- How data is connected.
- How the database should be organized.

It **does not store data**.

It only helps us design the database correctly.

---

## Why Is ER Model Important?

Without an ER Model,

developers may design completely different databases for the same problem.

The ER Model provides a common design that everyone agrees upon before implementation.

---

## Memory Trick

```text
Requirements

↓

ER Model

↓

Tables

↓

Database
```

---

## Interview Question

### Why Do We Need An ER Model?

```text
To design

a database

before creating

tables.
```

---

# Bridge

Now we know

**why** an ER Model exists.

The next question is:

```text
What are the objects

inside an ER Model?
```

The answer is:

```text
Entities
```
# 2. What Is An Entity?

Suppose we are designing

a college database.

Question:

```text
What information

should the database store?
```

Immediately we think of:

```text
Student

Teacher

Course

Department
```

These are the main objects

about which

we want to store data.

These objects are called:

```text
Entities
```

---

## Definition

An Entity is:

```text
A real-world object

about which

data is stored.
```

---

## Examples

### College Database

```text
Student

Teacher

Course

Department
```

---

### Banking Database

```text
Customer

Account

Loan

Transaction
```

---

### Hospital Database

```text
Doctor

Patient

Medicine

Room
```

---

## How To Identify An Entity?

Ask yourself:

```text
Do I need

to store

information

about this?
```

If the answer is

**Yes**,

it is most likely

an entity.

---

## Entity vs Not An Entity

| Entity | Not Entity |
|---------|------------|
| Student | Blue |
| Teacher | Fast |
| Customer | Tall |
| Product | Beautiful |

Notice that:

```text
Student
```

can exist independently.

But:

```text
Blue

Tall

Fast
```

only describe something.

They are **properties**,

not entities.

---

## One Common Confusion

Question:

Is **Department**

an entity

or an attribute?

Answer:

```text
It Depends.
```

### Case 1

If we only store:

```text
Department = CSE
```

it can be

an attribute.

---

### Case 2

Suppose we also store:

```text
Department Name

HOD

Building

Phone Number

Budget
```

Now we are storing

information

about the department itself.

So,

Department becomes

an entity.

---

## Rule

```text
If something

has its own

information

to be stored,

it becomes

an Entity.
```

---

## ER Diagram Symbol

Entities are represented

using a rectangle.

```text
+-----------+
|  Student  |
+-----------+
```

**Reference Image:**

https://www.geeksforgeeks.org/dbms/introduction-of-er-model/

---

## Interview Questions

### What Is An Entity?

```text
A real-world object

about which

data is stored.
```

---

### Give Examples Of Entities.

```text
Student

Teacher

Customer

Course

Product
```

---

## Bridge

Now we have

an entity.

Example:

```text
Student
```

Question:

```text
What information

about the student

should we store?
```

Examples:

```text
Name

Roll Number

Age

Department
```

These are called:

```text
Attributes
```

# 3. What Is An Attribute?

An Entity tells us:

```text
WHAT

we store.
```

An Attribute tells us:

```text
WHAT

we know

about it.
```

Example:

```text
Entity

↓

Student
```

Attributes:

```text
Roll Number

Name

Age

Department

Phone Number
```

Student is the object.

The remaining values

describe the student.

Hence,

they are called:

```text
Attributes
```

---

## Definition

An Attribute is:

```text
A property

that describes

an entity.
```

---

## More Examples

### Customer

```text
Customer

↓

Customer ID

Name

Address

Phone
```

---

### Product

```text
Product

↓

Product ID

Name

Price

Quantity
```

---

## Entity vs Attribute

| Entity | Attribute |
|---------|-----------|
| Student | Name |
| Teacher | Salary |
| Product | Price |
| Customer | Address |

Remember:

```text
Entity

↓

WHO / WHAT

----------------------

Attribute

↓

Information

About It
```

---

## Common Interview Confusion

Question:

Is Department

an Entity

or an Attribute?

Answer:

```text
It Depends.
```

If only this is stored:

```text
Department

=

CSE
```

then

Department

can be

an Attribute.

If we also store:

```text
Department Name

HOD

Building

Budget

Phone
```

then

Department itself

becomes an Entity.

---

## ER Symbol

Attributes

are represented

using an Oval.

```text
       (Name)

          |

+----------------+
|    Student     |
+----------------+
```

Reference Image:

https://www.geeksforgeeks.org/dbms/introduction-of-er-model/

---

# 4. Types Of Attributes

Not every attribute

behaves the same.

Some contain

one value.

Some contain

many values.

Some can be

broken into parts.

Some are calculated.

Hence,

attributes are classified.

---

## 1. Simple Attribute

Cannot be divided

further.

Examples:

```text
Age

Salary

Gender
```

---

## 2. Composite Attribute

Can be divided

into smaller attributes.

Example:

```text
Address

↓

House No

City

State

PIN Code
```

Instead of storing

one large value,

we store

its components.

---

## 3. Single-Valued Attribute

Contains

only one value.

Example:

```text
Roll Number

Date Of Birth

Blood Group
```

A student has

only one

roll number.

---

## 4. Multi-Valued Attribute

Contains

multiple values.

Example:

```text
Phone Numbers

↓

9876543210

9123456789
```

One student

may have

multiple numbers.

ER Symbol:

Double Oval

---

## 5. Derived Attribute

Can be calculated

from another attribute.

Example:

```text
Date Of Birth

↓

Age
```

Instead of storing

Age,

the DBMS

can calculate it

from DOB.

ER Symbol:

Dashed Oval.

---

## Summary

| Type | Example |
|------|----------|
| Simple | Age |
| Composite | Address |
| Single-Valued | Roll Number |
| Multi-Valued | Phone Numbers |
| Derived | Age (from DOB) |

---
so some attributes be composite but can have just a single value or can have many values !

## Interview Questions

### Which attribute

can be calculated?

```text
Derived Attribute
```

---

### Which attribute

stores multiple values?

```text
Multi-Valued Attribute
```

---

### Which attribute

can be divided?

```text
Composite Attribute
```

---

# 5. What Is A Relationship?

Until now,

we have only

individual entities.

```text
Student

Teacher

Course
```

Question:

How are they connected?

Example:

```text
Student

Studies

Course
```

or

```text
Teacher

Teaches

Course
```

The connection

between entities

is called

a Relationship.

---

## Definition

A Relationship is:

```text
An association

between

two or more

entities.
```

---

## Examples

College Database

```text
Student

Studies

Course
```

---

```text
Teacher

Teaches

Course
```

---

Bank Database

```text
Customer

Owns

Account
```

---

Amazon

```text
Customer

Places

Order
```

---

## ER Symbol

Relationships

are represented

using

a Diamond.

```text
+---------+

Student

+---------+

      |

      |

   <Studies>

      |

      |

+---------+

Course

+---------+
```

Reference Image:

https://www.geeksforgeeks.org/dbms/introduction-of-er-model/

---

# 6. Degree Of Relationship

Degree tells us:

```text
How many

entities

participate

in a relationship.
```

---

## Unary Relationship

One entity

participates.

Example:

```text
Employee

Manages

Employee
```

Same entity

appears twice.

Degree:

```text
1
```

---

## Binary Relationship

Two entities.

Example:

```text
Student

Studies

Course
```

Most common

relationship.

Degree:

```text
2
```

---

## Ternary Relationship

Three entities.

Example:

```text
Doctor

Prescribes

Medicine

To Patient
```

Degree:

```text
3
```

---

## Summary

| Degree | Entities |
|---------|----------|
| Unary | 1 |
| Binary | 2 |
| Ternary | 3 |

---

## Interview Questions

### Most common

relationship?

```text
Binary Relationship
```

### Degree means?

```text
Number of

participating

entities.
```

---

# Bridge

We now know

how entities

are connected.

But another

important question

still remains.

Suppose:

```text
Student

Studies

Course
```

Question:

Can one student

study

many courses?

Can one course

have

many students?

How do we

represent this?

This is called:

```text
Cardinality
```

# 7. Cardinality

We know that

entities are connected

using relationships.

Example:

```text
Student

Studies

Course
```

But this raises an important question.

```text
How many students

can study

a course?

How many courses

can one student study?
```

ER Models answer this

using:

```text
Cardinality
```

---

## Why Do We Need Cardinality?

Suppose we only write:

```text
Student

Studies

Course
```

Question:

Can one student

study only one course?

OR

many courses?

Nothing tells us.

Similarly,

can one course

have one student

or thousands?

Again,

nothing tells us.

The relationship

alone is incomplete.

We need to specify

**how many entities**

can participate.

That is exactly

what Cardinality tells us.

---

## Definition

Cardinality specifies:

```text
The maximum number

of entity instances

that can participate

in a relationship.
```

Simply remember:

```text
Maximum Participation
```

---

# Types Of Cardinality

There are four types.

---

## 1. One-To-One (1 : 1)

One entity

is related to

only one

other entity.

Example:

```text
Person

↔

Passport
```

One person

has one passport.

One passport

belongs to one person.

```text
Person 1

↓

Passport A

-----------------

Person 2

↓

Passport B
```

---

## 2. One-To-Many (1 : N)

One entity

can be related

to many entities.

Example:

```text
Department

↓

Students
```

One department

contains

many students.

But

one student

belongs to

only one department.

```text
CSE

↓

Arbaaz

Rahul

Ankit
```

---

## 3. Many-To-One (N : 1)

Exactly opposite

of One-To-Many.

Many entities

connect to

one entity.

Example:

```text
Students

↓

Department
```

Many students

belong to

one department.

This is simply

the reverse view

of the previous example.

---

## 4. Many-To-Many (M : N)

Many entities

connect to

many entities.

Example:

```text
Student

Studies

Course
```

One student

can study

many courses.

One course

can be studied

by many students.

```text
Student A

↓

DBMS

OS

CN

-------------------

DBMS

↓

Student A

Student B

Student C
```

This is the

most common

relationship

in databases.

---

# Summary

| Cardinality | Example |
|-------------|---------|
| 1 : 1 | Person → Passport |
| 1 : N | Department → Students |
| N : 1 | Students → Department |
| M : N | Students ↔ Courses |

---

# Common Interview Question

Question:

```text
Student

Studies

Course
```

What is

the cardinality?

Answer:

```text
Many

↓

Many
```

Because

one student

studies

many courses

and

one course

contains

many students.

---

# Common Confusion

Many students think

Degree

and

Cardinality

are the same.

They are not.

| Degree | Cardinality |
|----------|-------------|
| Number of participating entity types | Maximum participation of entities |

Example:

```text
Student

Studies

Course
```

Degree:

```text
2

(Binary Relationship)
```

Cardinality:

```text
Many : Many
```

One counts

**how many entities**

participate.

The other tells

**how many instances**

can participate.

---

# 8. Participation Constraints

Cardinality answered:

```text
Maximum

Participation.
```

Another question remains.

```text
Is participation

mandatory?

Or optional?
```

Example:

Can a student

exist

without joining

a course?

Maybe.

Can a passport

exist

without a person?

No.

This idea is called

Participation Constraint.

---

## Definition

Participation Constraint

specifies

whether participation

in a relationship

is:

```text
Mandatory

or

Optional.
```

---

## 1. Total Participation

Every entity

must participate.

Example:

```text
Passport

Belongs To

Person
```

A passport

cannot exist

without

a person.

Participation:

```text
Total
```

---

## 2. Partial Participation

Participation

is optional.

Example:

```text
Student

Enrolls

Course
```

A newly admitted student

may not have

enrolled

in any course yet.

The student

still exists.

Participation:

```text
Partial
```

---

# Difference Between

Cardinality

and

Participation

Cardinality asks:

```text
How Many?
```

Participation asks:

```text
Must Participate?
```

Example:

```text
Department

Has

Students
```

Cardinality:

```text
1 : N
```

Participation:

```text
Student

↓

Must belong

to one department

(Total)

Department

↓

May temporarily

have no students

(Partial)
```

---

# Interview Questions

### What does Cardinality specify?

```text
Maximum participation.
```

---

### What does Participation specify?

```text
Mandatory

or

Optional

participation.
```

---

### Difference?

```text
Cardinality

↓

How Many?

---------------------

Participation

↓

Mandatory

or Optional?
```

---

# Bridge

Until now,

every entity

could be identified

independently.

Example:

```text
Student

↓

Roll Number
```

But imagine

a dependent entity

that

cannot exist

without another entity.

Example:

```text
Employee

↓

Dependent
```

A dependent

has no identity

without

its employee.

This leads us to

Strong Entity

and

Weak Entity.

# 9. Strong Entity

A Strong Entity is an entity

that can be uniquely identified

using its own attributes.

It does **not**

depend on

any other entity.

---

## Example

```text
Student

↓

Roll Number
```

Every student

has a unique

Roll Number.

Therefore,

Student is

a Strong Entity.

---

More Examples

```text
Employee

↓

Employee ID
```

```text
Customer

↓

Customer ID
```

```text
Product

↓

Product ID
```

---

## ER Symbol

Single Rectangle

```text
+-----------+
| Student   |
+-----------+
```

---

# 10. Weak Entity

Now suppose

an employee

has dependents.

```text
Employee

↓

Dependent
```

Can a dependent

exist without

an employee?

```text
No.
```

Can we uniquely identify

a dependent

using only:

```text
Dependent Name
```

Suppose:

```text
John

John

John
```

Many employees

may have

dependents

with the same name.

So,

Dependent Name

alone

is not enough.

---

## Solution

We identify

the dependent

using:

```text
Employee ID

+

Dependent Name
```

Example:

```text
Employee ID = 101

Dependent = John
```

Now,

it becomes unique.

---

## Definition

A Weak Entity is:

```text
An entity

that cannot

be uniquely identified

without another entity.
```

The entity

it depends on

is called:

```text
Owner Entity
```

Here,

```text
Employee

↓

Owner Entity
```

```text
Dependent

↓

Weak Entity
```

---

## Identifying Relationship

The relationship

between

Owner Entity

and

Weak Entity

is called

an:

```text
Identifying Relationship
```

Because

it helps

identify

the weak entity.

---

## Partial Key

A Weak Entity

may have

its own attribute

that identifies it

**only within**

its owner.

Example:

```text
Dependent Name
```

This is called:

```text
Partial Key
```

Complete identification:

```text
Employee ID

+

Dependent Name
```

---

## Strong vs Weak Entity

| Strong Entity | Weak Entity |
|---------------|-------------|
| Has its own key | Depends on another entity |
| Independent | Dependent |
| Single Rectangle | Double Rectangle |

---

## Interview Question

Why is

Dependent

a Weak Entity?

```text
Because

it cannot

be uniquely identified

without

Employee.
```

---

# 11. Keys In ER Model

Keys uniquely identify

an entity.

Example:

```text
Student

↓

Roll Number
```

Roll Number

uniquely identifies

every student.

Hence,

it is the key.

---

Example:

```text
Employee

↓

Employee ID
```

---

Example:

```text
Customer

↓

Customer ID
```

Later,

we'll study

many kinds of keys

in detail.

For now,

remember:

```text
Key

↓

Unique Identifier
```

---

# 12. ER Diagram Rules

Every ER Diagram

uses standard symbols.

| Symbol | Meaning |
|---------|---------|
| Rectangle | Entity |
| Double Rectangle | Weak Entity |
| Oval | Attribute |
| Double Oval | Multi-Valued Attribute |
| Dashed Oval | Derived Attribute |
| Diamond | Relationship |
| Double Diamond | Identifying Relationship |

Reference Image:

https://www.geeksforgeeks.org/dbms/introduction-of-er-model/

---

# 13. Converting ER Model To Relational Model

An ER Diagram

is only

a design.

Databases

cannot execute

ER Diagrams.

They understand

tables.

Therefore,

every ER Diagram

must be converted

into tables.

Example:

ER Model

```text
Student

↓

Studies

↓

Course
```

becomes

Tables

```text
Student

(RollNo, Name)
```

```text
Course

(CourseID, Name)
```

The relationship

is represented

using

Foreign Keys.

We'll learn

Foreign Keys

properly

in DBMS02.

For now,

remember only:

```text
ER Diagram

↓

Tables

↓

Database
```

---

# 14. Interview Questions

### What is an Entity?

```text
A real-world object

about which

data is stored.
```

---

### What is an Attribute?

```text
A property

that describes

an entity.
```

---

### What is a Relationship?

```text
An association

between entities.
```

---

### Difference between

Degree

and

Cardinality?

```text
Degree

↓

Number of

participating entities

----------------------

Cardinality

↓

Maximum participation
```

---

### Difference between

Strong

and

Weak Entity?

```text
Strong

↓

Own Identity

--------------------

Weak

↓

Depends

on another entity.
```

---

# 15. Final Mental Model

```text
Requirements

↓

ER Model

↓

Entity

↓

Attribute

↓

Relationship

↓

Degree

↓

Cardinality

↓

Participation

↓

Strong / Weak Entity

↓

Keys

↓

ER Diagram

↓

Tables

↓

Database
```
