# DBMS 05 - Functional Dependencies & Normalization

---

## Navigation 

- [1. Why Do We Need Normalization?](#1-why-do-we-need-normalization)
- [2. Data Anomalies](#2-data-anomalies)
  - [2.1 Update Anomaly](#21-update-anomaly)
  - [2.2 Insert Anomaly](#22-insert-anomaly)
  - [2.3 Delete Anomaly](#23-delete-anomaly)
- [3. Functional Dependency](#4-what-is-a-functional-dependency)
- [4. Types of Functional Dependencies](#5-types-of-functional-dependencies)
  - [4.1 Full Functional Dependency](#51-full-functional-dependency)
  - [4.2 Partial Functional Dependency](#52-partial-functional-dependency)
  - [4.3 Trivial Functional Dependency](#53-trivial-functional-dependency)
  - [4.4 Non-Trivial Functional Dependency](#54-non-trivial-functional-dependency)
  - [4.5 Completely Non-Trivial Functional Dependency](#55-completely-non-trivial-dependency)
  - [4.6 Transitive Dependency](#56-transitive-dependency)
- [5. Armstrong's Axioms](#6-armstrongs-axioms)
  - [5.1 Reflexivity Rule](#61-reflexivity-rule)
  - [5.2 Augmentation Rule](#62-augmentation-rule)
  - [5.3 Transitivity Rule](#63-transitivity-rule)
- [6. Attribute Closure](#7-attribute-closure)

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

# 6. Armstrong's Axioms

In the previous chapter,

we learned

Functional Dependencies.

Example

```text
StudentID → Name
```

Question:

Suppose

we already know

some Functional Dependencies.

Can we discover

new Functional Dependencies

without

looking at

the data again?

```text
Yes.
```

The rules

used to derive

new Functional Dependencies

are called

```text
Armstrong's Axioms.
```

---

# Why Do We Need Them?

Suppose

we know

```text
A → B
```

Question:

Can we conclude

```text
A → A
```

What about

```text
A → AB
```

What about

```text
A → C
```

How do we know

which conclusions

are correct?

We need

a set of rules.

Those rules

are

Armstrong's Axioms.

---

# Think Of Them Like Mathematics

Example

Suppose

we know

```text
2 = 2
```

Then we can safely say

```text
2 + 1 = 2 + 1
```

Nobody questions that.

It follows

from

a mathematical rule.

Similarly,

Functional Dependencies

also have

logical rules.

---

# The Three Basic Axioms

Everything

is built

using

only three rules.

```text
1. Reflexivity

2. Augmentation

3. Transitivity
```

All other rules

are derived

from these three.

---

# 6.1 Reflexivity Rule

Rule

```text
If

Y ⊆ X

then

X → Y
```

Looks scary.

Let's make it simple.

---

Suppose

```text
(StudentID, Name)
```

Question:

Does knowing

(StudentID, Name)

allow us

to know

StudentID?

Obviously.

Yes.

StudentID

is already present.

---

Example

```text
(A,B)

↓

A
```

Valid.

---

Another

```text
(A,B,C)

↓

(B,C)
```

Also valid.

---

General Rule

If

the right side

already exists

inside

the left side,

the dependency

is always true.

This is exactly

the

Trivial Dependency

we studied earlier.

---

Examples

```text
A → A
```

```text
(A,B) → A
```

```text
(A,B,C) → C
```

```text
(A,B,C) → (A,C)
```

All valid.

---

# Why Is It Called Reflexivity?

Think

of a mirror.

A mirror

reflects

itself.

Similarly,

a set of attributes

always

determines itself.

---

# 6.2 Augmentation Rule

Suppose

we know

```text
StudentID

↓

Name
```

Question

Can we safely add

Department

to both sides?

```text
(StudentID, Department)

↓

(Name, Department)
```

Yes.

Nothing becomes false.

This is called

Augmentation.

---

General Rule

If

```text
X → Y
```

then

```text
XZ → YZ
```

where

Z

can be

anything.

---

Example

We know

```text
EmpID → Salary
```

Add

Department

to both sides.

```text
(EmpID, Department)

↓

(Salary, Department)
```

Still true.

---

Think Like This

Suppose

you know

someone's

Aadhaar Number

can identify

the person.

Adding

their favourite colour

doesn't destroy

that relationship.

You simply know

more information.

---

# Important Point

You must

add

the same attribute

to both sides.

Correct

```text
A → B

↓

AC → BC
```

Wrong

```text
A → B

↓

AC → B
```

This is NOT

Augmentation.

---

# 6.3 Transitivity Rule

This is

the most famous one.

Suppose

we know

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

Question

Can StudentID

determine HOD?

Of course.

If

StudentID

gives Department,

and Department

gives HOD,

then

StudentID

indirectly

gives HOD.

So,

```text
StudentID

↓

HOD
```

This is

Transitivity.

---

General Rule

If

```text
X → Y

and

Y → Z
```

then

```text
X → Z
```

---

Another Example

Suppose

```text
RollNo → StudentID

StudentID → Name
```

Then

```text
RollNo → Name
```

---

Real-Life Example

Suppose

PIN Code

↓

City

City

↓

State

Then

PIN Code

↓

State

You don't

need

another lookup.

---

# Why Is It Important?

Remember

Transitive Dependency?

```text
StudentID

↓

Department

↓

HOD
```

That concept

came directly

from this rule.

---

# Derived Rules

Books often

teach

more rules.

Actually,

they are

derived

from

the three axioms.

---

## Union Rule

Suppose

```text
A → B

A → C
```

Then

```text
A → BC
```

Example

```text
StudentID

↓

Name

StudentID

↓

Department
```

Therefore

```text
StudentID

↓

(Name, Department)
```

---

## Decomposition Rule

Suppose

```text
A → BC
```

Then

```text
A → B
```

and

```text
A → C
```

Example

```text
StudentID

↓

(Name, Department)
```

Means

```text
StudentID → Name

StudentID → Department
```

---

## Pseudo-Transitivity

Looks scary.

Actually

very easy.

Suppose

```text
A → B

and

BC → D
```

Then

```text
AC → D
```

You rarely

need this

for interviews,

but know

it exists.

---

# Summary Table

| Rule | Meaning |
|------|---------|
| Reflexivity | Attributes determine themselves |
| Augmentation | Add same attributes to both sides |
| Transitivity | Chain dependencies |
| Union | Combine dependencies |
| Decomposition | Split dependencies |
| Pseudo-Transitivity | Extended transitivity |

---

# Do We Memorize Them?

No.

Understand

the logic.

Most interviewers

care more about

whether you can

apply them

than

recite them.

---

# Interview Questions

## What are Armstrong's Axioms?

```text
Rules

used to derive

new Functional Dependencies

from existing ones.
```

---

## Name the three basic axioms.

```text
Reflexivity

Augmentation

Transitivity
```

---

## Which three are derived rules?

```text
Union

Decomposition

Pseudo-Transitivity
```

---

## Which axiom says

if

A → B

and

B → C

then

A → C?

```text
Transitivity
```

---

# Mental Model

Imagine

a treasure map.

You already know

three roads.

```text
Road 1

Road 2

Road 3
```

Armstrong's Axioms

are simply

the rules

that let you

combine roads

to discover

new routes

without

exploring

the entire map again.

That's exactly

what we do

with Functional Dependencies.

---

# Bridge

Now we know

how to derive

new Functional Dependencies.

Question:

Suppose

we have

10 Functional Dependencies.

How do we know

everything

that

one attribute

can determine?

Example

Given

```text
A → B

B → C

C → D
```

Question

What can

A

determine?

Finding

all possible attributes

is called

```text
Attribute Closure.
```

Attribute Closure

is one of

the most frequently asked

DBMS interview problems.

# 7. Attribute Closure

So far,

we have learned

Functional Dependencies.

Example

```text
StudentID → Name

StudentID → Department

Department → HOD
```

Question:

If I know

only

StudentID,

what information

can I determine?

Can I determine

Name?

Yes.

Department?

Yes.

HOD?

Yes.

How did we know that?

We followed

the Functional Dependencies.

The collection

of every attribute

that can be determined

from a given attribute set

is called

```text
Attribute Closure.
```

---

# What Is Attribute Closure?

Suppose

we have

the following Functional Dependencies.

```text
A → B

B → C

C → D
```

Question:

If someone gives you

A,

what else

can you determine?

Step 1

```text
A
```

Using

```text
A → B
```

Now we know

```text
A,B
```

Using

```text
B → C
```

Now we know

```text
A,B,C
```

Using

```text
C → D
```

Now we know

```text
A,B,C,D
```

Therefore,

the closure of A is

```text
A⁺ = {A,B,C,D}
```

Read it as

```text
A Plus
```

or

```text
Closure of A
```

---

# Definition

The Attribute Closure

of an attribute set X

is

```text
Every attribute

that can be

functionally determined

using

the given

Functional Dependencies.
```

---

# Why Do We Need Attribute Closure?

Question:

Why are we

finding closures?

Because they help us

answer questions like:

```text
Can this attribute

determine

the whole table?
```

If yes,

it is probably

a Candidate Key.

We'll study that

next.

So,

Closure is

the tool.

Candidate Key

is

the application.

---

# The Closure Algorithm

This is the only algorithm

you need.

Suppose

we need

A⁺.

### Step 1

Start

with

A itself.

```text
A⁺

=

{A}
```

---

### Step 2

Look at

every Functional Dependency.

Whenever

the left side

is already

inside the closure,

add

the right side.

---

### Step 3

Repeat

until

nothing new

can be added.

Done.

That final set

is

the closure.

---

# Example 1

FDs

```text
A → B

B → C

C → D
```

Find

A⁺.

---

Start

```text
A⁺={A}
```

---

A → B

```text
A⁺={A,B}
```

---

B → C

```text
A⁺={A,B,C}
```

---

C → D

```text
A⁺={A,B,C,D}
```

Nothing more.

Final Answer

```text
A⁺={A,B,C,D}
```

---

# Example 2

FDs

```text
A → B

A → C

C → D
```

Find

A⁺.

Start

```text
{A}
```

---

Apply

A → B

```text
{A,B}
```

---

Apply

A → C

```text
{A,B,C}
```

---

Apply

C → D

```text
{A,B,C,D}
```

Done.

---

# Example 3

FDs

```text
A → B

B → C

CD → E

E → F
```

Find

A⁺.

---

Start

```text
{A}
```

---

A → B

```text
{A,B}
```

---

B → C

```text
{A,B,C}
```

---

Can we use

CD → E?

No.

We have

C

but

not D.

Cannot apply.

---

Can we use

E → F?

No.

No E yet.

---

Nothing more.

Answer

```text
A⁺={A,B,C}
```

Notice

just because

an FD exists,

doesn't mean

we can use it.

The entire

left side

must already

be available.

---

# Example 4

FDs

```text
AB → C

C → D

D → E
```

Find

A⁺.

Start

```text
{A}
```

Question:

Can we use

AB → C?

No.

Missing B.

Nothing happens.

Final

```text
A⁺={A}
```

---

Now find

(AB)⁺.

Start

```text
{A,B}
```

AB → C

```text
{A,B,C}
```

C → D

```text
{A,B,C,D}
```

D → E

```text
{A,B,C,D,E}
```

Done.

Notice

the difference.

One missing attribute

prevented

the chain

from starting.

---

# Common Mistake

Students often think

if they have

half

the left side,

they can use

the dependency.

Wrong.

Suppose

```text
AB → C
```

Having only

A

does NOT

allow

C.

Having only

B

does NOT

allow

C.

Need

both.

---

# Another Example

FDs

```text
RollNo → StudentID

StudentID → Department

Department → HOD
```

Find

RollNo⁺.

Start

```text
{RollNo}
```

↓

```text
StudentID
```

↓

```text
Department
```

↓

```text
HOD
```

Answer

```text
{RollNo,
StudentID,
Department,
HOD}
```

---

# Shortcut

Think of

each Functional Dependency

as

a door.

Question:

Can I open

this door?

Only if

I already possess

every key

on the left side.

Open the door,

collect

new attributes,

then try

opening

more doors.

Eventually,

no doors

can be opened.

That is

the closure.

---

# Practice 1

FDs

```text
A → B

B → C

C → D

D → E
```

Find

```text
A⁺
```

Answer

```text
{A,B,C,D,E}
```

---

# Practice 2

FDs

```text
AB → C

C → D

A → E
```

Find

```text
A⁺
```

Solution

```text
Start

↓

{A}

↓

A→E

↓

{A,E}

↓

Stop
```

Cannot use

AB→C

because

B

is missing.

Final

```text
{A,E}
```

---

# Practice 3

FDs

```text
A → BC

C → D

D → E
```

Find

A⁺.

Start

```text
{A}
```

↓

```text
{A,B,C}
```

↓

```text
{A,B,C,D}
```

↓

```text
{A,B,C,D,E}
```

Done.

---

# Why Interviewers Love This Topic

Because

it tests

whether

you truly understand

Functional Dependencies.

Almost every

Candidate Key

question

starts with

computing closures.

---

# Interview Questions

## What is Attribute Closure?

```text
The set

of all attributes

that can be determined

from a given

attribute set

using

Functional Dependencies.
```

---

## Why is Attribute Closure important?

```text
To find

Candidate Keys,

Super Keys,

and verify

Functional Dependencies.
```

---

## When does the algorithm stop?

```text
When

no new attributes

can be added

to the closure.
```

---

# Mental Model
# Functional Dependencies → Normalization (Interview Flow)

## Problem

A college stores marks in one table.

| StudentID | Subject | StudentName | Department | HOD | Marks |
|-----------|---------|-------------|------------|-----|-------|
|101|DBMS|Arbaaz|CSE|Dr. Rao|95|
|101|OS|Arbaaz|CSE|Dr. Rao|90|
|102|DBMS|Rahul|ECE|Dr. Sharma|80|

We want to remove redundancy.

---

# Step 1: Business Rules

The college tells us:

```text
One StudentID belongs to one student.

One student belongs to one department.

One department has one HOD.

Marks are stored per Student and Subject.
```

These are real-world facts.

---

# Step 2: Convert into Functional Dependencies

```text
StudentID → StudentName

StudentID → Department

Department → HOD

(StudentID, Subject) → Marks
```

Now the database understands the business.

---

# Step 3: Armstrong's Axioms

Question:

Can StudentID determine HOD?

Nobody explicitly wrote:

```text
StudentID → HOD
```

But

```text
StudentID → Department

Department → HOD
```

Using Transitivity:

```text
StudentID → HOD
```

We have discovered a hidden FD.

---

# Step 4: Attribute Closure

Compute

```text
(StudentID)+
```

Start:

```text
{StudentID}
```

Apply FDs:

```text
StudentID
↓

StudentName

↓

Department

↓

HOD
```

Result:

```text
StudentID+

=

{StudentID,
StudentName,
Department,
HOD}
```

Notice:

Marks is missing because Subject is required.

---

Now compute

```text
(StudentID, Subject)+
```

Result:

```text
{
StudentID,
Subject,
StudentName,
Department,
HOD,
Marks
}
```

Now every attribute is present.

Therefore

```text
(StudentID, Subject)
```

is the Candidate Key.

---

# Step 5: Check 2NF

Candidate Key

```text
(StudentID, Subject)
```

Check every FD.

```text
StudentID → StudentName
```

StudentID is only PART of the Candidate Key.

↓

Partial Dependency

❌ 2NF violated

---

```text
StudentID → Department
```

Again

↓

Partial Dependency

❌ 2NF violated

---

Fix

Split the table.

```text
Student

StudentID
StudentName
Department
```

```text
Marks

StudentID
Subject
Marks
```

---

# Step 6: Check 3NF

Now look at

```text
Student

StudentID
StudentName
Department
HOD
```

FDs

```text
StudentID → Department

Department → HOD
```

Notice

```text
StudentID
      ↓
Department
      ↓
HOD
```

HOD depends on another Non-Key attribute.

↓

Transitive Dependency

❌ 3NF violated

Fix

```text
Student

StudentID
StudentName
Department
```

```text
Department

Department
HOD
```

---

# Step 7: BCNF

BCNF needs a different type of example.

Suppose

```text
Student
Course
Teacher
```

FDs

```text
(Student, Course) → Teacher

Teacher → Course
```

Candidate Key

```text
(Student, Course)
```

Check

```text
Teacher → Course
```

Question:

Is Teacher a Candidate Key?

No.

❌ BCNF violated.

Even though this satisfies 3NF.

---

# Final Flow

```text
Business Rules
        ↓
Functional Dependencies
        ↓
Armstrong's Axioms
        ↓
Attribute Closure
        ↓
Candidate Keys
        ↓
Check every FD

Part of Key
      ↓
Non-Key
→ 2NF

Non-Key
      ↓
Non-Key
→ 3NF

Determinant
NOT Candidate Key
→ BCNF
```

# Interview Summary

```text
Business Rules
↓
Describe the real world.

FDs
↓
Describe the rules mathematically.

Armstrong
↓
Find hidden dependencies.

Closure
↓
Find Candidate Keys.

Candidate Keys
↓
Check every Functional Dependency.

Partial Dependency
↓
2NF

Transitive Dependency
↓
3NF

Determinant not a Candidate Key
↓
BCNF
```
# Formal Rules of Normalization (Interview)

## 2NF (Second Normal Form)

A relation is in **2NF** if:

- It is already in **1NF**.
- **No non-prime (non-key) attribute depends on a proper subset of any Candidate Key.**

### Rule

```text
Part of Candidate Key
          ↓
Non-Prime Attribute

❌ Not Allowed
```

---

## 3NF (Third Normal Form)

A relation is in **3NF** if:

- It is already in **2NF**.
- For **every Functional Dependency** `X → A`, **at least one** of the following is true:

```text
1. X is a Super Key

OR

2. A is a Prime Attribute
```

### Rule

```text
For every FD X → A

Is X a Super Key?

YES → OK

NO

↓

Is A a Prime Attribute?

YES → OK

NO

↓

❌ 3NF Violation
```

---

## BCNF (Boyce-Codd Normal Form)

A relation is in **BCNF** if:

- For **every Functional Dependency** `X → A`,

```text
X must be a Super Key.
```

### Rule

```text
For every FD X → A

Is X a Super Key?

YES → OK

NO

↓

❌ BCNF Violation
```

---

# Quick Revision

```text
2NF
---------
Part of Candidate Key
        ↓
Non-Prime
❌

3NF
---------
For every FD X → A

X is Super Key
      OR
A is Prime
✅

BCNF
---------
For every FD X → A

X must be
a Super Key

No exceptions.
```

# Normalization Interview Checklist

## Step 1
Write all Functional Dependencies.

## Step 2
Find all Candidate Keys using Attribute Closure.

## Step 3
Identify

- Prime Attributes
- Non-Prime Attributes

## Step 4
Check 2NF

Rule:

No Non-Prime Attribute should depend on part of a Candidate Key.

Violation?

↓

Split the table.

## Step 5
Check 3NF

For every FD X → A

- X is a Super Key
OR
- A is a Prime Attribute

Otherwise

↓

Split the table.

## Step 6
Check BCNF

For every FD X → A

X must be a Super Key.

Otherwise

↓

Split the table based on that FD.

```text
YES. ✅

That's the core idea of normalization.

More precisely:

✅ The Functional Dependency causing the violation is moved into a new table.
✅ The remaining attributes stay in another table.
✅ The determinant (left side of the FD) is kept in both tables to act as the common key (join attribute).

Question 1

A → B and B → C, where B is a non-key attribute. Do we create another table where B is basically the Candidate Key?

YES. ✅

That's exactly the idea.

Example:

StudentID → Department

Department → HOD

Split into:

Student
---------
StudentID (PK)
Department
Department
----------
Department (PK)
HOD

Here, Department becomes the Candidate Key (and Primary Key) of the new Department table. ✅

Question 2

If B → C, C is Prime, and it violates BCNF, what do we do?

Exactly the same thing. ✅

BCNF doesn't care whether C is Prime or Non-Prime.

It only checks:

Is B a Super Key?

If No:

❌ BCNF violation.

Split:

Table1
-------
B
C

where B becomes the key of this new table.

The remaining attributes stay in another table containing B as the common attribute.

Rule to remember 🌟

Whenever an FD

X → Y

causes a violation:

Always create

Table1
-------
X   ← Key
Y

and

Table2
-------
Remaining attributes
+
X   ← Common attribute for joining

So the decomposition process is the same for 2NF, 3NF, and BCNF. The only difference is which FD is considered a violation.

```
