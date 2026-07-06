# DBMS 05 - Functional Dependencies & Normalization

# Navigation

- [1. Why Do We Need Normalization?](#1-why-do-we-need-normalization)
  - [Data Redundancy](#what-is-data-redundancy)
  - [Why Is Redundancy Bad?](#why-is-redundancy-bad)
  - [Advantages of Normalization](#advantages)
  - [Interview Questions](#interview-questions)

- [2. Data Anomalies](#2-data-anomalies)
  - [Update Anomaly](#21-update-anomaly)
  - [Insert Anomaly](#22-insert-anomaly)
  - [Delete Anomaly](#23-delete-anomaly)
  - [Root Cause of Anomalies](#why-do-these-happen)
  - [Solution](#the-solution)
  - [Interview Questions](#interview-questions-1)
  - [Mental Model](#mental-model)

- [3. Functional Dependencies](#4-what-is-a-functional-dependency)
  - [Main Idea](#the-main-idea)
  - [Formal Definition](#formal-definition)
  - [Determinant](#determinant)
  - [Dependent Attribute](#dependent-attribute)
  - [One Attribute → Many Attributes](#can-one-attribute)
  - [Many Attributes → One Attribute](#can-many-attributes)
  - [Important Rule](#important-rule)
  - [How to Identify an FD](#how-to-identify)
  - [Examples](#examples)
  - [Common Mistakes](#common-mistakes)
  - [Interview Questions](#interview-questions-2)
  - [Mental Model](#mental-model-1)

- [4. Types of Functional Dependencies](#5-types-of-functional-dependencies)
  - [Full Functional Dependency](#51-full-functional-dependency)
  - [Partial Functional Dependency](#52-partial-functional-dependency)
  - [Trivial Functional Dependency](#53-trivial-functional-dependency)
  - [Non-Trivial Functional Dependency](#54-non-trivial-functional-dependency)
  - [Completely Non-Trivial Dependency](#55-completely-non-trivial-dependency)
  - [Transitive Dependency](#56-transitive-dependency)
  - [Summary Table](#summary-table)
  - [Relationship with Normalization](#how-they-relate-to-normalization)
  - [Interview Questions](#interview-questions-3)
  - [Mental Model](#mental-model-2)


# 1. Why Do We Need Normalization?

Before learning

Normalization,

let's understand

the problem

it tries to solve.

---

## Suppose We Design A Student Table

| StudentID | Name | Department | HOD |
|-----------|------|------------|-----|
|101|Arbaaz|CSE|Dr. Rao|
|102|Rahul|CSE|Dr. Rao|
|103|Priya|ECE|Dr. Sharma|
|104|Amit|CSE|Dr. Rao|

Looks perfectly fine.

Question:

Can you spot

any problem?

Not immediately.

---

## Look Carefully

The HOD

of CSE is:

```text
Dr. Rao
```

Notice

how many times

it appears.

| StudentID | Department | HOD |
|-----------|------------|-----|
|101|CSE|Dr. Rao|
|102|CSE|Dr. Rao|
|104|CSE|Dr. Rao|

The same information

is stored

again

and

again.

This is called

```text
Data Redundancy
```

---

## What Is Data Redundancy?

Data Redundancy means

storing

the same information

multiple times.

Example:

```text
Student 101

↓

CSE

↓

Dr. Rao

----------------

Student 102

↓

CSE

↓

Dr. Rao

----------------

Student 104

↓

CSE

↓

Dr. Rao
```

Instead of storing

"Dr. Rao"

once,

we stored it

three times.

---

## Why Is Redundancy Bad?

Suppose

the HOD changes.

```text
Dr. Rao

↓

Dr. Kumar
```

How many rows

must we update?

```text
3
```

If there are

1000 students,

we must update

1000 rows.

---

## Another Problem

Suppose

we update

only

two rows.

| StudentID | Department | HOD |
|-----------|------------|-----|
|101|CSE|Dr. Kumar|
|102|CSE|Dr. Kumar|
|104|CSE|Dr. Rao|

Now,

who is

the real HOD

of CSE?

```text
We don't know.
```

The database

has become

inconsistent.

---

## The Root Cause

The problem

is not

the update.

The real problem is:

```text
One fact

is stored

many times.
```

Whenever

the same fact

appears repeatedly,

future updates

become difficult.

---

## Can We Store It Better?

Instead of

this:

Student

| StudentID | Name | Department | HOD |
|-----------|------|------------|-----|
|101|Arbaaz|CSE|Dr. Rao|
|102|Rahul|CSE|Dr. Rao|
|103|Priya|ECE|Dr. Sharma|

Split it

into two tables.

### Student

| StudentID | Name | Department |
|-----------|------|------------|
|101|Arbaaz|CSE|
|102|Rahul|CSE|
|103|Priya|ECE|

---

### Department

| Department | HOD |
|------------|-----|
|CSE|Dr. Rao|
|ECE|Dr. Sharma|

Now,

the HOD

is stored

only once.

---

## Advantages

Changing

the HOD

requires

updating

only one row.

No duplication.

Less storage.

Less chance

of mistakes.

---

## This Process Is Called

```text
Normalization
```

Normalization means:

```text
Organizing data

into smaller,

well-structured tables

to reduce

redundancy

and avoid anomalies.
```

Notice:

Normalization

does **not**

remove data.

It only

stores it

more efficiently.

---

## Important Point

Normalization

is **not**

about making

more tables.

It is about

removing

unnecessary duplication

while preserving

all information.

Sometimes

one table

is already perfect.

Sometimes

it should be split.

Normalization

helps us decide.

---

## Interview Questions

### Why do we need Normalization?

```text
To reduce

data redundancy

and

avoid anomalies.
```

---

### What is Data Redundancy?

```text
Storing

the same information

multiple times.
```

---

### Does Normalization

remove data?

```text
No.

It reorganizes

the data

without losing

information.
```

---
# 2. Data Anomalies

In the previous topic,

we learned that

redundant data

causes problems.

Question:

What kind of problems?

There are exactly

three major problems.

They are called:

```text
1. Update Anomaly

2. Insert Anomaly

3. Delete Anomaly
```

Every normalization technique

tries to remove

these anomalies.

---

# Example Table

Suppose

our database stores

student information.

| StudentID | Name | Department | HOD |
|-----------|------|------------|-----------|
|101|Arbaaz|CSE|Dr. Rao|
|102|Rahul|CSE|Dr. Rao|
|103|Priya|ECE|Dr. Sharma|
|104|Amit|CSE|Dr. Rao|

Notice

the HOD

of CSE

appears

three times.

This redundancy

causes

all three anomalies.

---

# 2.1 Update Anomaly

Suppose

Dr. Rao retires.

The new HOD becomes

Dr. Kumar.

Question:

How many rows

must be updated?

| StudentID | Department | HOD |
|-----------|------------|-----------|
|101|CSE|Dr. Rao|
|102|CSE|Dr. Rao|
|104|CSE|Dr. Rao|

Every row

must be updated.

---

Suppose

the programmer

accidentally

forgets

one row.

| StudentID | Department | HOD |
|-----------|------------|-----------|
|101|CSE|Dr. Kumar|
|102|CSE|Dr. Kumar|
|104|CSE|Dr. Rao|

Question:

Who is

the actual HOD?

```text
Dr. Kumar?

or

Dr. Rao?
```

The database

contains

contradictory information.

This is called

```text
Update Anomaly.
```

---

## Definition

Update Anomaly means

the same information

must be updated

in multiple places.

If even

one update

is missed,

the database

becomes inconsistent.

---

# Real World Example

Suppose

Amazon stores

Seller Address

inside

every order.

Seller

has completed

100,000 orders.

Now

the seller

changes address.

Question:

How many rows

must Amazon update?

```text
100,000
```

One missed row

creates

incorrect data.

---

# 2.2 Insert Anomaly

Suppose

a new department

starts.

Department

```text
AI
```

New HOD

```text
Dr. Reddy
```

Question:

Can we insert

this information?

Current table:

| StudentID | Name | Department | HOD |
|-----------|------|------------|-----|

Notice

there is

no student.

StudentID

is mandatory.

Name

is mandatory.

Question:

Where will we store

the department?

We cannot.

Because

our table

stores

students,

not departments.

So,

until

the first student joins,

we cannot

store

the new department.

Information

is lost.

This is called

```text
Insert Anomaly.
```

---

## Definition

Insert Anomaly means

we cannot insert

one piece of information

without requiring

another,

unrelated piece

of information.

---

# Another Example

Hospital

stores

Doctor

and

Patient

in one table.

A new doctor joins.

No patients yet.

Can we insert

the doctor?

No.

The design

doesn't allow it.

Again,

Insert Anomaly.

---

# 2.3 Delete Anomaly

Suppose

ECE

has

only

one student.

| StudentID | Name | Department | HOD |
|-----------|------|------------|------------|
|103|Priya|ECE|Dr. Sharma|

Now

Priya graduates.

We delete

the row.

```sql
DELETE
FROM Student
WHERE StudentID=103;
```

Table becomes

| StudentID | Name | Department | HOD |
|-----------|------|------------|-----|
|101|Arbaaz|CSE|Dr. Rao|
|102|Rahul|CSE|Dr. Rao|

Question:

What happened

to

ECE?

```text
Gone.
```

What happened

to

Dr. Sharma?

```text
Gone.
```

We wanted

to delete

a student.

Instead,

we accidentally

deleted

department information

too.

This is called

```text
Delete Anomaly.
```

---

## Definition

Delete Anomaly means

deleting one record

accidentally removes

other important information.

---

# Why Do These Happen?

Question:

Why do all

three anomalies

exist?

Answer:

Because

multiple facts

are stored

inside

one table.

Our table

contains

two different facts.

Fact 1

```text
Student

↓

Department
```

Fact 2

```text
Department

↓

HOD
```

Both are mixed

into

one table.

That is

the real problem.

---

# The Solution

Instead of

one table,

split it.

Student

| StudentID | Name | Department |
|-----------|------|------------|
|101|Arbaaz|CSE|
|102|Rahul|CSE|
|103|Priya|ECE|

Department

| Department | HOD |
|------------|------------|
|CSE|Dr. Rao|
|ECE|Dr. Sharma|

Now

changing

the HOD

requires

only one update.

---

Adding

a department

doesn't require

a student.

---

Deleting

the last student

doesn't remove

the department.

---

All three anomalies

disappear.

---

# Interview Questions

## Why does Update Anomaly occur?

```text
Because

the same information

is stored

multiple times.
```

---

## Why does Insert Anomaly occur?

```text
Because

unrelated information

is forced

into

one table.
```

---

## Why does Delete Anomaly occur?

```text
Deleting one record

also deletes

other useful information.
```

---

## Root Cause Of Every Anomaly

```text
Redundant Data

↓

Multiple Facts

Inside One Table

↓

Update Anomaly

Insert Anomaly

Delete Anomaly
```

---

# Mental Model

Think of a notebook.

Suppose

you write

your friend's phone number

on

every page.

If the number changes,

you must erase

every page.

If one page

is forgotten,

the notebook

contains

two different numbers.

The notebook

is inconsistent.

Normalization

simply says:

```text
Don't repeat

the same fact

again

and

again.
```

Store it

once,

and refer to it

whenever needed.

---

# Bridge

Question:

How does a database

know

which information

depends

on

which?

For example,

how does it know

that

```text
StudentID

↓

determines

Student Name?
```

Or

```text
Department

↓

determines

HOD?
```

This idea

is called

```text
Functional Dependency.
```

Everything

in Normalization

is built

on Functional Dependencies.

# 4. What Is A Functional Dependency?

Until now,

we learned:

```text
Redundant Data

↓

Data Anomalies

↓

Normalization
```

Question:

How does the database

know

which information

is repeated

unnecessarily?

Or,

how does it know

which columns

should be

placed together

and which

should be separated?

The answer is:

```text
Functional Dependency
```

Everything

in Normalization

starts here.

---

# The Main Idea

Suppose

we have

this table.

| StudentID | Name | Department |
|-----------|------|------------|
|101|Arbaaz|CSE|
|102|Rahul|ECE|
|103|Priya|CSE|
|104|Amit|MECH|

Question:

If I tell you

the StudentID

is

101,

can you tell me

the student's name?

```text
Yes.

Arbaaz.
```

Can you tell me

the department?

```text
Yes.

CSE.
```

Interesting.

Knowing

StudentID

was enough

to determine

both

Name

and

Department.

We write this as

```text
StudentID

↓

determines

↓

Name
```

or

mathematically,

```text
StudentID → Name
```

Similarly,

```text
StudentID → Department
```

This is called

a

Functional Dependency.

---

# What Does

"Determines"

Actually Mean?

This is where

most students

get confused.

It does **not** mean

StudentID

calculates

the Name.

It simply means:

```text
If two rows

have the same StudentID,

they MUST

have the same Name.
```

That is all.

Nothing more.

---

Example

Suppose

StudentID

is

101.

Question:

Can Name

sometimes be

Arbaaz

and sometimes

Rahul?

No.

That would mean

the same student

has

two different names.

Impossible.

Therefore,

StudentID

uniquely determines

Name.

---

# Another Example

Employee Table

| EmpID | Name | Salary |
|------|------|--------|
|1|Alice|50000|
|2|Bob|60000|
|3|Charlie|70000|

Question:

If I know

EmpID = 2,

can I determine

Salary?

```text
Yes.

60000.
```

Can I determine

Name?

```text
Yes.
```

Therefore,

```text
EmpID → Name

EmpID → Salary
```

---

# Real-Life Analogy

Suppose

someone tells you

their

Aadhaar Number.

Can you identify

the person?

```text
Yes.
```

Now suppose

they tell you

only

their first name.

Example

```text
Rahul
```

Question:

Can you identify

the person?

```text
No.
```

Because

there are

millions

of Rahuls.

So

Aadhaar Number

determines

Person.

But

Name

does not

determine

Person.

Exactly the same

idea

exists

inside databases.

---

# Formal Definition

A Functional Dependency

is a relationship

between attributes.

If

knowing

attribute X

is enough

to uniquely determine

attribute Y,

then

we say

```text
X → Y
```

Read it as

```text
X

functionally determines

Y.
```

or simply

```text
Y depends on X.
```

---

# Determinant

The left side

of

```text
X → Y
```

is called

the

Determinant.

Example

```text
StudentID → Name
```

Determinant

is

```text
StudentID
```

because

it determines

everything

on the right.

---

# Dependent Attribute

The right side

is called

the

Dependent Attribute.

Example

```text
StudentID → Department
```

Department

depends on

StudentID.

---

# Can One Attribute

Determine Many?

Yes.

Example

```text
StudentID

↓

Name

↓

Department

↓

Age

↓

Phone
```

We simply write

```text
StudentID

↓

Name, Department, Age, Phone
```

or

```text
StudentID

→

Name, Department, Age, Phone
```

One attribute

can determine

many attributes.

---

# Can Many Attributes

Determine One?

Yes.

Suppose

Marks table.

| StudentID | Subject | Marks |
|-----------|---------|-------|
|101|DBMS|90|
|101|OS|80|
|102|DBMS|85|

Question:

Can

StudentID

alone

determine

Marks?

No.

Student 101

has

90

and

80.

Question:

Can

Subject

alone

determine

Marks?

No.

DBMS

has

90

and

85.

Now,

combine them.

```text
(StudentID, Subject)
```

Now,

there is

exactly

one Marks value.

Therefore,

```text
(StudentID, Subject)

↓

Marks
```

or

```text
(StudentID, Subject)

→

Marks
```

Notice

sometimes

one attribute

is not enough.

Multiple attributes

together

become

the determinant.

---

# Important Rule

A Functional Dependency

does **not**

come from

the current data.

It comes from

the meaning

of the data.

Example

Current table

| RollNo | Name |
|--------|------|
|101|Arbaaz|
|102|Rahul|

Question:

Right now,

every Name

is unique.

Can we say

```text
Name → RollNo
```

No.

Tomorrow,

another student

named

Arbaaz

may join.

Then

the dependency

breaks.

So,

Functional Dependencies

are decided

by

real-world rules,

not by

today's sample data.

This is one of

the most important

interview points.

---

# How To Identify

A Functional Dependency

Ask yourself

one question.

```text
If I know

the value

of X,

can I always

know

the value

of Y?
```

If

Yes,

then

```text
X → Y
```

If

No,

then

no Functional Dependency

exists.

---

# Examples

## Example 1

StudentID

↓

Name

```text
Yes.
```

Functional Dependency exists.

---

## Example 2

Department

↓

HOD

Suppose

every department

has exactly

one HOD.

```text
Department → HOD
```

Valid.

---

## Example 3

Name

↓

Department

Can

Name

determine

Department?

```text
No.
```

Many students

can have

the same name.

No Functional Dependency.

---

## Example 4

Pincode

↓

City

Suppose

every pincode

belongs

to exactly

one city.

```text
Pincode → City
```

Valid.

Question:

Can

City

determine

Pincode?

No.

One city

has many pincodes.

---

# Common Mistakes

❌

Thinking

```text
X → Y
```

means

X causes Y.

No.

It only means

Y can be

uniquely identified

using X.

---

❌

Thinking

Functional Dependencies

depend on

sample data.

They don't.

They depend on

business rules.

---

❌

Thinking

every unique column

must be

a determinant.

Only if

it is guaranteed

by the application,

not by coincidence.

---

# Interview Questions

## What is a Functional Dependency?

```text
A relationship

where

one attribute

(or set of attributes)

uniquely determines

another attribute.
```

---

## What does

X → Y

mean?

```text
Knowing X

is sufficient

to uniquely determine

Y.
```

---

## Does

Functional Dependency

come from

the sample data?

```text
No.

It comes

from

real-world rules

and business logic.
```

---

## Can multiple attributes

determine

one attribute?

```text
Yes.

Example:

(StudentID, Subject)

↓

Marks
```

---

# Mental Model

Think of

a locker.

Every locker

has

one key.

If someone

gives you

the key,

you can open

exactly

one locker.

```text
Key

↓

Locker
```

The key

determines

the locker.

But

the locker

cannot determine

the key.

Functional Dependency

works

exactly

the same way.

One value

uniquely identifies

another value.

---

# Bridge

Now we know

what a

Functional Dependency is.

But not all

Functional Dependencies

are the same.

Some are

simple.

Some are

partial.

Some are

transitive.

These different types

are exactly

what create

1NF,

2NF,

3NF,

and BCNF.

So next,

we'll study

the **Types of Functional Dependencies**.

# 5. Types Of Functional Dependencies

In the previous topic,

we learned

what a

Functional Dependency (FD)

is.

Example:

```text
StudentID → Name
```

But not every

Functional Dependency

is the same.

Some are perfectly fine.

Some create redundancy.

Some are the reason

why

2NF

or

3NF

exists.

Understanding these

is the key

to understanding

Normalization.

---

# The Types

We will study

six types.

```text
1. Full Functional Dependency

2. Partial Functional Dependency

3. Trivial Functional Dependency

4. Non-Trivial Functional Dependency

5. Completely Non-Trivial Functional Dependency

6. Transitive Dependency
```

Do not memorize them.

Instead,

understand

what problem

each one describes.

---

# Before We Start

Consider

this table.

| StudentID | Subject | Marks |
|-----------|----------|-------|
|101|DBMS|90|
|101|OS|80|
|102|DBMS|85|

Question:

Can

StudentID

determine

Marks?

```text
No.
```

Student 101

has

two marks.

---

Can

Subject

determine

Marks?

```text
No.
```

DBMS

has

90

and

85.

---

Now combine

StudentID

and

Subject.

```text
(StudentID, Subject)
```

Question:

Can they determine

Marks?

```text
Yes.
```

Every

Student-Subject pair

has exactly

one Marks value.

Therefore

```text
(StudentID, Subject)

↓

Marks
```

Keep this table

in mind.

We'll use it

throughout.

---

# 5.1 Full Functional Dependency

Suppose

our dependency is

```text
(StudentID, Subject)

↓

Marks
```

Question:

Can we remove

StudentID

and still

determine Marks?

No.

---

Can we remove

Subject

and still

determine Marks?

No.

---

Both attributes

are required.

Neither can be removed.

This is called

```text
Full Functional Dependency
```

---

## Definition

An attribute

is Fully Functionally Dependent

if it depends

on

the entire determinant,

not just

part of it.

---

## Visual

```text
(StudentID, Subject)

↓

Marks

✔ Needs BOTH
```

---

## Example

Employee Project Table

| EmpID | ProjectID | Hours |
|-------|-----------|-------|
|1|101|20|
|1|102|15|
|2|101|25|

Question:

Can EmpID

alone

determine Hours?

No.

---

Can ProjectID

alone

determine Hours?

No.

---

Need both.

```text
(EmpID, ProjectID)

↓

Hours
```

Full Dependency.

---

# Why Is It Good?

Because

every attribute

on the left

is actually needed.

Nothing is redundant.

---

# 5.2 Partial Functional Dependency

Now consider

another table.

| StudentID | Subject | StudentName |
|-----------|----------|------------|
|101|DBMS|Arbaaz|
|101|OS|Arbaaz|
|102|DBMS|Rahul|

Suppose

the key is

```text
(StudentID, Subject)
```

Question:

Does StudentName

depend on

both?

Think carefully.

---

If I know

StudentID

alone,

can I find

StudentName?

```text
Yes.
```

StudentID

101

always means

Arbaaz.

---

Does Subject

matter?

No.

StudentName

doesn't change

because

the subject changes.

---

So the dependency

is actually

```text
StudentID

↓

StudentName
```

NOT

```text
(StudentID, Subject)

↓

StudentName
```

The second attribute

is unnecessary.

This is called

```text
Partial Functional Dependency
```

---

## Definition

A Partial Dependency

exists when

an attribute

depends

on only

part

of a composite key.

---

## Visual

```text
(StudentID, Subject)

↓

StudentName

Actually

↓

StudentID

↓

StudentName
```

Subject

contributes nothing.

---

# Why Is Partial Dependency Bad?

Suppose

Arbaaz

changes

his name.

Question:

How many rows

must be updated?

Every subject

he studies.

Again,

redundancy appears.

This is exactly

what

2NF

will remove.

---

# Interview Trick

Partial Dependency

can exist

only when

the determinant

contains

multiple attributes.

If the key is

just

StudentID,

Partial Dependency

is impossible.

---

# 5.3 Trivial Functional Dependency

Suppose

I write

```text
StudentID

↓

StudentID
```

Question:

True?

Of course.

Knowing StudentID

always tells us

StudentID.

---

Another example

```text
(StudentID, Name)

↓

StudentID
```

Again,

true.

The right side

already exists

inside

the left side.

This is called

a

Trivial Dependency.

---

## Rule

If

the right side

is already

part of

the left side,

the dependency

is trivial.

---

Examples

```text
A → A

(A,B) → A

(A,B,C) → B
```

All are

Trivial.

---

# Why Study This?

Mostly

for theory.

Trivial Dependencies

never create

redundancy.

---

# 5.4 Non-Trivial Functional Dependency

Suppose

```text
StudentID

↓

Department
```

Question:

Is Department

already present

on the left?

No.

Therefore,

this is

Non-Trivial.

---

## Rule

Right side

must contain

at least

one attribute

not present

on the left.

---

Examples

```text
StudentID

↓

Department
```

```text
EmpID

↓

Salary
```

Both

are Non-Trivial.

---

# 5.5 Completely Non-Trivial Dependency

Suppose

```text
(A,B)

↓

(C,D)
```

Notice

the two sides

share

nothing.

Left

contains

A,B.

Right

contains

C,D.

No overlap.

This is called

Completely Non-Trivial.

---

Example

```text
StudentID

↓

Department
```

StudentID

and

Department

share nothing.

So

it is also

Completely

Non-Trivial.

---

# 5.6 Transitive Dependency

This one

is

VERY IMPORTANT.

It is

the entire reason

3NF exists.

---

Suppose

Student table.

| StudentID | Department | HOD |
|-----------|------------|-----------|
|101|CSE|Dr. Rao|
|102|ECE|Dr. Sharma|

We know

```text
StudentID

↓

Department
```

Also

```text
Department

↓

HOD
```

Question:

Can StudentID

determine HOD?

Yes.

Through Department.

```text
StudentID

↓

Department

↓

HOD
```

Notice

StudentID

doesn't directly

determine HOD.

It determines

Department,

which then

determines HOD.

This is called

```text
Transitive Dependency
```

---

## Visual

```text
StudentID

↓

Department

↓

HOD
```

---

## Real Life Example

Suppose

EmployeeID

determines

Department.

Department

determines

Department Head.

Then

EmployeeID

indirectly

determines

Department Head.

Exactly

the same idea.

---

# Why Is It Bad?

Suppose

CSE

gets

a new HOD.

Question:

How many rows

must change?

Every student

in CSE.

Redundancy

returns.

This is exactly

what

3NF

will remove.

---

# Summary Table

| Dependency | Meaning |
|------------|---------|
| Full | Depends on the whole key |
| Partial | Depends on only part of a composite key |
| Trivial | Right side already exists on the left |
| Non-Trivial | Right side has new attributes |
| Completely Non-Trivial | Left and right share nothing |
| Transitive | Depends indirectly through another attribute |

---

# How They Relate To Normalization

This is the part

students usually miss.

```text
Functional Dependency

↓

Find Partial Dependency

↓

Remove It

↓

2NF

---------------------

Functional Dependency

↓

Find Transitive Dependency

↓

Remove It

↓

3NF
```

Notice

2NF

and

3NF

are not

new ideas.

They are simply

rules

for removing

specific types

of dependencies.

---

# Interview Questions

## What is Full Functional Dependency?

Depends

on the

entire determinant.

---

## When can Partial Dependency occur?

Only

when

the key

is composite.

---

## Which Normal Form removes

Partial Dependency?

```text
2NF
```

---

## Which Normal Form removes

Transitive Dependency?

```text
3NF
```

---

## Which dependency

causes

3NF violations?

```text
Transitive Dependency.
```

---

# Mental Model

Imagine

a company.

```text
EmployeeID

↓

Department

↓

Manager
```

You don't ask

EmployeeID

for the Manager.

You ask

the Department,

which knows

its Manager.

That's exactly

a Transitive Dependency.

One fact

is reached

through another fact.

---

# Bridge

So far,

we know

what dependencies

exist.

Question:

How do we

discover

new Functional Dependencies

from existing ones?

For example,

if we know

```text
A → B

B → C
```

Can we conclude

```text
A → C ?
```

Yes.

The rules

used to derive

new dependencies

are called:

```text
Armstrong's Axioms
```

These are the mathematical foundation of normalization.
