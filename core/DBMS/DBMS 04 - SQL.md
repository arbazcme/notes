# DBMS 04 - SQL (Structured Query Language)

## Navigation 

- [1. Why Do We Need SQL?](#1-why-do-we-need-sql)
- [2. Our Database](#2-our-database)
- [3. First SQL Query](#3-first-sql-query)
- [4. How Does DBMS Execute A Query?](#4-how-does-dbms-execute-a-query)
- [5. WHERE Clause](#5-where-clause)
- [6. Building Complex Conditions](#6-building-complex-conditions)
- [7. ORDER BY](#7-order-by)
- [8. DISTINCT, LIMIT & Aggregate Functions](#8-distinct-limit--aggregate-functions)
- [9. GROUP BY & HAVING](#9-group-by--having)
- [10. JOINS](#10-joins)
- [11. Subqueries](#11-subqueries)
- [12. IN, EXISTS, ANY & ALL](#12-in-exists-any--all)
- [13. Views](#13-views)
- [14. Stored Procedures](#14-stored-procedures)
- [15. Functions](#15-functions)
- [16. Triggers](#16-triggers)
- [17. Final Mental Model](#final-mental-model)

---

# 1. Why Do We Need SQL?

So far we have learned:

```text
ER Model

↓

Tables

↓

Database
```

Question:

Now that the database exists,

how do we talk to it?

Suppose we want to ask:

```text
Show all students.

Find Rahul.

Display every CSE student.

Count students.

Delete a student.

Add a new student.
```

Can we simply write:

```text
Give me Rahul.
```

No.

The DBMS understands

only one language.

That language is:

```text
SQL

(Structured Query Language)
```

---

## What Is SQL?

SQL is a language

used to communicate

with a relational database.

Using SQL,

we can:

- Store data.
- Read data.
- Update data.
- Delete data.
- Create tables.
- Delete tables.
- Control access.

Think of SQL as:

```text
Human

↓

SQL

↓

DBMS

↓

Database
```

---

## Real-Life Example

Suppose

Instagram wants

your profile.

The backend sends

an SQL query.

```text
Backend

↓

SQL Query

↓

DBMS

↓

Database

↓

Result
```

Almost every website

you use

works like this.

---

# 2. Our Database

Throughout this chapter,

we'll use

the same database.

## Student

| RollNo | Name | Age | Department |
|--------|------|-----|------------|
|101|Arbaaz|21|CSE|
|102|Rahul|20|ECE|
|103|Priya|22|CSE|
|104|Neha|19|EEE|
|105|Amit|21|MECH|

---

## Course

| CourseID | CourseName |
|----------|------------|
|C101|DBMS|
|C102|OS|
|C103|CN|
|C104|DSA|

---

## Enrollment

| RollNo | CourseID |
|--------|----------|
|101|C101|
|101|C102|
|102|C101|
|103|C103|
|105|C104|

We'll use these

same tables

for almost

every SQL problem.

This makes SQL

much easier

to learn.

---

# 3. First SQL Query

Suppose

someone asks:

```text
Show every student.
```

The SQL query is:

```sql
SELECT *
FROM Student;
```

Looks scary?

Actually,

it is almost English.

Read it slowly.

```text
SELECT

↓

Give Me

↓

*

↓

Everything

↓

FROM

↓

Student Table
```

Meaning:

```text
Give me

everything

from

the Student table.
```

That's it.

---

## Output

| RollNo | Name | Age | Department |
|--------|------|-----|------------|
|101|Arbaaz|21|CSE|
|102|Rahul|20|ECE|
|103|Priya|22|CSE|
|104|Neha|19|EEE|
|105|Amit|21|MECH|

---

# 4. How Does DBMS Execute A Query?

Question:

What happens

after we press Enter?

Suppose we execute:

```sql
SELECT *
FROM Student;
```

The DBMS processes

the query

step by step.

### Step 1

Read:

```sql
FROM Student
```

Question:

Which table

should I search?

Answer:

```text
Student
```

The DBMS locates

the Student table.

---

### Step 2

Read:

```sql
SELECT *
```

Question:

What columns

are required?

The symbol:

```text
*
```

means:

```text
Everything.
```

So,

the DBMS selects

every column.

---

### Step 3

Return

the result.

```text
Student Table

↓

Output
```

Notice something.

SQL is **declarative**.

We tell

the DBMS:

```text
WHAT

we want.
```

We never tell it:

```text
HOW

to find it.
```

The DBMS

decides

the fastest way.

This is one of

the biggest ideas

in SQL.

---

## Interview Question

### Is SQL

Procedural

or

Declarative?

Answer:

```text
Declarative.
```

Because

we specify

what we want,

not

how to retrieve it.

---

# Problem 1

Display

every student.

```sql
SELECT *
FROM Student;
```

---

# Problem 2

Display

only

student names.

```sql
SELECT Name
FROM Student;
```

Output

| Name |
|------|
|Arbaaz|
|Rahul|
|Priya|
|Neha|
|Amit|

---

# Problem 3

Display

Roll Number

and

Department.

```sql
SELECT RollNo, Department
FROM Student;
```

---

# Problem 4

Display

Age

only.

```sql
SELECT Age
FROM Student;
```

---

# Common Mistakes

Wrong:

```sql
SELECT
FROM Student;
```

Reason:

No column

was selected.

---

Wrong:

```sql
SELECT Name Age
FROM Student;
```

Reason:

Comma missing.

Correct:

```sql
SELECT Name, Age
FROM Student;
```

---

# What We Learned

```text
SQL

↓

SELECT

↓

FROM

↓

*

↓

How DBMS

Executes

A Query
```

---

# Bridge

Until now,

we always

displayed

the entire table.

Question:

What if we want

only

CSE students?

Or

students

older than 20?

Or

students

whose name

is Rahul?

To filter rows,

SQL provides:

```sql
WHERE
```

The next topic

will teach us

how to search

for exactly

the data we want.


# 5. WHERE Clause

Until now,

we displayed

the entire table.

```sql
SELECT *
FROM Student;
```

Question:

What if

we need only

CSE students?

Or

only students

older than 20?

Displaying

the entire table

is unnecessary.

We need to

**filter** rows.

SQL provides:

```sql
WHERE
```

---

## Syntax

```sql
SELECT column_name
FROM table_name
WHERE condition;
```

Read it like English.

```text
SELECT

↓

Give Me

↓

These Columns

FROM

↓

This Table

WHERE

↓

This Condition

Is True
```

---

## Example Database

Student

| RollNo | Name | Age | Department |
|--------|------|-----|------------|
|101|Arbaaz|21|CSE|
|102|Rahul|20|ECE|
|103|Priya|22|CSE|
|104|Neha|19|EEE|
|105|Amit|21|MECH|

---

# Problem 1

Display

only CSE students.

Think first.

Question:

Which column

contains CSE?

```text
Department
```

Condition:

```text
Department = CSE
```

SQL:

```sql
SELECT *
FROM Student
WHERE Department = 'CSE';
```

Output

| RollNo | Name | Age | Department |
|--------|------|-----|------------|
|101|Arbaaz|21|CSE|
|103|Priya|22|CSE|

---

## Dry Run

DBMS checks

every row.

```text
Row 1

Department = CSE

↓

Yes

↓

Return Row
```

---

```text
Row 2

Department = ECE

↓

No

↓

Skip
```

---

```text
Row 3

Department = CSE

↓

Yes

↓

Return Row
```

The DBMS

continues

until

every row

has been checked.

---

# Problem 2

Display students

older than 20.

Question:

Which column?

```text
Age
```

Condition?

```text
Age > 20
```

SQL

```sql
SELECT *
FROM Student
WHERE Age > 20;
```

Output

| RollNo | Name | Age |
|--------|------|-----|
|101|Arbaaz|21|
|103|Priya|22|
|105|Amit|21|

---

# Problem 3

Display

Rahul's details.

Question:

How do we

identify Rahul?

Using Name.

```sql
SELECT *
FROM Student
WHERE Name = 'Rahul';
```

Output

| RollNo | Name | Age | Department |
|--------|------|-----|------------|
|102|Rahul|20|ECE|

---

# Problem 4

Display

only

Name

and

Department

of CSE students.

```sql
SELECT Name, Department
FROM Student
WHERE Department = 'CSE';
```

Output

| Name | Department |
|------|------------|
|Arbaaz|CSE|
|Priya|CSE|

Notice

WHERE filters rows.

SELECT chooses columns.

These are

two different jobs.

---

# Comparison

```sql
SELECT Name
FROM Student;
```

All rows

Only Name column.

---

```sql
SELECT *
FROM Student
WHERE Department='CSE';
```

All columns

Only CSE rows.

---

```sql
SELECT Name
FROM Student
WHERE Department='CSE';
```

Only Name column

Only CSE rows.

---

# Comparison Operators

| Operator | Meaning |
|----------|---------|
| = | Equal |
| > | Greater Than |
| < | Less Than |
| >= | Greater Than Or Equal |
| <= | Less Than Or Equal |
| != or <> | Not Equal |

---

# Problem 5

Students

younger than 21.

```sql
SELECT *
FROM Student
WHERE Age < 21;
```

---

# Problem 6

Students

whose age

is exactly 21.

```sql
SELECT *
FROM Student
WHERE Age = 21;
```

---

# Problem 7

Students

not in CSE.

```sql
SELECT *
FROM Student
WHERE Department != 'CSE';
```

or

```sql
SELECT *
FROM Student
WHERE Department <> 'CSE';
```

Both are valid.

---

# Common Mistakes

❌ Wrong

```sql
WHERE Department = CSE
```

Correct

```sql
WHERE Department = 'CSE'
```

Text values

must use

single quotes.

---

❌ Wrong

```sql
WHERE Age = '21'
```

Better

```sql
WHERE Age = 21
```

Numbers

do not require

quotes.

---

❌ Wrong

```sql
SELECT *
WHERE Age > 20
FROM Student;
```

Correct

```sql
SELECT *
FROM Student
WHERE Age > 20;
```

Remember the order.

```text
SELECT

↓

FROM

↓

WHERE
```

---

# Interview Questions

### What does

WHERE do?

```text
Filters

rows

that satisfy

a condition.
```

---

### Does WHERE

filter

rows

or columns?

```text
Rows
```

---

### Does SELECT

choose

rows

or columns?

```text
Columns
```

---

# Mini Challenge

Write SQL for:

### Q1

Display

only student names.

---

### Q2

Display

all students

whose department

is ECE.

---

### Q3

Display

students

older than 21.

---

### Q4

Display

Name

and Age

of students

whose age

is 20.

(Try solving these before looking at the next topic.)

---

# Bridge

Until now,

we filtered

using

one condition.

Question:

What if we need:

```text
CSE students

AND

Age > 20
```

Or

```text
CSE students

OR

ECE students
```

Or

students

whose age

is between

18 and 22.

SQL provides:

```sql
AND

OR

NOT

BETWEEN

IN

LIKE
```

These allow us

to build

more powerful

search conditions.

# 6. Building Complex Conditions

Until now,

we filtered using

only one condition.

Example:

```sql
SELECT *
FROM Student
WHERE Age > 20;
```

What if we need:

```text
Age > 20

AND

Department = CSE
```

SQL provides:

- AND
- OR
- NOT
- IN
- BETWEEN
- LIKE

---

## AND

All conditions

must be true.

```sql
SELECT *
FROM Student
WHERE Age > 20
AND Department='CSE';
```

---

### Problem

Display CSE students

older than 20.

---

## OR

At least one condition

must be true.

```sql
SELECT *
FROM Student
WHERE Department='CSE'
OR Department='ECE';
```

---

### Problem

Display students

from CSE or ECE.

---

## NOT

Reverses

a condition.

```sql
SELECT *
FROM Student
WHERE NOT Department='CSE';
```

---

### Problem

Display students

not in CSE.

---

## BETWEEN

Checks

a range.

Instead of

```sql
Age>=18
AND Age<=21
```

write

```sql
SELECT *
FROM Student
WHERE Age BETWEEN 18 AND 21;
```

---

### Problem

Students

between

18 and 21.

---

## IN

Checks

multiple values.

Instead of

```sql
Department='CSE'
OR Department='ECE'
OR Department='MECH'
```

write

```sql
SELECT *
FROM Student
WHERE Department IN ('CSE','ECE','MECH');
```

---

### Problem

Display

CSE, ECE

and MECH students.

---

## LIKE

Used for

pattern matching.

### Starts with A

```sql
SELECT *
FROM Student
WHERE Name LIKE 'A%';
```

---

### Ends with a

```sql
SELECT *
FROM Student
WHERE Name LIKE '%a';
```

---

### Contains ah

```sql
SELECT *
FROM Student
WHERE Name LIKE '%ah%';
```

---

## Wildcards

| Symbol | Meaning |
|---------|---------|
| % | Any number of characters |
| _ | Exactly one character |

---

## Practice

### Q1

Students

older than 20

from CSE.

---

### Q2

Students

whose name

starts with P.

---

### Q3

Students

from

CSE or ECE.

---

### Q4

Students

between

19 and 22.

---

### Q5

Students

not from MECH.

---

## Common Mistakes

❌

```sql
WHERE Age BETWEEN 18,21
```

✅

```sql
WHERE Age BETWEEN 18 AND 21
```

---

❌

```sql
LIKE 'A'
```

✅

```sql
LIKE 'A%'
```

---

# Bridge

Until now,

the DBMS

returned rows

in its own order.

Question:

What if we want

students

sorted by

Age

or

Name?

SQL provides:

```sql
ORDER BY
```

# 7. ORDER BY

Until now,

the DBMS returned

rows

in its own order.

Example:

| RollNo | Name | Age |
|--------|------|-----|
|101|Arbaaz|21|
|102|Rahul|20|
|103|Priya|22|
|104|Neha|19|

Question:

What if we want

students sorted

by Age

or

Name?

SQL provides:

```sql
ORDER BY
```

---

## Syntax

```sql
SELECT columns
FROM table
ORDER BY column;
```

Read it as:

```text
Give me

↓

These columns

↓

From this table

↓

Sorted by

this column
```

---

## Ascending Order (Default)

Smallest → Largest

```sql
SELECT *
FROM Student
ORDER BY Age;
```

Output

| Name | Age |
|------|-----|
|Neha|19|
|Rahul|20|
|Arbaaz|21|
|Priya|22|

---

## Descending Order

Largest → Smallest

```sql
SELECT *
FROM Student
ORDER BY Age DESC;
```

Output

| Name | Age |
|------|-----|
|Priya|22|
|Arbaaz|21|
|Rahul|20|
|Neha|19|

---

## ASC Keyword

Ascending

is the default.

These two queries

are identical.

```sql
SELECT *
FROM Student
ORDER BY Age;
```

```sql
SELECT *
FROM Student
ORDER BY Age ASC;
```

---

## Sorting By Name

```sql
SELECT *
FROM Student
ORDER BY Name;
```

Alphabetical order.

---

## Sorting By Multiple Columns

Question:

Suppose

two students

have the same age.

Sort by:

- Age
- Then Name

```sql
SELECT *
FROM Student
ORDER BY Age, Name;
```

DBMS first sorts

by Age.

If ages are equal,

it sorts

by Name.

---

# Practice

### Problem 1

Display students

sorted by Name.

```sql
SELECT *
FROM Student
ORDER BY Name;
```

---

### Problem 2

Display students

sorted by Age.

```sql
SELECT *
FROM Student
ORDER BY Age DESC;
```

---

### Problem 3

Display Name

and Department

sorted alphabetically.

```sql
SELECT Name, Department
FROM Student
ORDER BY Name;
```

---

### Problem 4

Sort by

Department,

then Name.

```sql
SELECT *
FROM Student
ORDER BY Department, Name;
```

---

## Common Mistakes

❌

```sql
ORDER Age BY
```

✅

```sql
ORDER BY Age
```

---

❌

```sql
ORDER BY DESC Age
```

✅

```sql
ORDER BY Age DESC
```

---

## Interview Questions

### Default sorting?

```text
Ascending

(ASC)
```

---

### Which keyword

sorts

Largest → Smallest?

```text
DESC
```

---

# Bridge

Until now,

we have:

- Selected columns.
- Filtered rows.
- Sorted results.

Question:

What if

the same value

appears

multiple times?

Example:

```text
Department

CSE

ECE

CSE

MECH

ECE
```

Can SQL

return only

unique values?

Yes.

Using:

```sql
DISTINCT
```

# 8. DISTINCT, LIMIT & Aggregate Functions

---

# DISTINCT

Question:

Suppose

our Student table

contains:

| Department |
|------------|
| CSE |
| ECE |
| CSE |
| MECH |
| ECE |

Question:

Can we display

only unique departments?

Yes.

Using:

```sql
SELECT DISTINCT Department
FROM Student;
```

Output

| Department |
|------------|
| CSE |
| ECE |
| MECH |

---

## When To Use DISTINCT?

Whenever you need

only unique values.

---

### Problem 1

Display all

unique departments.

```sql
SELECT DISTINCT Department
FROM Student;
```

---

### Problem 2

Display unique ages.

```sql
SELECT DISTINCT Age
FROM Student;
```

---

# LIMIT

Sometimes

we don't need

every row.

Suppose

the table contains

10,000 students.

Question:

Can we display

only the first 5?

Yes.

```sql
SELECT *
FROM Student
LIMIT 5;
```

Output

Only the first

5 rows.

---

### Problem 3

Display

first 3 students.

```sql
SELECT *
FROM Student
LIMIT 3;
```

---

### Problem 4

Display

first 2 names.

```sql
SELECT Name
FROM Student
LIMIT 2;
```

---

# Aggregate Functions

Aggregate Functions

perform calculations

on multiple rows

and return

one result.

The five most common

aggregate functions are:

```text
COUNT

SUM

AVG

MIN

MAX
```

---

# COUNT()

Counts

the number of rows.

```sql
SELECT COUNT(*)
FROM Student;
```

Suppose

there are

5 students.

Output

```text
5
```

---

### Problem

Count

all students.

```sql
SELECT COUNT(*)
FROM Student;
```

---

# SUM()

Adds

all values

of a column.

Example:

| Marks |
|-------|
|90|
|80|
|70|

```sql
SELECT SUM(Marks)
FROM Student;
```

Output

```text
240
```

---

### Problem

Find

total marks.

```sql
SELECT SUM(Marks)
FROM Student;
```

---

# AVG()

Returns

the average.

```sql
SELECT AVG(Age)
FROM Student;
```

Suppose

ages are

21,20,22,19

Average

```text
20.5
```

---

### Problem

Average age

of students.

```sql
SELECT AVG(Age)
FROM Student;
```

---

# MIN()

Returns

the smallest value.

```sql
SELECT MIN(Age)
FROM Student;
```

Output

```text
19
```

---

### Problem

Youngest student age.

```sql
SELECT MIN(Age)
FROM Student;
```

---

# MAX()

Returns

the largest value.

```sql
SELECT MAX(Age)
FROM Student;
```

Output

```text
22
```

---

### Problem

Oldest student age.

```sql
SELECT MAX(Age)
FROM Student;
```

---

# Summary

| Function | Purpose |
|----------|---------|
| COUNT() | Count rows |
| SUM() | Total |
| AVG() | Average |
| MIN() | Smallest value |
| MAX() | Largest value |

---

# Practice

### Q1

Count

all students.

---

### Q2

Find

average age.

---

### Q3

Find

highest age.

---

### Q4

Find

lowest age.

---

### Q5

Display

unique departments.

---

### Q6

Display

first

three students.

---

## Common Mistakes

❌

```sql
COUNT(Age)
```

counts

only

non-NULL ages.

If you want

every row,

use

```sql
COUNT(*)
```

---

❌

```sql
SELECT Name, AVG(Age)
FROM Student;
```

Invalid.

Why?

Because

AVG()

returns

one value,

while Name

returns

many rows.

We'll learn

how to combine them

using

```sql
GROUP BY
```

next.

---

## Interview Questions

### Which function

counts rows?

```text
COUNT()
```

---

### Which function

returns

the average?

```text
AVG()
```

---

### Which function

returns

the highest value?

```text
MAX()
```

---

### Difference

between

COUNT(*)

and

COUNT(column)?

```text
COUNT(*)

↓

Counts

every row.

--------------------

COUNT(column)

↓

Counts only

non-NULL values.
```

---

# Bridge

Suppose

we want:

```text
Average age

of

CSE students

separately,

ECE students

separately,

MECH students

separately.
```

Aggregate functions

alone

cannot do this.

We need

to divide

the rows

into groups.

SQL provides:

```sql
GROUP BY
```

This is one of

the most important

topics

in SQL.

# 9. GROUP BY & HAVING

Until now,

Aggregate Functions

returned

one result.

Example:

```sql
SELECT AVG(Age)
FROM Student;
```

Output

```text
20.6
```

Question:

What if we need

the average age

of

each department?

Example:

```text
CSE

↓

Average Age

ECE

↓

Average Age

MECH

↓

Average Age
```

One average

is not enough.

We need

one average

for each department.

This is exactly

what

```sql
GROUP BY
```

does.

---

## Example Table

| Name | Age | Department |
|------|-----|------------|
|Arbaaz|21|CSE|
|Rahul|20|ECE|
|Priya|22|CSE|
|Amit|21|MECH|
|Neha|19|ECE|

---

# GROUP BY

Think of GROUP BY as

first creating groups.

Example:

```sql
GROUP BY Department
```

DBMS internally creates:

```text
CSE

↓

Arbaaz

Priya

--------------------

ECE

↓

Rahul

Neha

--------------------

MECH

↓

Amit
```

Only after

creating groups

does it apply

aggregate functions.

---

## Example 1

Count students

in each department.

```sql
SELECT Department,
COUNT(*)
FROM Student
GROUP BY Department;
```

Output

| Department | COUNT |
|------------|-------|
|CSE|2|
|ECE|2|
|MECH|1|

---

## Dry Run

Step 1

Create Groups.

```text
CSE

↓

2 Students

-------------------

ECE

↓

2 Students

-------------------

MECH

↓

1 Student
```

Step 2

Apply COUNT()

to every group.

---

## Example 2

Average age

of each department.

```sql
SELECT Department,
AVG(Age)
FROM Student
GROUP BY Department;
```

Output

| Department | AVG(Age) |
|------------|-----------|
|CSE|21.5|
|ECE|19.5|
|MECH|21|

---

## Example 3

Maximum age

in every department.

```sql
SELECT Department,
MAX(Age)
FROM Student
GROUP BY Department;
```

---

# Why GROUP BY?

Without GROUP BY,

this query

is impossible.

```text
Average Age

Department Wise
```

GROUP BY divides

rows into groups.

Aggregate Functions

work

inside each group.

---

# HAVING

Question:

Suppose

we only want

departments

having

more than

one student.

Can we write

```sql
WHERE COUNT(*) > 1
```

No.

Because

WHERE

runs

before

GROUP BY.

COUNT()

doesn't exist yet.

Instead,

SQL provides

```sql
HAVING
```

---

## Rule

```text
WHERE

↓

Filters Rows

Before Grouping

-----------------------

HAVING

↓

Filters Groups

After Grouping
```

This is

the biggest difference.

---

## Example

Departments

having

more than

one student.

```sql
SELECT Department,
COUNT(*)
FROM Student
GROUP BY Department
HAVING COUNT(*) > 1;
```

Output

| Department | COUNT |
|------------|-------|
|CSE|2|
|ECE|2|

MECH

is removed

because

COUNT = 1.

---

## WHERE vs HAVING

Suppose

we need:

```text
Average Age

of

CSE Students.
```

Correct Query

```sql
SELECT Department,
AVG(Age)
FROM Student
WHERE Department='CSE'
GROUP BY Department;
```

Notice

WHERE removed

rows

before grouping.

---

Suppose

we need:

```text
Departments

having

Average Age > 20
```

Now

Average

is known

only after grouping.

So,

```sql
SELECT Department,
AVG(Age)
FROM Student
GROUP BY Department
HAVING AVG(Age) > 20;
```

---

# SQL Execution Order

Many students think

SQL runs

top to bottom.

Actually,

DBMS executes

roughly like this.

```text
FROM

↓

WHERE

↓

GROUP BY

↓

HAVING

↓

SELECT

↓

ORDER BY
```

Knowing this

explains

almost every SQL error.

---

# Problems

### Problem 1

Count students

in every department.

```sql
SELECT Department,
COUNT(*)
FROM Student
GROUP BY Department;
```

---

### Problem 2

Average age

of every department.

```sql
SELECT Department,
AVG(Age)
FROM Student
GROUP BY Department;
```

---

### Problem 3

Departments

having

more than

2 students.

```sql
SELECT Department,
COUNT(*)
FROM Student
GROUP BY Department
HAVING COUNT(*) > 2;
```

---

### Problem 4

Maximum age

of every department.

```sql
SELECT Department,
MAX(Age)
FROM Student
GROUP BY Department;
```

---

### Problem 5

Departments

whose average age

is greater than 20.

```sql
SELECT Department,
AVG(Age)
FROM Student
GROUP BY Department
HAVING AVG(Age) > 20;
```

---

## Common Mistakes

❌

```sql
WHERE COUNT(*) > 1
```

✅

```sql
HAVING COUNT(*) > 1
```

---

❌

```sql
SELECT Name,
AVG(Age)
FROM Student
GROUP BY Department;
```

Invalid.

Because

Name

is neither

grouped

nor aggregated.

---

## Interview Questions

### Difference

between

WHERE

and

HAVING?

| WHERE | HAVING |
|---------|---------|
| Filters Rows | Filters Groups |
| Before GROUP BY | After GROUP BY |

---

### Why is HAVING needed?

```text
To filter

groups

after

GROUP BY.
```

---

# Bridge

So far,

every query

used

only one table.

Question:

Suppose

Student details

are in one table

and

Course details

are in another.

How do we

combine them?

SQL provides:

```sql
JOIN
```

JOINS are

the heart

of SQL

and one of

the most frequently

asked interview topics.

# 10. JOINS

Until now,

every query

used only

one table.

Example:

Student

| RollNo | Name | Department |
|--------|------|------------|
|101|Arbaaz|CSE|
|102|Rahul|ECE|
|103|Priya|CSE|

Question:

Suppose we also have

another table.

Course

| RollNo | Course |
|--------|--------|
|101|DBMS|
|101|OS|
|102|CN|
|104|DSA|

Question:

Can the Student table

tell us

which course

Arbaaz studies?

```text
No.
```

The information

is stored

in another table.

So,

we need to

combine tables.

This is called:

```text
JOIN
```

---

## Why Do We Need JOINS?

Think of it like this.

Student table

knows:

```text
Who

the student is.
```

Course table

knows:

```text
Which course

the student studies.
```

To answer

questions like:

```text
Which course

does Arbaaz study?
```

we must

combine

both tables.

---

# INNER JOIN

The most common join.

It returns

only

matching rows.

---

## Syntax

```sql
SELECT *
FROM Student
INNER JOIN Course
ON Student.RollNo = Course.RollNo;
```

---

## Dry Run

Student

| RollNo | Name |
|--------|------|
|101|Arbaaz|
|102|Rahul|
|103|Priya|

Course

| RollNo | Course |
|--------|--------|
|101|DBMS|
|101|OS|
|102|CN|
|104|DSA|

DBMS checks

every Roll Number.

```text
101

↓

Found

↓

Join
```

---

```text
102

↓

Found

↓

Join
```

---

```text
103

↓

Not Found

↓

Skip
```

---

```text
104

↓

No Student

↓

Skip
```

---

Output

| Name | Course |
|------|--------|
|Arbaaz|DBMS|
|Arbaaz|OS|
|Rahul|CN|

Notice

only

matching rows

appear.

---

## LEFT JOIN

Question:

What if

we want

every student,

even if

they have

no course?

SQL provides

LEFT JOIN.

```sql
SELECT *
FROM Student
LEFT JOIN Course
ON Student.RollNo = Course.RollNo;
```

Output

| Name | Course |
|------|--------|
|Arbaaz|DBMS|
|Arbaaz|OS|
|Rahul|CN|
|Priya|NULL|

Notice

Priya

is still shown.

Because

LEFT JOIN

keeps

every row

from

the left table.

---

## RIGHT JOIN

Exactly opposite.

Keep

every row

from

the right table.

```sql
SELECT *
FROM Student
RIGHT JOIN Course
ON Student.RollNo=Course.RollNo;
```

Output

| Name | Course |
|------|--------|
|Arbaaz|DBMS|
|Arbaaz|OS|
|Rahul|CN|
|NULL|DSA|

Notice

Student 104

exists only

in Course.

So,

RIGHT JOIN

still returns it.

---

## FULL OUTER JOIN

Return

everything.

Matching rows.

Left-only rows.

Right-only rows.

```text
INNER

+

LEFT ONLY

+

RIGHT ONLY
```

---

## SELF JOIN

Question:

Can a table

join with itself?

Yes.

Example:

Employee

| EmpID | Name | ManagerID |
|------|------|-----------|
|1|Alice|NULL|
|2|Bob|1|
|3|Charlie|1|

Question:

Who manages Bob?

We join

Employee

with

Employee.

```sql
SELECT E.Name,
M.Name
FROM Employee E
JOIN Employee M
ON E.ManagerID=M.EmpID;
```

Output

| Employee | Manager |
|----------|---------|
|Bob|Alice|
|Charlie|Alice|

---

## CROSS JOIN

Returns

every possible

combination.

Student

3 rows.

Course

4 rows.

Result

```text
3 × 4

=

12 rows
```

No matching

is required.

---

# Summary

| Join | Returns |
|------|----------|
| INNER | Matching rows only |
| LEFT | All left + matching |
| RIGHT | All right + matching |
| FULL | Everything |
| SELF | Same table joins itself |
| CROSS | Every combination |

---

# Practice

### Problem 1

Display

Student Name

and

Course.

(Hint: INNER JOIN)

---

### Problem 2

Display

every student,

even if

they haven't

joined

any course.

(Hint: LEFT JOIN)

---

### Problem 3

Display

every course,

even if

no student

studies it.

(Hint: RIGHT JOIN)

---

### Problem 4

Display

employee

and

manager names.

(Hint: SELF JOIN)

---

### Problem 5

Find

every possible

Student-Course

combination.

(Hint: CROSS JOIN)

---

# Common Mistakes

❌

Joining

without

ON condition.

Produces

incorrect results.

---

❌

Using

INNER JOIN

when

LEFT JOIN

is required.

Remember

INNER JOIN

removes

non-matching rows.

---

# Interview Questions

### Most common join?

```text
INNER JOIN
```

---

### Which join

returns

all rows

from

the left table?

```text
LEFT JOIN
```

---

### Which join

returns

every possible

combination?

```text
CROSS JOIN
```

---

# Mental Trick

```text
INNER

↓

Only Friends

-------------------

LEFT

↓

Take Everyone

From Left

-------------------

RIGHT

↓

Take Everyone

From Right

-------------------

FULL

↓

Take Everyone

-------------------

SELF

↓

Meet Yourself

-------------------

CROSS

↓

Everyone Meets Everyone
```

---

# Bridge

So far,

every query

was written

using

one SELECT statement.

Question:

Can one query

contain

another query?

Example:

```text
Find students

whose age

is greater

than

the average age.
```

To solve this,

SQL provides:

```text
Subqueries
```

# 11. Subqueries

Until now,

we wrote

one SQL query.

Example:

```sql
SELECT *
FROM Student;
```

Question:

Can a query

contain

another query?

```text
Yes.
```

This is called a:

```text
Subquery
```

---

## What Is A Subquery?

A Subquery is:

```text
A query

inside

another query.
```

General Form:

```sql
SELECT ...
FROM ...
WHERE column = (
    SELECT ...
);
```

The inner query

executes first.

Its result

is used

by the outer query.

---

## Example

Student

| Name | Age |
|------|-----|
|Arbaaz|21|
|Rahul|20|
|Priya|22|
|Neha|19|

Question:

Display students

whose age

is greater than

the average age.

---

### Step 1

Find average age.

```sql
SELECT AVG(Age)
FROM Student;
```

Suppose Output:

```text
20.5
```

---

### Step 2

Use it

inside another query.

```sql
SELECT *
FROM Student
WHERE Age >
(
    SELECT AVG(Age)
    FROM Student
);
```

Output

| Name | Age |
|------|-----|
|Arbaaz|21|
|Priya|22|

---

## Dry Run

Inner Query

↓

```sql
SELECT AVG(Age)
```

↓

20.5

Outer Query

↓

```sql
Age > 20.5
```

↓

Return matching rows.

---

## Practice

### Problem 1

Students

older than

average age.

---

### Problem 2

Students

having

maximum age.

```sql
SELECT *
FROM Student
WHERE Age =
(
SELECT MAX(Age)
FROM Student
);
```

---

# 12. IN, EXISTS, ANY & ALL

Suppose

we need

students

from:

```text
CSE

ECE

MECH
```

Instead of

```sql
Department='CSE'
OR Department='ECE'
OR Department='MECH'
```

write

```sql
SELECT *
FROM Student
WHERE Department
IN ('CSE','ECE','MECH');
```

---

## IN With Subquery

Display students

whose Roll Number

exists

in Enrollment.

```sql
SELECT *
FROM Student
WHERE RollNo IN
(
SELECT RollNo
FROM Enrollment
);
```

---

# 10. EXISTS

Question:

Does at least

one matching row

exist?

If yes,

return TRUE.

---

Example

Display students

who enrolled

in at least

one course.

```sql
SELECT *
FROM Student S
WHERE EXISTS
(
SELECT *
FROM Enrollment E
WHERE S.RollNo=E.RollNo
);
```

Think of EXISTS as:

```text
Does

at least one

matching row

exist?
```

---

# 11. ANY

ANY means:

```text
Compare with

any one

value

returned

by the subquery.
```

Example

Students older than

any student

in ECE.

```sql
SELECT *
FROM Student
WHERE Age > ANY
(
SELECT Age
FROM Student
WHERE Department='ECE'
);
```

---

# 12. ALL

ALL means:

```text
Compare with

every value

returned.
```

Example

Students older than

all students

in ECE.

```sql
SELECT *
FROM Student
WHERE Age > ALL
(
SELECT Age
FROM Student
WHERE Department='ECE'
);
```

---

## ANY vs ALL

| ANY | ALL |
|------|------|
| At least one value | Every value |

---

## Practice

### Q1

Students

older than

average age.

---

### Q2

Students

having

maximum age.

---

### Q3

Students

whose RollNo

exists

in Enrollment.

---

### Q4

Difference

between

IN

and

EXISTS.

---

## Interview Questions

### What is a Subquery?

```text
A query

inside

another query.
```

---

### Which executes first?

```text
Inner Query
```

---

### Difference

between

ANY

and

ALL?

```text
ANY

↓

At least one

-------------------

ALL

↓

Every value
```

---

# Bridge

Until now,

every query

worked directly

on tables.

Question:

Can we create

a virtual table

whose result

comes from

another query?

Yes.

This is called

a:

```text
View
```

# 13. Views

Suppose

you frequently run

this query.

```sql
SELECT Name, Department
FROM Student;
```

Instead of writing

the query

again and again,

you can save it.

That saved query

is called a:

```text
View
```

---

## Definition

A View is:

```text
A virtual table

created

from

an SQL query.
```

It does **not**

store data itself.

It stores

only

the SQL query.

---

## Syntax

```sql
CREATE VIEW StudentInfo AS
SELECT Name, Department
FROM Student;
```

Now,

instead of writing

the full query,

simply write:

```sql
SELECT *
FROM StudentInfo;
```

---

## Why Use Views?

- Simpler queries.
- Better security.
- Hide unnecessary columns.
- Reuse common queries.

---

## Interview Question

### Does a View

store data?

```text
No.

It stores

the query,

not the data.
```

---

# 14. Stored Procedures

Suppose

a company

uses

the same SQL

every day.

Instead of writing

the SQL repeatedly,

it can be stored

inside the DBMS.

This is called

a:

```text
Stored Procedure
```

---

## Definition

A Stored Procedure is:

```text
A collection

of SQL statements

stored

inside

the database.
```

---

## Example

```sql
CREATE PROCEDURE GetStudents()
BEGIN
    SELECT *
    FROM Student;
END;
```

Execute:

```sql
CALL GetStudents();
```

---

## Why Use Procedures?

- Reuse code.
- Faster execution.
- Easier maintenance.
- Centralized business logic.

---

# 15. Functions

A Function

is similar to

a Stored Procedure,

but

it returns

a value.

---

## Example

```sql
CREATE FUNCTION Square(x INT)
RETURNS INT
RETURN x * x;
```

Use it:

```sql
SELECT Square(5);
```

Output

```text
25
```

---

## Procedure vs Function

| Procedure | Function |
|-----------|----------|
| May return nothing | Must return a value |
| Called using CALL | Used inside SQL expressions |

---

# 16. Triggers

Question:

Can the database

automatically perform

an action

when data changes?

```text
Yes.
```

This is called

a:

```text
Trigger
```

---

## Definition

A Trigger is:

```text
SQL code

that automatically

executes

when an event occurs.
```

Events include:

- INSERT
- UPDATE
- DELETE

---

## Example

Suppose

every time

a student

is deleted,

we want

to save

that information

in another table.

Instead of relying

on the programmer,

the DBMS

can do it

automatically

using a Trigger.

---

## Example

```sql
CREATE TRIGGER LogDelete
AFTER DELETE
ON Student
FOR EACH ROW
BEGIN
    INSERT INTO StudentLog
    VALUES (OLD.RollNo);
END;
```

---

## Why Use Triggers?

- Automatic logging.
- Data validation.
- Auditing.
- Business rules.

---

# Practice

### Q1

What is

a View?

---

### Q2

Difference

between

Procedure

and

Function.

---

### Q3

When are

Triggers executed?

---

### Q4

Name three

Trigger events.

---

# Common Interview Questions

### What is SQL?

```text
Structured Query Language

used to communicate

with

a relational database.
```

---

### Difference

between

WHERE

and

HAVING?

```text
WHERE

↓

Filters Rows

--------------------

HAVING

↓

Filters Groups
```

---

### Which Join

returns

matching rows only?

```text
INNER JOIN
```

---

### Which clause

sorts data?

```text
ORDER BY
```

---

### Which clause

creates groups?

```text
GROUP BY
```

---

### Does a View

store data?

```text
No.
```

---

### Difference

between

Procedure

and

Function?

```text
Procedure

↓

May not return

a value

--------------------

Function

↓

Must return

a value
```

---

# Final Mental Model

```text
SQL

│

├── SELECT

├── WHERE

├── AND / OR / NOT

├── BETWEEN

├── IN

├── LIKE

├── ORDER BY

├── DISTINCT

├── LIMIT

├── Aggregate Functions

│      ├── COUNT

│      ├── SUM

│      ├── AVG

│      ├── MIN

│      └── MAX

├── GROUP BY

├── HAVING

├── JOINS

│      ├── INNER

│      ├── LEFT

│      ├── RIGHT

│      ├── FULL

│      ├── SELF

│      └── CROSS

├── Subqueries

├── EXISTS

├── ANY

├── ALL

├── Views

├── Stored Procedures

├── Functions

└── Triggers
```

---

# DBMS04 Complete ✅

## What You Learned

- SQL Basics
- SELECT
- WHERE
- AND / OR / NOT
- BETWEEN
- IN
- LIKE
- ORDER BY
- DISTINCT
- LIMIT
- Aggregate Functions
- GROUP BY
- HAVING
- JOINS
- Subqueries
- EXISTS
- ANY
- ALL
- Views
- Stored Procedures
- Functions
- Triggers

## Bridge To DBMS05

Until now,

we learned

how to

create,

retrieve,

update,

and query data.

The next question is:

```text
How should

we design

our tables

to avoid

duplicate data,

wasted storage,

and update problems?
```

The answer is:

```text
Functional Dependencies

&

Normalization
```

This is one of the **highest-weightage DBMS topics** in placements and interviews.
