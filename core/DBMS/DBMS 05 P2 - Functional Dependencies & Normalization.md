# DBMS 05 P2 - Functional Dependencies & NormalizationF

---

## Navigation

- [1. Candidate Keys](#1-candidate-keys)
  - [Candidate Key vs Super Key](#candidate-key-vs-super-key)
  - [Primary Key](#primary-key)
  - [How To Find A Candidate Key?](#how-to-find-a-candidate-key)
- [2. Minimal Cover](#2-minimal-cover)
  - [Why Do We Need A Minimal Cover?](#why-do-we-need-a-minimal-cover)
  - [Properties Of A Minimal Cover](#properties-of-a-minimal-cover)
  - [The Algorithm](#the-algorithm)
- [3. First Normal Form (1NF)](#3-first-normal-form-1nf)
- [4. Second Normal Form (2NF)](#4-second-normal-form-2nf)
  - [How To Check 2NF](#how-to-check-2nf)
- [5. Third Normal Form (3NF)](#5-third-normal-form-3nf)
  - [How To Detect 3NF](#how-to-detect-3nf)
- [6. Boyce-Codd Normal Form (BCNF)](#6-boyce-codd-normal-form-bcnf)
  - [Difference Between 3NF and BCNF](#difference-between-3nf-and-bcnf)
  - [2NF vs 3NF vs BCNF](#2nf-vs-3nf-vs-bcnf)
- [7. Fourth Normal Form (4NF) – Overview](#7-fourth-normal-form-4nf---overview)
- [8. Fifth Normal Form (5NF) – Overview](#8-fifth-normal-form-5nf---overview)
- [9. Denormalization](#9-denormalization)
- [Complete Revision](#complete-revision)
  - [Normal Forms](#normal-forms)
  - [Dependency Revision](#dependency-revision)
  - [Key Revision](#key-revision)
  - [Entire Chapter Mental Model](#entire-chapter-mental-model)
  - [Interview Cheat Sheet](#interview-cheat-sheet)

# 1. Candidate Keys

So far,

we have learned

- Functional Dependency
- Armstrong's Axioms
- Attribute Closure

Question:

Why did we learn

all these topics?

Because they help us answer

one important question.

```text
Can this attribute

(or set of attributes)

uniquely identify

every row

in the table?
```

If yes,

then it may be

a Candidate Key.

---

# Let's Start With A Simple Table

Student

| StudentID | Name | Department | Age |
|-----------|------|------------|-----|
|101|Arbaaz|CSE|21|
|102|Rahul|ECE|20|
|103|Priya|CSE|22|

Suppose

the Functional Dependencies are

```text
StudentID → Name

StudentID → Department

StudentID → Age
```

Question:

If I know

StudentID,

what can I determine?

Answer

```text
Name

Department

Age
```

Along with

StudentID itself,

we now know

every attribute.

Therefore,

```text
StudentID⁺

=

{StudentID,
Name,
Department,
Age}
```

Notice something.

The closure contains

every attribute

of the relation.

That means

StudentID

can uniquely identify

every row.

StudentID

is a Candidate Key.

---

# Formal Definition

A Candidate Key

is

```text
The smallest

set of attributes

whose closure

contains

every attribute

of the relation.
```

Two conditions

must always hold.

---

## Condition 1

It must determine

every attribute.

Suppose

our relation is

```text
R(A,B,C,D)
```

If

```text
A⁺

=

{A,B,C,D}
```

then

A satisfies

Condition 1.

---

## Condition 2

It must be

minimal.

This is

the most important part.

---

# What Does Minimal Mean?

Suppose

```text
A

↓

B,C,D
```

Question:

Can

AB

also determine

everything?

Of course.

Because

A alone

already could.

Then

AB

is not minimal.

It contains

an unnecessary attribute.

Therefore,

AB

is NOT

a Candidate Key.

---

Example

Suppose

```text
A⁺

=

{A,B,C,D}
```

Question

Is

```text
AB
```

a Candidate Key?

No.

Because

A alone

already identifies

everything.

B contributes

nothing.

---

# Candidate Key vs Super Key

This is

one of

the most asked

interview questions.

Suppose

A

is a Candidate Key.

Question:

Can

AB

identify

every row?

Yes.

Because

A already could.

Question:

Can

ABC

identify

every row?

Also yes.

Question:

Can

ABCD

identify

every row?

Still yes.

These are called

Super Keys.

---

## Definition

A Super Key

is

```text
Any set

of attributes

that uniquely identifies

every row.
```

Some Super Keys

contain

extra attributes.

Candidate Keys

never do.

---

# Visual

Suppose

A

is enough.

```text
A

↓

Candidate Key
```

Now add

B.

```text
AB

↓

Super Key
```

Add

C.

```text
ABC

↓

Super Key
```

Add

D.

```text
ABCD

↓

Super Key
```

Only

the smallest

one

is the Candidate Key.

---

# Real-Life Example

Suppose

your Aadhaar Number

uniquely identifies you.

Question

Can

```text
(Aadhaar Number)
```

identify you?

Yes.

Candidate Key.

---

Now consider

```text
(Aadhaar Number,
Phone Number)
```

Can it identify you?

Yes.

But

Phone Number

was unnecessary.

Therefore,

it is

only

a Super Key.

---

# Primary Key

Question:

If there are

multiple Candidate Keys,

which one

becomes

the Primary Key?

Answer:

The database designer

chooses

one Candidate Key

as

the Primary Key.

---

Example

Employee

| EmpID | Email | Phone |
|-------|-------|-------|

Suppose

both

EmpID

and

Email

are unique.

Then

```text
EmpID

↓

Candidate Key
```

```text
Email

↓

Candidate Key
```

Question

Can both

be Primary Keys?

No.

A table

has

only one

Primary Key.

The remaining

Candidate Keys

become

Alternate Keys.

---

# Relationship Between Keys

```text
Super Keys

↓

Many

-------------------

Candidate Keys

↓

Minimal Super Keys

-------------------

Primary Key

↓

One chosen

Candidate Key

-------------------

Alternate Keys

↓

Remaining

Candidate Keys
```

---

# How To Find A Candidate Key?

We use

Attribute Closure.

Suppose

Relation

```text
R(A,B,C,D)
```

FDs

```text
A → B

B → C

C → D
```

Question

Find Candidate Key.

---

Step 1

Compute

A⁺.

Start

```text
{A}
```

↓

A→B

```text
{A,B}
```

↓

B→C

```text
{A,B,C}
```

↓

C→D

```text
{A,B,C,D}
```

Closure

contains

every attribute.

Question

Can we remove

anything

from A?

No.

Single attribute.

Therefore,

```text
A

↓

Candidate Key
```

---

# Example 2

Relation

```text
R(A,B,C)
```

FDs

```text
AB → C
```

Find

Candidate Key.

---

Try

A⁺

```text
{A}
```

Nothing happens.

Not enough.

---

Try

B⁺

```text
{B}
```

Nothing.

---

Try

AB⁺

Start

```text
{A,B}
```

↓

AB→C

```text
{A,B,C}
```

All attributes.

Question

Can A

be removed?

No.

Question

Can B

be removed?

No.

Therefore,

```text
AB

↓

Candidate Key
```

---

# Example 3

Relation

```text
R(A,B,C,D)
```

FDs

```text
A → B

C → D
```

Question

Find Candidate Key.

---

Try

A⁺

```text
{A,B}
```

Missing

C,D.

---

Try

C⁺

```text
{C,D}
```

Missing

A,B.

---

Try

AC⁺

```text
{A,C}

↓

A→B

↓

{A,B,C}

↓

C→D

↓

{A,B,C,D}
```

All attributes.

Question

Can A

be removed?

No.

Can C

be removed?

No.

Therefore

```text
AC

↓

Candidate Key
```

---

# Shortcut

Whenever solving

Candidate Key questions,

always remember.

```text
Closure

first.

Minimality

second.
```

Students

often forget

the second step.

---

# Common Mistakes

❌

Finding

a Super Key

and calling it

a Candidate Key.

---

❌

Checking only

whether

closure

contains

all attributes.

You must

also verify

minimality.

---

❌

Ignoring

composite keys.

Sometimes

one attribute

is never enough.

---

# Interview Questions

## What is

a Candidate Key?

```text
The smallest

set of attributes

whose closure

contains

every attribute

of the relation.
```

---

## Difference

between

Super Key

and

Candidate Key?

| Super Key | Candidate Key |
|------------|---------------|
| Identifies every row | Identifies every row |
| May contain extra attributes | Minimal |
| Many possible | One or more |

---

## Can a table

have multiple

Candidate Keys?

```text
Yes.
```

---

## Can a table

have multiple

Primary Keys?

```text
No.
```

Only one

Primary Key

can be chosen.

---

# Mental Model

Imagine

a bunch of keys

on a keychain.

Some keys

can open

every room

in a building.

Those are

Super Keys.

Now remove

every unnecessary key.

The smallest

keyring

that still opens

every room

is the

Candidate Key.

---

# Bridge

Finding Candidate Keys

is useful,

but interview questions

often give

dozens of

Functional Dependencies.

Many of them

are redundant.

Question:

Can we simplify

the Functional Dependencies

without changing

their meaning?

Yes.

This process

is called

```text
Minimal Cover.
```

Minimal Cover

helps reduce

unnecessary dependencies

before normalization.

# 2. Minimal Cover

So far,

we have learned

Functional Dependencies.

Example

```text
A → B

B → C

A → C
```

Question:

Do we really need

all three

Functional Dependencies?

Notice something.

We already know

```text
A → B
```

and

```text
B → C
```

Using

Transitivity,

we automatically get

```text
A → C
```

Therefore,

```text
A → C
```

is unnecessary.

We can remove it.

The meaning

of the database

doesn't change.

This idea

is called

```text
Minimal Cover.
```

---

# Why Do We Need A Minimal Cover?

Suppose

a database designer

writes

100 Functional Dependencies.

Question

Do all of them

contain

new information?

Not always.

Some are

duplicates.

Some are

derived from others.

Some contain

unnecessary attributes.

Minimal Cover

removes

all unnecessary information

while keeping

exactly the same meaning.

Think of it as

simplifying

an equation

without changing

its value.

---

# Definition

A Minimal Cover

is

the smallest possible

set of Functional Dependencies

that

preserves

exactly the same information

as

the original set.

Nothing useful

is lost.

Only redundancy

is removed.

---

# Properties Of A Minimal Cover

A Functional Dependency Set

is called

Minimal

when

all three conditions

are satisfied.

---

## Condition 1

One attribute

on the

right side.

Wrong

```text
A → BC
```

Split it into

```text
A → B

A → C
```

Every Functional Dependency

must have

only one attribute

on the right side.

---

## Condition 2

No unnecessary attributes

on the

left side.

Suppose

```text
AB → C
```

Question

Do we really need

both

A

and

B?

Suppose

A alone

can determine

C.

Then

B

is unnecessary.

We simplify it.

```text
AB → C

↓

A → C
```

Now

the dependency

is smaller.

---

## Condition 3

No redundant

Functional Dependencies.

Suppose

```text
A → B

B → C

A → C
```

Question

Do we need

A → C?

No.

It already follows

from

the other two.

Delete it.

---

# The Algorithm

Whenever

you are asked

to find

a Minimal Cover,

follow

three steps.

```text
Step 1

Split

Right Side

↓

Step 2

Remove

Extra Attributes

from Left Side

↓

Step 3

Remove

Redundant

Functional Dependencies
```

Always

in this order.

---

# Example 1

Given

```text
A → BC

B → C
```

Find

Minimal Cover.

---

Step 1

Split

```text
A → B

A → C

B → C
```

Done.

---

Step 2

Check

left sides.

Every left side

contains

one attribute.

Nothing to remove.

---

Step 3

Check redundancy.

Question

Can

A → C

be obtained

from

A → B

and

B → C?

Yes.

Using

Transitivity.

Delete it.

Final Answer

```text
A → B

B → C
```

---

# Example 2

Given

```text
AB → C

A → C
```

Question

Do we need

AB → C?

Notice

A alone

already determines

C.

B contributes

nothing.

Therefore,

```text
AB → C

↓

A → C
```

Now

both dependencies

are identical.

Keep only one.

Final

```text
A → C
```

---

# Example 3

Given

```text
A → BC

C → D

D → E
```

Step 1

Split.

```text
A → B

A → C

C → D

D → E
```

Step 2

No unnecessary

left attributes.

---

Step 3

Question

Can

A → C

be removed?

No.

Nothing derives it.

Can

C → D

be removed?

No.

Can

D → E

be removed?

No.

Already minimal.

---

# How Do We Check

Whether

A Dependency

Is Redundant?

Temporarily

remove it.

Then

compute

Attribute Closure.

If

the removed dependency

can still

be derived,

it was redundant.

Otherwise,

put it back.

---

# Common Mistakes

❌

Thinking

Minimal Cover

changes

the database.

No.

It only removes

unnecessary dependencies.

---

❌

Removing

dependencies

without checking

Attribute Closure.

Always verify.

---

❌

Forgetting

to split

the right side

before starting.

Always begin

with

Step 1.

---

# Why Do We Study

Minimal Cover?

Mainly because

database designers

prefer

the simplest

possible

dependency set.

It also helps

while

decomposing tables

during normalization.

---

# Interview Questions

## What is

a Minimal Cover?

```text
The smallest

equivalent set

of Functional Dependencies

containing

no redundant information.
```

---

## What are

the three steps?

```text
Split

↓

Remove Extra Left Attributes

↓

Remove Redundant Dependencies
```

---

## Does

Minimal Cover

change

the meaning

of the Functional Dependencies?

```text
No.

It only removes

redundancy.
```

---

# Mental Model

Suppose

you write

the following directions.

```text
Go to School.

↓

Turn Left.

↓

Reach Library.

↓

Go to School.

↓

Turn Left.

↓

Reach Library.
```

The second set

adds

no new information.

Removing it

doesn't change

the route.

Minimal Cover

does exactly

the same thing.

It removes

repeated

or unnecessary

instructions,

while keeping

the destination

exactly the same.

---

# Bridge

Now

we finally have

everything needed

for Normalization.

We know

Functional Dependencies.

We know

Candidate Keys.

We know

Partial Dependencies.

We know

Transitive Dependencies.

Now,

we begin

Normal Forms.

Starting with

the simplest one:

# 3. First Normal Form (1NF)

```text
First Normal Form (1NF)
```

Surprisingly,

1NF

has nothing to do

with

Functional Dependencies.

It simply says

that every cell

should contain

one value,

not multiple values.

1NF
↓

Fix the table structure

--------------------

2NF
↓

Remove Partial Dependency

--------------------

3NF
↓

Remove Transitive Dependency

--------------------

BCNF
↓

Every Determinant
must be a Candidate Key

# 4. Second Normal Form (2NF)

In 1NF,

we solved

only one problem.

```text
Multiple values

inside one cell.
```

Our table

became

well structured.

But

redundancy

still exists.

---

# The Problem

Suppose

we store

student marks.

| StudentID | Subject | StudentName | Marks |
|-----------|----------|-------------|-------|
|101|DBMS|Arbaaz|90|
|101|OS|Arbaaz|80|
|101|CN|Arbaaz|85|
|102|DBMS|Rahul|88|

Question:

What is

the Candidate Key?

Think.

Can StudentID

alone

identify a row?

No.

Student 101

has multiple subjects.

---

Can Subject

alone

identify a row?

No.

Many students

study DBMS.

---

Need both.

```text
(StudentID, Subject)
```

This is

the Candidate Key.

---

# The Functional Dependencies

We know

```text
(StudentID, Subject)

↓

Marks
```

Correct.

---

We also know

```text
StudentID

↓

StudentName
```

Notice something.

StudentName

does NOT

depend

on the whole key.

It depends

only on

StudentID.

Subject

has nothing

to do with

StudentName.

This is called

```text
Partial Dependency.
```

---

# Why Is This Bad?

Look carefully.

| StudentID | Subject | StudentName |
|-----------|----------|-------------|
|101|DBMS|Arbaaz|
|101|OS|Arbaaz|
|101|CN|Arbaaz|

Question

How many times

is

"Arbaaz"

stored?

```text
3
```

Suppose

Arbaaz

changes

his name.

Question

How many rows

must be updated?

```text
3
```

Miss one row,

and

the database

becomes inconsistent.

Exactly

the Update Anomaly

we studied earlier.

---

# Why Did This Happen?

Because

our key

is

```text
(StudentID, Subject)
```

But

StudentName

depends only

on

StudentID.

Half

the key

is enough.

The other half

is unnecessary.

This creates

redundancy.

---

# Definition

A table

is in

Second Normal Form

if

```text
1.

It is already

in 1NF.

AND

2.

Every non-key attribute

is Fully Functionally Dependent

on the entire

Candidate Key.
```

Remember

the phrase

```text
Fully Functionally Dependent.
```

We studied it

earlier.

Now

you can see

why it matters.

---

# How Do We Fix It?

Instead of

one table,

split it.

---

Student

| StudentID | StudentName |
|-----------|-------------|
|101|Arbaaz|
|102|Rahul|

---

Marks

| StudentID | Subject | Marks |
|-----------|----------|-------|
|101|DBMS|90|
|101|OS|80|
|101|CN|85|
|102|DBMS|88|

Notice

what happened.

StudentName

is stored

only once.

No repetition.

No redundancy.

---

# Did We Lose Any Data?

No.

We only

organized it

better.

Whenever needed,

both tables

can be joined.

```sql
SELECT *

FROM Student S

JOIN Marks M

ON S.StudentID=M.StudentID;
```

The original information

is recovered.

---

# Another Example

Employee Project

| EmpID | ProjectID | EmpName | Hours |
|-------|-----------|----------|-------|
|1|101|Alice|20|
|1|102|Alice|15|
|2|101|Bob|30|

Candidate Key

```text
(EmpID, ProjectID)
```

Functional Dependencies

```text
EmpID

↓

EmpName
```

```text
(EmpID, ProjectID)

↓

Hours
```

Question

Does

EmpName

depend

on

ProjectID?

No.

Therefore,

Partial Dependency.

Not in

2NF.

---

Split.

Employee

| EmpID | EmpName |
|-------|----------|
|1|Alice|
|2|Bob|

---

ProjectHours

| EmpID | ProjectID | Hours |
|-------|-----------|-------|
|1|101|20|
|1|102|15|
|2|101|30|

Now

2NF.

---

# Very Important Rule

Question

Can

Partial Dependency

exist

if

the Candidate Key

contains

only one attribute?

No.

Impossible.

Example

Candidate Key

```text
StudentID
```

Question

Can

StudentName

depend

on

part

of StudentID?

No.

There is

no "part."

Therefore

tables

with a

single-attribute

Candidate Key

are

automatically

in 2NF.

This is

a favourite

interview question.

---

# Common Mistakes

❌

Thinking

2NF

removes

all redundancy.

No.

It removes

only

redundancy

caused by

Partial Dependencies.

---

❌

Thinking

every repeated value

means

2NF violation.

Wrong.

Only

Partial Dependencies

matter.

---

❌

Forgetting

that

1NF

must already

be satisfied.

Every table

must reach

1NF

before

2NF.

---

# How To Check 2NF

Step 1

Find

Candidate Keys.

↓

Step 2

Find

Non-Key Attributes.

↓

Step 3

Ask

```text
Does

every

non-key attribute

depend

on

the whole

Candidate Key?
```

If yes,

2NF.

If no,

split the table.

---

# Interview Questions

## What does

2NF remove?

```text
Partial Dependency.
```

---

## Can

Partial Dependency

exist

without

Composite Keys?

```text
No.
```

---

## Is every

1NF table

also

2NF?

```text
No.
```

---

## Is every

2NF table

also

1NF?

```text
Yes.
```

Because

2NF

requires

1NF first.

---

# Mental Model

Imagine

a bicycle

that needs

two keys

to unlock.

One key

opens

the bicycle.

The second key

opens

the lock box.

Now suppose

someone writes

your name

on

both keys.

Question

Does your name

depend

on

both keys?

No.

Your name

belongs

only

to you.

Writing it

on both keys

creates

unnecessary duplication.

That's exactly

what

Partial Dependency

does.

---

# Bridge

We fixed

Partial Dependency.

Great.

But

look at

this table.

| StudentID | Department | HOD |
|-----------|------------|-----------|
|101|CSE|Dr. Rao|
|102|ECE|Dr. Sharma|

Functional Dependencies

```text
StudentID

↓

Department
```

```text
Department

↓

HOD
```

Question

Does

HOD

depend

directly

on StudentID?

No.

It depends

on

Department,

which depends

on StudentID.

This is called

a

Transitive Dependency.

Removing

Transitive Dependencies

is exactly

what

Third Normal Form (3NF)

does.

# 5. Third Normal Form (3NF)

In 2NF,

we removed

Partial Dependencies.

Question:

Is our database

now

perfect?

Not yet.

There is still

another type

of redundancy.

It is called

```text
Transitive Dependency.
```

---

# The Problem

Suppose

we have

this table.

| StudentID | Department | HOD |
|-----------|------------|------------|
|101|CSE|Dr. Rao|
|102|ECE|Dr. Sharma|
|103|CSE|Dr. Rao|

Question

What is

the Candidate Key?

```text
StudentID
```

because

StudentID

uniquely identifies

every student.

---

# Functional Dependencies

We know

```text
StudentID → Department

Department → HOD
```

Question

Does

StudentID

directly

determine

HOD?

No.

StudentID

determines

Department.

Department

determines

HOD.

Therefore

```text
StudentID

↓

Department

↓

HOD
```

This is

Transitive Dependency.

---

# Why Is This Bad?

Look carefully.

| StudentID | Department | HOD |
|-----------|------------|------------|
|101|CSE|Dr. Rao|
|103|CSE|Dr. Rao|

Question

How many times

is

Dr. Rao

stored?

```text
Twice
```

Suppose

Dr. Rao retires.

New HOD

becomes

Dr. Kumar.

Question

How many rows

must change?

Every student

in

CSE.

Exactly

the Update Anomaly

again.

---

# Root Cause

Question

Why is

HOD

repeated?

Because

HOD

doesn't actually

depend on

StudentID.

It depends on

Department.

Department

is acting

as

a middleman.

---

# Definition

A table

is in

Third Normal Form

if

```text
1.

It is already

in 2NF.

AND

2.

No non-key attribute

depends on

another

non-key attribute.
```

Another way

to remember.

```text
Non-Key

↓

Non-Key

❌
```

---

# How Do We Fix It?

Split

the table.

Student

| StudentID | Department |
|-----------|------------|
|101|CSE|
|102|ECE|
|103|CSE|

Department

| Department | HOD |
|------------|------------|
|CSE|Dr. Rao|
|ECE|Dr. Sharma|

Now

the HOD

is stored

only once.

No redundancy.

---

# Another Example

Employee

| EmpID | Dept | Manager |
|-------|------|----------|
|1|HR|Alice|
|2|IT|Bob|
|3|HR|Alice|

Functional Dependencies

```text
EmpID → Dept

Dept → Manager
```

Question

Does

Manager

really depend

on

EmpID?

No.

It depends

on

Dept.

Split.

Employee

| EmpID | Dept |
|-------|------|
|1|HR|
|2|IT|
|3|HR|

Department

| Dept | Manager |
|------|----------|
|HR|Alice|
|IT|Bob|

Now

3NF.

---

# How To Detect 3NF

Step 1

Find

Candidate Key.

↓

Step 2

Find

Non-Key Attributes.

↓

Step 3

Ask

```text
Does one

Non-Key Attribute

determine

another?

```

If yes,

3NF violation.

---

# Common Mistakes

❌

Thinking

every dependency

violates 3NF.

Wrong.

Only

Transitive Dependencies

matter.

---

❌

Confusing

2NF

and

3NF.

Remember

```text
2NF

↓

Partial Dependency

-------------------

3NF

↓

Transitive Dependency
```

---

# Interview Questions

## What does

3NF remove?

```text
Transitive Dependency.
```

---

## Does

3NF

remove

all redundancy?

Almost,

but not always.

BCNF

is even stronger.

---

# Mental Model

Suppose

you ask

a student

who the HOD is.

The student

doesn't know

directly.

He first

looks up

his department.

Then

the department

tells him

the HOD.

The information

flows

through

another attribute.

That is

Transitive Dependency.

---

# Bridge

Question

Can a table

be in

3NF

and still

contain redundancy?

Surprisingly,

Yes.

That is exactly

why

BCNF exists.

======================================================

# 6. Boyce-Codd Normal Form (BCNF)

BCNF

is often called

a stronger

version

of

3NF.

Most books

say this

without explaining

why.

Let's understand.

---

# The Rule

3NF says

```text
No Transitive Dependency.
```

BCNF says

something

more powerful.

```text
Every Determinant

must be

a Candidate Key.
```

Remember

Determinant?

Left side

of

a Functional Dependency.

Example

```text
A → B
```

A

is

the Determinant.

BCNF says

```text
Every

Determinant

must itself

be

a Candidate Key.
```

---

# Example

Relation

| Teacher | Subject | Room |
|----------|----------|------|
|Alice|DBMS|101|
|Bob|OS|102|
|Alice|CN|101|

Suppose

Functional Dependencies

are

```text
(Teacher, Subject)

↓

Room
```

Also

```text
Room

↓

Teacher
```

Question

Candidate Key?

```text
(Teacher, Subject)
```

Question

Is

Room

a Candidate Key?

No.

But

Room

determines

Teacher.

```text
Room

↓

Teacher
```

Notice

who is

the Determinant.

```text
Room
```

Is Room

a Candidate Key?

No.

Therefore

BCNF

is violated.

---

# Why Is This Bad?

Suppose

Room 101

changes

its teacher.

Question

How many rows

must change?

Every subject

using

Room 101.

Again,

redundancy.

---

# Fix

Split.

Room

| Room | Teacher |
|------|----------|
|101|Alice|
|102|Bob|

Teaching

| Teacher | Subject |
|----------|----------|
|Alice|DBMS|
|Alice|CN|
|Bob|OS|

BCNF achieved.

---

# Difference Between

3NF

and

BCNF

This is

the favourite

interview question.

| 3NF | BCNF |
|------|------|
| Removes Transitive Dependency | Every Determinant must be Candidate Key |
| Slightly weaker | Stronger |
| May still allow some redundancy | Removes more redundancy |
| Easier to satisfy | Harder to satisfy |

---

# Easy Way To Remember

Imagine

a school.

Only

the Principal

can make

school-wide decisions.

Candidate Key

=

Principal.

Now suppose

a random teacher

starts making

school-wide decisions.

Problem.

BCNF says

```text
Only

Candidate Keys

may determine

other attributes.
```

No exceptions.

---

# 2NF vs 3NF vs BCNF

2NF

asks

```text
Does

every Non-Key Attribute

depend

on

the entire

Candidate Key?
```

If no,

2NF violation.

---

3NF

asks

```text
Does

a Non-Key Attribute

depend

on

another

Non-Key Attribute?
```

If yes,

3NF violation.

---

BCNF

asks

```text
Is

every Determinant

a Candidate Key?
```

If no,

BCNF violation.

---

# Complete Flow

```text
1NF

↓

Atomic Values

-----------------------

2NF

↓

Remove Partial Dependency

-----------------------

3NF

↓

Remove Transitive Dependency

-----------------------

BCNF

↓

Every Determinant

Must Be

Candidate Key
```

---

# Interview Questions

## Which is stronger?

```text
BCNF
```

---

## Is every BCNF table

also

3NF?

```text
Yes.
```

---

## Is every 3NF table

BCNF?

```text
No.
```

---

## What is

the BCNF rule?

```text
Every Determinant

must be

a Candidate Key.
```

---

# Final Mental Model

Imagine

building

a house.

```text
1NF

↓

Organize the bricks.

----------------

2NF

↓

Remove

unnecessary duplicates

caused by

partial ownership.

----------------

3NF

↓

Remove

indirect dependencies.

----------------

BCNF

↓

Ensure

only the true owners

(Candidate Keys)

control information.
```

After BCNF,

your database

is highly organized,

contains

minimal redundancy,

and is much easier

to maintain.

---

# Bridge

The remaining

Normal Forms

are

4NF

and

5NF.

They solve

very specialized

problems

(Multi-Valued Dependencies

and

Join Dependencies).

For

SWE/SDE interviews,

an overview

is usually sufficient.

======================================================

# 7. Fourth Normal Form (4NF) - Overview

By the time

a table reaches

BCNF,

most redundancy

has already

been removed.

However,

there is still

one special problem.

It is called

```text
Multi-Valued Dependency (MVD)
```

---

## What Is A Multi-Valued Dependency?

Suppose

a professor

can teach

multiple subjects

and

also knows

multiple programming languages.

Example

| Professor | Subject | Language |
|------------|----------|-----------|
|Alice|DBMS|Java|
|Alice|DBMS|Python|
|Alice|OS|Java|
|Alice|OS|Python|

Notice

Subjects

and

Languages

are independent.

Alice teaches

DBMS

and

OS.

Alice knows

Java

and

Python.

But

the database

stores

every combination.

This creates

unnecessary redundancy.

---

## Solution

Split

into

two tables.

ProfessorSubject

| Professor | Subject |
|------------|----------|
|Alice|DBMS|
|Alice|OS|

ProfessorLanguage

| Professor | Language |
|------------|-----------|
|Alice|Java|
|Alice|Python|

The unnecessary

combinations disappear.

---

## Definition

A relation

is in

Fourth Normal Form

if

it has

no

non-trivial

Multi-Valued Dependencies.

---

## Interview Point

For most

SWE interviews,

knowing

that

4NF removes

Multi-Valued Dependencies

is enough.

Very few companies

ask

4NF problems.

======================================================

# 8. Fifth Normal Form (5NF) - Overview

5NF

is even more advanced.

It deals with

Join Dependencies.

---

## Why?

Sometimes

a table

is split

into

multiple tables.

Question

Can we always

join them back

without creating

extra rows?

If not,

5NF

may be violated.

---

Example

Supplier

Part

Project

relationships

are classic

5NF examples.

These are

rarely used

outside

advanced database design.

---

## Definition

A relation

is in

Fifth Normal Form

if

every Join Dependency

is implied

by

Candidate Keys.

---

## Interview Point

For freshers,

remember only

```text
5NF

↓

Join Dependency
```

Nothing more

is usually expected.

======================================================

# 9. Denormalization

So far,

every Normal Form

has tried

to

split tables.

Question

Why would

anyone

combine them

again?

Answer:

Performance.

---

## Example

Normalized Database

Student

| StudentID | Department |
|-----------|------------|

Department

| Department | HOD |
|------------|------|

To display

student

and

HOD,

the DBMS

must perform

a JOIN.

---

Suppose

millions

of records

are queried

every second.

Repeated JOINs

can become expensive.

One solution

is

Denormalization.

Store

the HOD

directly

inside

the Student table.

This introduces

redundancy,

but

reduces JOIN operations.

---

## Advantages

- Faster Reads
- Fewer JOINs
- Better Reporting Performance

---

## Disadvantages

- More Redundancy
- More Storage
- More Update Anomalies
- Harder Maintenance

---

## When Is Denormalization Used?

Large-scale systems

that

read

far more often

than they write.

Examples

- Analytics
- Dashboards
- Data Warehouses
- Reporting Systems

Most OLTP systems

prefer

Normalization.

======================================================

# Complete Revision

## Normal Forms

```text
1NF

↓

Atomic Values

------------------

2NF

↓

Remove Partial Dependency

------------------

3NF

↓

Remove Transitive Dependency

------------------

BCNF

↓

Every Determinant

must be

a Candidate Key

------------------

4NF

↓

Remove

Multi-Valued Dependency

------------------

5NF

↓

Remove

Join Dependency
```

======================================================

# Dependency Revision

```text
Functional Dependency

↓

Full Dependency

↓

Partial Dependency

↓

Transitive Dependency

↓

Normalization
```

======================================================

# Key Revision

```text
Super Key

↓

Candidate Key

↓

Primary Key

↓

Alternate Key
```

======================================================

# Entire Chapter Mental Model

Think of

Normalization

as

cleaning

a messy room.

Initially

everything

is thrown

into

one giant box.

```text
Student

Department

HOD

Subjects

Marks

Phone Numbers

Everything

mixed together.
```

Step by step,

we organize it.

```text
1NF

↓

One value

per box.

-------------------

2NF

↓

Move information

that depends

on

only part

of the key.

-------------------

3NF

↓

Move information

that depends

on

another

non-key attribute.

-------------------

BCNF

↓

Ensure

only Candidate Keys

control

other information.

-------------------

4NF

↓

Separate

independent

multi-valued facts.

-------------------

5NF

↓

Ensure

tables can be

joined back

without

introducing

incorrect data.
```

Eventually,

every piece

of information

has

exactly

one proper place.

The database becomes

- Easy to maintain
- Consistent
- Less redundant
- Easier to query
- Easier to update

======================================================

# Interview Cheat Sheet

| Topic | One-Line Answer |
|--------|-----------------|
| Functional Dependency | One attribute determines another. |
| Attribute Closure | All attributes determined by an attribute set. |
| Candidate Key | Minimal attribute set identifying every row. |
| Super Key | Attribute set identifying every row. |
| 1NF | Atomic values. |
| 2NF | Remove Partial Dependency. |
| 3NF | Remove Transitive Dependency. |
| BCNF | Every Determinant must be a Candidate Key. |
| 4NF | Remove Multi-Valued Dependencies. |
| 5NF | Remove Join Dependencies. |
| Denormalization | Intentionally introduce redundancy to improve read performance. |

======================================================

# End Of DBMS05

Congratulations.

You have completed

the most theory-intensive

chapter

of DBMS.

The remaining chapters

(Transactions,

Concurrency,

Recovery,

Indexing)

build naturally

on concepts

you've already learned

and are generally

easier to grasp.
