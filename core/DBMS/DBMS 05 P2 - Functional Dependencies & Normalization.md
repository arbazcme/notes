# DBMS 05 P2 - Functional Dependencies & Normalization.md

# Navigation

- [6. Armstrong's Axioms](#6-armstrongs-axioms)
  - [Why Do We Need Them?](#why-do-we-need-them)
  - [Think Of Them Like Mathematics](#think-of-them-like-mathematics)
  - [The Three Basic Axioms](#the-three-basic-axioms)
  - [6.1 Reflexivity Rule](#61-reflexivity-rule)
  - [6.2 Augmentation Rule](#62-augmentation-rule)
  - [6.3 Transitivity Rule](#63-transitivity-rule)
  - [Derived Rules](#derived-rules)
  - [Summary Table](#summary-table)
  - [Interview Questions](#interview-questions)
  - [Mental Model](#mental-model)

- [7. Attribute Closure](#7-attribute-closure)
  - [What Is Attribute Closure?](#what-is-attribute-closure)
  - [Why Do We Need Attribute Closure?](#why-do-we-need-attribute-closure)
  - [The Closure Algorithm](#the-closure-algorithm)
  - [Examples](#example-1)
  - [Practice Problems](#practice-1)
  - [Interview Questions](#interview-questions-1)

- [Functional Dependencies → Normalization (Interview Flow)](#functional-dependencies--normalization-interview-flow)
  - [Business Rules](#step-1-business-rules)
  - [Functional Dependencies](#step-2-convert-into-functional-dependencies)
  - [Armstrong's Axioms](#step-3-armstrongs-axioms)
  - [Attribute Closure](#step-4-attribute-closure)
  - [Check 2NF](#step-5-check-2nf)
  - [Check 3NF](#step-6-check-3nf)
  - [Check BCNF](#step-7-bcnf)
  - [Final Flow](#final-flow)
  - [Interview Summary](#interview-summary)

- [Formal Rules of Normalization](#formal-rules-of-normalization-interview)
  - [2NF](#2nf-second-normal-form)
  - [3NF](#3nf-third-normal-form)
  - [BCNF](#bcnf-boyce-codd-normal-form)
  - [Quick Revision](#quick-revision)

- [Normalization Interview Checklist](#normalization-interview-checklist)

- [Fourth Normal Form (4NF)](#14-fourth-normal-form-4nf)
  
- [Fifth Normal Form (5NF)](#15-fifth-normal-form-5nf)
  
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
# 14. Fourth Normal Form (4NF)

After BCNF,

most redundancy

has already been removed.

However,

one special kind of redundancy

can still exist.

It is called

```text
Multi-Valued Dependency (MVD).
```

Unlike Functional Dependency,

where one attribute determines another,

a Multi-Valued Dependency means

one attribute independently determines

multiple values of another attribute.

---

## Example

Suppose

a professor

can teach multiple subjects

and

also knows multiple programming languages.

| Professor | Subject | Language |
|------------|----------|----------|
|Alice|DBMS|Java|
|Alice|DBMS|Python|
|Alice|OS|Java|
|Alice|OS|Python|

Notice

Subjects

and

Languages

are completely independent.

The database stores

every possible combination,

creating unnecessary redundancy.

---

## Solution

Split into

ProfessorSubject

| Professor | Subject |
|------------|----------|
|Alice|DBMS|
|Alice|OS|

ProfessorLanguage

| Professor | Language |
|------------|----------|
|Alice|Java|
|Alice|Python|

Now,

the unnecessary combinations disappear.

---

## Definition

A relation is in

Fourth Normal Form (4NF)

if

it is already in BCNF

and

contains

no non-trivial

Multi-Valued Dependencies.

---

## Interview Questions

### What does 4NF remove?

```text
Multi-Valued Dependencies.
```

---

### Is every BCNF table also in 4NF?

```text
No.

A BCNF table
can still contain
Multi-Valued Dependencies.
```

---

### Is every 4NF table also BCNF?

```text
Yes.
```

---

### Do freshers solve 4NF problems?

Usually no.

Knowing

the definition,

purpose,

and one example

is enough.

---

# 15. Fifth Normal Form (5NF)

5NF

deals with

Join Dependencies.

---

## Why?

Sometimes

a table

is decomposed into

multiple tables.

Question:

Can we always

join them back

to recover

exactly

the original table?

Not always.

5NF ensures

that the decomposition

is lossless

and

does not create

extra rows.

---

## Conceptual Example

Imagine

three entities

```text
Supplier

Part

Project
```

These relationships

may be split

into multiple tables.

If joining them

creates

incorrect combinations,

the design

violates 5NF.

---

## Definition

A relation is in

Fifth Normal Form (5NF)

if

every Join Dependency

is implied

by

its Candidate Keys.

---

## Why Is 5NF Rare?

Most production databases

never require it.

BCNF

or

4NF

is sufficient

for the vast majority

of applications.

5NF mainly appears in

advanced database design.

---

## Interview Questions

### What does 5NF remove?

```text
Join Dependency.
```

---

### Is 5NF commonly asked?

```text
Rarely.

Freshers are expected
to know only

its purpose,

definition,

and basic idea.
```
