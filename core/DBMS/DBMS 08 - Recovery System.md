# DBMS 08 - Recovery System

# Navigation
 

1. [Why Do We Need Recovery?](#1-why-do-we-need-recovery)
2. [Types of Failures](#2-types-of-failures)
3. [Log-Based Recovery](#3-log-based-recovery)
4. [Write-Ahead Logging (WAL)](#4-write-ahead-logging-wal)
5. [Undo and Redo](#5-undo-and-redo)
6. [Checkpoints](#6-checkpoints)
7. [Shadow Paging](#7-shadow-paging)
8. [Final Revision](#final-revision)
9. [Interview Summary](#interview-summary)

---


# PRE-REQ:

# DBMS: Normal Execution, Logging & Recovery

## 1. Normal Execution

```text
Database (Disk)
        ↓
Load Required Pages
        ↓
RAM (Buffer Pool)
        ↓
Transactions Read/Write Pages
        ↓
Concurrency Control (Locks / Timestamps)
        ↓
Dirty Pages
        ↓
Background Writer
        ↓
Database (Disk)
```

> **Key Idea:** Almost all reads, writes, and concurrency control happen on **pages in RAM**, not directly on disk.

---

# 2. Where Do Logs Fit?

Whenever a transaction modifies a page:

```text
Modify RAM Page
        ↓
Create Log Record
        ↓
Write Log to Disk (Write-Ahead Logging - WAL)
        ↓
COMMIT
        ↓
Later, Background Writer writes Dirty Pages to Disk
```

### Important

* ✅ Log reaches disk **before** the actual database page.
* ✅ This guarantees crash recovery.

---

# 3. Crash Recovery Example

### Before Crash

```text
RAM
A = 200

Disk
A = 100

Log
A : 100 → 200
COMMIT
```

Crash 💥

RAM is lost.

---

### After Restart

```text
Disk
A = 100

Log
A : 100 → 200
COMMIT
```

Recovery Manager checks the log.

Since **COMMIT** exists:

```text
REDO

A = 200
```

Database becomes consistent again.

---

# 4. Final Mental Model

```text
Database (Disk)
        ↓
Load Pages
        ↓
RAM (Buffer Pool)
        ↓
Transactions Execute
        ↓
Concurrency Control
        ↓
Dirty Pages
        ↓
Write Logs to Disk (WAL)
        ↓
COMMIT
        ↓
Background Writer writes Dirty Pages to Database Disk
```

If a crash occurs **before dirty pages reach disk**, the **logs recover the database using REDO/UNDO**.

---

# 5. Concurrency in DBMS

**Question:** Is all concurrency on pages in RAM?

**Answer:** **Mostly Yes.**

* Transactions operate on **data cached in RAM (Buffer Pool)**.
* Locks or Timestamp checks are performed on these in-memory data items (rows/pages/tables).
* The **disk is not involved in concurrency control**.
* The disk only stores the **persistent copy** of the database.

### Memory Trick 🧠

```text
Disk
   ↓
RAM (Buffer Pool)
   ↓
Concurrency Control
   ↓
Dirty Pages
   ↓
Logs → COMMIT
   ↓
Background Writer
   ↓
Disk
```




---

# 1. Why Do We Need Recovery?

So far,

we have learned

how transactions

execute correctly.

We studied

- ACID
- Concurrency Control
- Locks
- Serializability

Question:

What happens if

the database crashes

**in the middle of a transaction?**

---

## The Problem

Suppose

your bank account

contains

```text
₹10,000
```

Transaction T1

transfers

₹2,000

to another account.

It performs

```text
Read Balance

↓

10000

↓

Subtract 2000

↓

Write 8000
```

Immediately after writing,

the power goes off.

```text
⚡ System Crash
```

Question:

Did the transaction finish?

```text
No.
```

Question:

Should the database

keep ₹8000

or

restore ₹10000?

If the transfer

never completed,

keeping ₹8000

would lose money.

If the receiver

already got ₹2000,

restoring ₹10000

would create money.

Either way,

the database

becomes inconsistent.

---

## Another Example

Suppose

you order

a laptop online.

The database performs

```text
Reduce Stock

↓

Charge Payment

↓

Create Order
```

Now imagine

the server crashes

after

charging your card

but before

creating the order.

Result

```text
Money deducted

↓

No Order
```

Clearly,

this is unacceptable.

---

## The Real Problem

The issue is

not the crash.

Computers crash.

Power fails.

Servers restart.

Disks fail.

These are unavoidable.

The real challenge is

```text
How can the DBMS

recover

to a consistent state?
```

This is exactly

what the

Recovery System

does.

---

# What Is Recovery?

Recovery means

bringing the database

back to

a **correct and consistent state**

after a failure.

The DBMS

must decide

```text
Which transactions

should be

Undone?

Which transactions

should be

Redone?
```

Everything

in this chapter

answers

those two questions.

---

# Goals Of Recovery

A Recovery System

tries to ensure

three things.

### 1.

Completed transactions

must never be lost.

Suppose

T1

already committed.

Even if

the system crashes,

its changes

must remain.

---

### 2.

Incomplete transactions

must disappear.

If

T2

crashed before Commit,

the database

should behave

as though

T2 never existed.

---

### 3.

The database

must remain consistent.

After recovery,

all constraints

must still hold.

No corrupted data.

No half-completed transactions.

---

## Where Does ACID Fit?

Recovery

mainly supports

two ACID properties.

```text
Atomicity

↓

Undo incomplete work

--------------------

Durability

↓

Committed work

must survive crashes.
```

Notice

Recovery

is basically

Atomicity

+

Durability

implemented in practice.

---

# Interview Questions

### Why is Recovery needed?

```text
To restore

the database

to a consistent state

after failures.
```

---

### Which ACID properties

does Recovery mainly support?

```text
Atomicity

and

Durability.
```

---

### What are the two main tasks

of Recovery?

```text
Undo

and

Redo.
```

---

# Mental Model

Imagine

writing

an exam.

You save

your answers

every few minutes.

Suddenly,

your laptop crashes.

Question:

Should the system

restore

only

the last saved answers,

or

everything you typed,

or

nothing?

Recovery

solves

this exact problem

for databases.

---

# Bridge

Now we know

why

Recovery

is needed.

Question:

Do all failures

look the same?

No.

Sometimes

only one transaction fails.

Sometimes

the entire operating system crashes.

Sometimes

the hard disk itself

is destroyed.

These failures

require

different recovery strategies.

---

# 2. Types Of Failures

Not every database failure

is the same.

Some failures

affect

only one transaction.

Others

bring down

the entire DBMS.

Some

destroy

the storage device itself.

The recovery method

depends

on

the type of failure.

---

# Overview

There are

three major categories.

```text
1.

Transaction Failure

-------------------

2.

System Failure (Crash)

-------------------

3.

Media Failure
```

Let's study them

one by one.

---

# 2.1 Transaction Failure

This is

the smallest failure.

Only

one transaction

fails.

The database

and

other transactions

continue normally.

---

## Causes

Examples

```text
Divide by Zero

↓

Constraint Violation

↓

Deadlock Victim

↓

User executes

ROLLBACK
```

Suppose

a transaction

tries to insert

a duplicate

Primary Key.

```sql
INSERT INTO Student

VALUES

(101,'Arbaaz');
```

But

RollNo 101

already exists.

The DBMS

aborts

only that transaction.

Everything else

continues.

---

## Recovery

Very simple.

```text
Undo

that transaction.
```

Nothing else

needs recovery.

---

# 2.2 System Failure (Crash)

This is

much more serious.

The entire DBMS

stops.

Example

```text
Power Failure

↓

Operating System Crash

↓

Server Restart

↓

Memory Failure
```

Notice

the hard disk

is still intact.

Only

main memory

is lost.

---

## Why Is This Dangerous?

Suppose

Transaction T1

already updated

the disk.

Transaction T2

updated

only RAM.

Crash.

RAM disappears.

Disk survives.

Now

the DBMS

must determine

```text
Which transactions

had committed?

↓

Redo them.

------------------

Which had not?

↓

Undo them.
```

We'll learn

how this works

using Logs.

---

# 2.3 Media Failure

This is

the worst type

of failure.

The storage device

itself

is damaged.

Examples

```text
Hard Disk Failure

↓

SSD Failure

↓

Fire

↓

Flood

↓

Disk Corruption
```

Now

both

memory

and

database files

may be lost.

---

## Recovery

Simple Undo/Redo

is not enough.

The DBMS

must restore

the database

from

```text
Backups

+

Log Files.
```

This takes

much longer

than

recovering

from

a normal crash.

---

# Comparison

| Failure | What Fails? | Recovery |
|----------|-------------|----------|
| Transaction Failure | One Transaction | Undo |
| System Crash | Entire DBMS / RAM | Undo + Redo |
| Media Failure | Storage Device | Restore Backup + Logs |

---

# Interview Questions

### What are the three major types of failures?

```text
Transaction Failure

System Failure

Media Failure
```

---

### Which failure affects only one transaction?

```text
Transaction Failure.
```

---

### Which failure loses RAM

but not Disk?

```text
System Crash.
```

---

### Which failure requires backups?

```text
Media Failure.
```

---

# Mental Model

Imagine

writing

a book.

Transaction Failure

↓

One page

contains mistakes.

Rewrite

only that page.

--------------------

System Failure

↓

Your computer

restarts.

Recover

the unsaved work.

--------------------

Media Failure

↓

The laptop

is destroyed.

Restore

from cloud backup.

---

# Bridge

Now we know

what kinds of failures

can happen.

Question:

How does the DBMS

know

what was happening

before the crash?

How can it know

which transactions

must be

Undone

or

Redone?

The answer is

```text
Logs.
```

Every important action

performed by the DBMS

is first recorded

inside

a Log File.

The Log

is the heart

of every

Recovery System.

# 3. Log-Based Recovery

In the previous topic,

we learned

that failures

can happen at any time.

Question:

After a crash,

how does the DBMS know

what transactions

were running?

How does it know

what changes

were already made?

How does it know

what to Undo

or

Redo?

The answer is

```text
Transaction Log.
```

Everything important

that happens

inside the database

is first recorded

inside a special file

called

the

```text
Log File.
```

Without Logs,

modern database recovery

would be almost impossible.

---

# What Is A Log?

A Log

is simply

a chronological record

of every important action

performed by transactions.

Think of it as

the database's

diary.

Example

```text
09:00

T1 Started

----------------

09:01

Updated Account A

----------------

09:02

Updated Account B

----------------

09:03

Committed
```

If

the database crashes,

the DBMS

reads this diary

to understand

what happened.

---

# Why Do We Need Logs?

Suppose

T1 transfers

₹2000.

Operations

```text
Read Account

↓

Subtract 2000

↓

Write New Balance

↓

Commit
```

Immediately

after writing,

the server crashes.

Question

After restart,

how will the DBMS know

whether

that transaction

had committed?

Without

some record,

it cannot know.

Logs solve

this exact problem.

---

# What Information Is Stored?

Whenever

a transaction

changes data,

the log stores

information like

```text
Transaction ID

↓

Data Item

↓

Old Value

↓

New Value
```

Example

Suppose

Account Balance

changes

from

```text
10000

↓

8000
```

The Log may contain

```text
<T1,

Account,

Old = 10000,

New = 8000>
```

Notice

both values

are stored.

Question

Why?

Because

the database

may later need

either

the old value

or

the new value.

---

# Common Log Records

A log

typically contains

records such as

```text
<T1 START>

<T1, A, 100, 80>

<T1, B, 50, 70>

<T1 COMMIT>
```

Let's understand them.

---

## START Record

```text
<T1 START>
```

Meaning

```text
Transaction T1

has begun.
```

---

## UPDATE Record

```text
<T1, A, 100, 80>
```

Meaning

```text
Transaction T1

changed

A

from

100

to

80.
```

Notice

both

old

and

new values

are saved.

---

## COMMIT Record

```text
<T1 COMMIT>
```

Meaning

```text
Transaction

completed successfully.
```

Its work

must survive

future crashes.

---

## ABORT Record

Sometimes

a transaction

fails.

The Log records

```text
<T2 ABORT>
```

Meaning

```text
Discard

all changes

made by T2.
```

---

# Example

Suppose

two transactions

execute.

The Log becomes

```text
<T1 START>

<T1,A,100,80>

<T2 START>

<T2,B,500,700>

<T1 COMMIT>

<T2 COMMIT>
```

Notice

the log

doesn't care

about tables.

It simply records

events

in the exact order

they occur.

---

# Why Store The Old Value?

Suppose

T1 updates

Salary

from

```text
50000

↓

60000
```

Before committing,

the server crashes.

Question

What should happen?

Since

T1 never committed,

the update

must disappear.

How?

The DBMS restores

the

old value

```text
50000
```

This is called

```text
Undo.
```

---

# Why Store The New Value?

Now suppose

T1

already committed,

but

the updated page

was never written

to disk

before

the crash.

After restart,

the DBMS

must apply

the update again.

How?

Using

the

new value

stored

inside the Log.

This is called

```text
Redo.
```

---

# Important Observation

Old Value

↓

Undo

-------------------

New Value

↓

Redo

This single idea

is the foundation

of database recovery.

---

# Immediate Update

Most modern databases

use

Immediate Update.

Meaning

the database

may write

modified pages

to disk

even

before

the transaction commits.

Question

Isn't that dangerous?

Yes.

That's exactly why

Undo

is required.

Because

an uncommitted transaction

might already

have modified

the disk.

---

# Deferred Update

Another approach

is

Deferred Update.

Here,

changes

are not written

to disk

until

Commit.

Question

Need Undo?

No.

Because

nothing reached disk.

Need Redo?

Yes.

Because

the committed changes

may still need

to be written

after a crash.

Deferred Update

is easier,

but generally

less efficient.

Most databases

prefer

Immediate Update.

---

# Interview Questions

### What is a Log?

```text
A file

that records

every important

transaction event

needed for recovery.
```

---

### Why are Logs important?

```text
They allow

the DBMS

to Undo

or

Redo

transactions

after failures.
```

---

### Why are both

Old

and

New values stored?

```text
Old Value

↓

Undo

New Value

↓

Redo
```

---

### What is Immediate Update?

```text
Database pages

may be written

before Commit.
```

---

### What is Deferred Update?

```text
Pages

are written

only after Commit.
```

---

# Mental Model

Imagine

editing

a Word document.

Before

every edit,

Microsoft Word

creates

an AutoSave record.

If

your laptop crashes,

Word

can restore

your work.

The Recovery Log

plays

exactly

the same role

inside a database.

---

# Bridge

Question:

Suppose

the DBMS

updates

the database page

first,

and

only later

writes

the Log.

Now imagine

a crash

occurs

between

those two operations.

The database

has changed,

but

there is

no record

of that change.

Recovery

becomes impossible.

To prevent this,

every DBMS follows

one golden rule

called

```text
Write-Ahead Logging (WAL).
```

WAL is arguably

the single most important rule

in the entire Recovery chapter.

# 4. Write-Ahead Logging (WAL)

In the previous topic,

we learned that

the DBMS keeps

a Log File

containing every important change.

Question:

Is merely keeping a log enough?

No.

The **order** in which the Log

and

the Database

are written

is extremely important.

This rule is called

```text
Write-Ahead Logging (WAL).
```

Almost every commercial database

(MySQL,

PostgreSQL,

Oracle,

SQL Server)

follows this rule.

---

# The Golden Rule

The rule is surprisingly simple.

```text
Before

any modified page

is written

to the database,

its Log Record

MUST already

be safely stored.
```

Or simply

```text
LOG FIRST

DATABASE LATER
```

Never the opposite.

---

# Why Is WAL Needed?

Suppose

Balance

changes

```text
10000

↓

8000
```

Imagine

the DBMS writes

the database page first.

```text
Disk

↓

Balance = 8000
```

Now,

before

writing the Log,

the system crashes.

Question

After restart,

how will the DBMS know

who changed

the balance?

It cannot.

The database changed,

but

the Log

doesn't know about it.

Recovery becomes impossible.

---

# Correct Order

The DBMS always performs

```text
Step 1

Write Log

↓

Step 2

Flush Log

to Stable Storage

↓

Step 3

Write Database Page
```

Now,

even if

the crash occurs,

the Log survives.

Recovery is still possible.

---

# Example

Transaction

updates

Salary

```text
50000

↓

60000
```

Correct sequence

```text
Create Log Record

↓

Write Log

↓

Flush Log

↓

Write Page

↓

Commit
```

Crash

can happen

after any step.

Recovery

still works

because

the Log

already exists.

---

# Stable Storage

Question

Where is the Log stored?

Not just

ordinary RAM.

The Log

must be written

to

Stable Storage.

Examples

```text
SSD

↓

Hard Disk

↓

Replicated Storage
```

Reason

RAM disappears

during crashes.

Stable Storage

survives.

---

# WAL Rule During Commit

There is another

important rule.

Before

the DBMS says

```text
COMMIT SUCCESSFUL
```

it must ensure

that

every Log Record

for that transaction

has already reached

Stable Storage.

Only then

may

Commit

be acknowledged.

This guarantees

Durability.

---

# WAL In One Diagram

```text
Transaction

↓

Modify Data

↓

Create Log Record

↓

Write Log

↓

Flush Log

↓

Write Database Page

↓

Commit
```

Never

```text
Database

↓

Log
```

Always

```text
Log

↓

Database
```

---

# Advantages

- Recovery becomes possible.
- Supports Undo.
- Supports Redo.
- Guarantees Durability.
- Used by almost every modern DBMS.

---

# Interview Questions

### What is WAL?

```text
A protocol

that requires

the Log Record

to be written

before

the corresponding

database page.
```

---

### Why is WAL important?

```text
Without WAL,

recovery

may become impossible

after a crash.
```

---

### Which is written first?

```text
Log

↓

Database Page.
```

---

# Mental Model

Imagine

writing

important notes.

Before

editing

the original notebook,

you first

photograph

the old page.

If something

goes wrong,

you still know

what was written.

The photograph

is the Log.

The notebook

is the Database.

---

# Bridge

Now that

the Log

is safely stored,

the DBMS can answer

two questions

after a crash.

```text
Transaction

Committed?

↓

Redo

----------------

Not Committed?

↓

Undo
```

Let's understand

both operations.

---

# 5. Undo And Redo

Recovery

is based

on only

two operations.

```text
Undo

Redo
```

Everything else

is built

on these.

---

# What Is Undo?

Undo means

```text
Reverse

the effects

of

an incomplete transaction.
```

Question

Why?

Because

it never committed.

The database

must behave

as though

that transaction

never happened.

---

# Example

Initial Balance

```text
10000
```

T1

updates

the balance

to

```text
8000
```

Before Commit,

the server crashes.

Log

contains

```text
Old = 10000

New = 8000
```

Recovery

reads

the Log

and restores

```text
10000
```

The transaction

disappears.

This is

Undo.

---

# Undo Uses

```text
Old Value.
```

Always remember

```text
Undo

↓

Old Value
```

---

# What Is Redo?

Redo means

```text
Reapply

the effects

of

a committed transaction.
```

Question

Why?

Because

sometimes

the transaction

committed,

but

its updated pages

never reached

the database.

After restart,

those changes

must be applied again.

---

# Example

T1

changes

Salary

```text
50000

↓

60000
```

Log

already contains

```text
Old = 50000

New = 60000
```

Transaction

commits.

Immediately

after Commit,

the server crashes.

The updated page

was never written.

Recovery

reads

the Log

and writes

```text
60000
```

again.

This is

Redo.

---

# Redo Uses

```text
New Value.
```

Remember

```text
Redo

↓

New Value
```

---

# Easy Trick

```text
Undo

↓

Go Back

↓

Old Value

-------------------

Redo

↓

Go Forward

↓

New Value
```

---

# Recovery Cases

Question

After a crash,

how does the DBMS decide

whether

Undo

or

Redo

is required?

Very simple.

---

## Case 1

Transaction

did NOT Commit.

```text
Undo.
```

Reason

Half-finished work

must disappear.

---

## Case 2

Transaction

Committed.

Database Page

already written.

```text
Nothing.
```

Everything

is already correct.

---

## Case 3

Transaction

Committed.

Page

NOT written.

```text
Redo.
```

The committed work

must be restored.

---

# Decision Table

| Transaction | Page Written? | Recovery |
|-------------|---------------|----------|
| Not Committed | Yes / No | Undo |
| Committed | Yes | Nothing |
| Committed | No | Redo |

This table

alone

answers

most interview questions.

---

# Why Immediate Update Needs Both

Remember

Immediate Update.

Pages

may reach disk

before Commit.

Therefore

two situations

become possible.

```text
Uncommitted Page

↓

Undo

------------------

Committed

Not Written

↓

Redo
```

Hence,

Immediate Update

requires

both

Undo

and

Redo.

---

# Deferred Update

Pages

are written

only after Commit.

Therefore

```text
Undo

Not Needed

Redo

Needed
```

Because

uncommitted data

never reaches

the database.

---

# Check Your Understanding

Suppose

Log

contains

```text
<T1 START>

<T1,A,100,80>

CRASH
```

Recovery?

```text
Undo.
```

---

Another

```text
<T1 START>

<T1,A,100,80>

<T1 COMMIT>

CRASH
```

Recovery?

```text
Redo

(if page wasn't written)

otherwise

Nothing.
```

---

# Interview Questions

### What is Undo?

```text
Restoring

the old value

of

an uncommitted transaction.
```

---

### What is Redo?

```text
Reapplying

the new value

of

a committed transaction.
```

---

### Which value

does Undo use?

```text
Old Value.
```

---

### Which value

does Redo use?

```text
New Value.
```

---

### Immediate Update

requires?

```text
Undo

+

Redo.
```

---

### Deferred Update

requires?

```text
Redo only.
```

---

# Mental Model

Suppose

you are

editing

a document.

Undo

means

pressing

Ctrl + Z.

Redo

means

pressing

Ctrl + Y.

Database Recovery

uses exactly

the same idea,

except

it works

using

the Transaction Log.

---

# Bridge

Question

Suppose

the database

contains

millions

of Log Records.

After every crash,

should the DBMS

scan

the entire Log

from the beginning?

That would be

extremely slow.

To solve this,

the DBMS

creates

special markers

called

```text
Checkpoints.
```
# 6. Checkpoints

So far,

we learned

that after a crash,

the DBMS uses

the Transaction Log

to decide

what should be

Undone

or

Redone.

Question:

Suppose

the Log contains

10 million records.

Should the DBMS

start reading

from

the very beginning

after every crash?

```text
No.
```

That would make

recovery

extremely slow.

The solution is

```text
Checkpoint.
```

---

# What Is A Checkpoint?

A Checkpoint

is simply

a marker

placed inside

the Transaction Log.

It tells the DBMS

```text
Everything

before this point

has already been

handled safely.
```

Therefore,

after a crash,

the DBMS

usually starts recovery

from

the latest Checkpoint,

instead of

reading

the entire Log.

---

# Why Do We Need Checkpoints?

Suppose

the database

has been running

for

6 months.

The Log now contains

```text
50 Million Records.
```

Server crashes.

Without Checkpoints,

Recovery would do

```text
Read

Record 1

↓

Record 2

↓

Record 3

↓

...

↓

Record 50,000,000
```

Even though

almost all

old transactions

were already completed.

Huge waste of time.

---

# Idea Behind Checkpoints

Every few minutes,

the DBMS performs

a Checkpoint.

During a Checkpoint,

it ensures

```text
Dirty Pages

↓

Written to Disk

------------------

Log

↓

Flushed

to Stable Storage

------------------

Checkpoint Record

↓

Written
```

Now,

everything

before this Checkpoint

is considered

safe.

---

# Example

Log

```text
T1 START

↓

T1 COMMIT

↓

Checkpoint

↓

T2 START

↓

T2 UPDATE

↓

Crash
```

Question

After restart,

should the DBMS

begin

from T1?

No.

Recovery starts

from

the latest

Checkpoint.

Much faster.

---

# What Happens During A Checkpoint?

A simplified sequence

looks like this.

```text
Stop accepting

new updates

(for a very short time)

↓

Write modified pages

to disk

↓

Flush Log

↓

Write

<Checkpoint>

↓

Resume
```

Modern databases

optimize this process,

so users

rarely notice it.

---

# Advantages

- Faster recovery
- Smaller Log scan
- Better performance
- Less restart time

---

# Interview Questions

### What is a Checkpoint?

```text
A marker

in the Log

that allows

Recovery

to begin

from a recent point

instead of

the beginning.
```

---

### Why are Checkpoints used?

```text
To reduce

Recovery Time.
```

---

### Does a Checkpoint

replace the Log?

```text
No.

It only reduces

how much of the Log

must be scanned.
```

---

# Mental Model

Imagine

reading

a huge book.

Instead of

remembering

the last page

you read,

you place

a bookmark.

Next time,

you continue

from

the bookmark,

not

Page 1.

A Checkpoint

is exactly

that bookmark

inside

the Transaction Log.

---

# Bridge

Logs,

Undo,

Redo,

and Checkpoints

are used

by almost

every modern DBMS.

However,

there exists

another recovery technique

that works

without

Undo

or

Redo Logs.

It is called

```text
Shadow Paging.
```

---

# 7. Shadow Paging

Shadow Paging

is another

Recovery technique.

Unlike

Log-Based Recovery,

it does **not**

maintain

Undo

or

Redo Logs.

Instead,

it works

by maintaining

two copies

of the database.

---

# Main Idea

Initially,

both pointers

refer

to

the same pages.

```text
Database

↓

Page Table

↓

Pages
```

One copy

is called

```text
Current Page Table
```

The other

is called

```text
Shadow Page Table.
```

The Shadow copy

is **never modified**.

---

# How Updates Work

Suppose

a transaction

updates

Page 5.

Instead of

changing

the original page,

the DBMS

creates

a new copy.

```text
Original Page

↓

Copy

↓

Modify Copy
```

The Shadow Page

remains untouched.

---

# Commit

If

the transaction

commits,

the DBMS

simply changes

one pointer.

Old Pointer

```text
↓

Old Pages
```

becomes

```text
↓

New Pages
```

Commit

takes

almost

constant time.

---

# Crash Before Commit

Suppose

the system crashes

before Commit.

Question

What happens?

Very simple.

The DBMS ignores

all modified pages

and

continues using

the Shadow Pages.

Recovery

is almost immediate.

No Undo.

No Redo.

---

# Advantages

- Very fast recovery.
- No Undo Logs.
- No Redo Logs.
- Simple concept.

---

# Disadvantages

- Copying pages

is expensive.
- Poor scalability.
- Fragmentation.
- Difficult for

very large databases.

Because of these reasons,

modern databases

rarely use

pure Shadow Paging.

Log-Based Recovery

is much more common.

---

# Comparison

| Log-Based Recovery | Shadow Paging |
|--------------------|---------------|
| Uses Logs | No Logs |
| Uses Undo/Redo | No Undo/Redo |
| Widely Used | Rare |
| Better for Large Databases | Poor for Large Databases |

---

# Interview Questions

### What is Shadow Paging?

```text
A recovery technique

that maintains

a Shadow Copy

of database pages

instead of

using Logs.
```

---

### Does Shadow Paging

use Undo?

```text
No.
```

---

### Which recovery technique

is used

by modern DBMS?

```text
Log-Based Recovery.
```

---

# Final Revision

```text
Failures

↓

Transaction Failure

↓

Undo

-------------------

System Crash

↓

Undo

+

Redo

-------------------

Media Failure

↓

Backup

+

Logs

==================

Recovery

↓

Transaction Log

↓

Write-Ahead Logging

↓

Undo

↓

Redo

↓

Checkpoints

↓

Shadow Paging
```

---

# Interview Summary

### Recovery

```text
Restores

the database

after failures.
```

---

### WAL

```text
Write Log

Before

Writing Database.
```

---

### Undo

```text
Restore

Old Value.
```

---

### Redo

```text
Apply

New Value.
```

---

### Checkpoint

```text
Reduces

Recovery Time.
```

---

### Shadow Paging

```text
Recovery

without

Undo/Redo Logs.
```

---

# Mental Model

Think of

Google Docs.

Every edit

is automatically saved

(history),

which acts

like

the Transaction Log.

Occasionally,

Google creates

a recovery snapshot,

similar to

a Checkpoint.

If something goes wrong,

it restores

from

the latest safe state

instead of

replaying

every single edit

since the document

was created.

That is exactly

how modern database

Recovery Systems work.

---
