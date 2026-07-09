# DBMS 07 - Concurrency Control

# Navigation
- [1. Why Do We Need Concurrency Control?](#1-why-do-we-need-concurrency-control)
- [2. Lock-Based Protocols](#2-lock-based-protocols)
  - [2.1 Shared Lock (S Lock)](#21-shared-lock-s-lock)
  - [2.2 Exclusive Lock (X Lock)](#22-exclusive-lock-x-lock)
- [3. Two-Phase Locking (2PL)](#3-two-phase-locking-2pl)
- [4. Timestamp Ordering Protocol](#5-timestamp-ordering-protocol)
- [5. Recoverable Schedules](#7-recoverable-schedules)
- [Revision](#dbms07-revision)

  # 1. Why Do We Need Concurrency Control?

In DBMS06,

we learned

that executing transactions concurrently

makes the database much faster.

Instead of waiting,

multiple users

can access the database

at the same time.

This improves

- CPU utilization
- Throughput
- Response time

Modern databases

therefore prefer

Concurrent Schedules.

---

## Recall Concurrent Execution

Suppose

two ATM machines

access

the same account.

Initial Balance

```text
₹10,000
```

ATM 1

withdraws

₹2,000.

ATM 2

withdraws

₹3,000.

Both read

the balance

at almost

the same time.

Without coordination,

both believe

the balance is

```text
₹10,000
```

Final balance

may become

```text
₹8,000
```

or

```text
₹7,000
```

instead of

```text
₹5,000.
```

The database

is now incorrect.

---

## Problems We Studied

Concurrent execution can produce

```text
Lost Update

Dirty Read

Non-Repeatable Read

Phantom Read
```

These problems

are called

Concurrency Problems.

Question:

Should we stop

using concurrent execution?

No.

Concurrent execution

is too valuable

to give up.

Instead,

we need

a way

to safely

execute

multiple transactions

at the same time.

---

# The Core Idea

Imagine

a classroom.

One notebook

is kept

on the teacher's desk.

Question:

Can

ten students

write

on the same notebook

at exactly

the same time?

No.

Pages

would overlap,

sentences

would be erased,

and

the notebook

would become unreadable.

Instead,

the teacher says

```text
Wait.

One student

writes first.

Others wait.
```

Or

```text
Everyone may read,

but only

one person

may write.
```

This simple idea

is exactly

what databases use.

---

# What Is Concurrency Control?

Concurrency Control

is the collection

of techniques

used by the DBMS

to ensure

that multiple transactions

can execute simultaneously

without

corrupting

the database.

Notice

the goal is NOT

to stop

concurrent execution.

The goal is

```text
Safe

Concurrent Execution.
```

---

# Think About A Bank

Suppose

100 customers

use UPI

at the same time.

Question:

Can the bank

allow only

one customer

per minute?

Of course not.

The bank

must process

thousands of transactions

simultaneously.

At the same time,

it must ensure

```text
No money disappears.

No money is duplicated.

No incorrect balance appears.
```

This balance between

speed

and

correctness

is achieved using

Concurrency Control.

---

# The Goal Of Concurrency Control

An ideal DBMS wants

both.

```text
High Performance

+

Correct Data
```

Not

```text
Fast

but Wrong.
```

Also not

```text
Correct

but Extremely Slow.
```

Concurrency Control

tries to achieve

both simultaneously.

---

# How Does A DBMS Do This?

It uses

several techniques.

The most important are

```text
Locks

↓

Two Phase Locking

↓

Timestamp Ordering

↓

Validation Protocols
```

Among these,

Locking

is by far

the most common.

Almost every

commercial DBMS

uses locks.

So,

we'll study

Locking first.

---

# Interview Questions

### Why do we need Concurrency Control?

```text
To allow

multiple transactions

to execute safely

without

creating

data inconsistency.
```

---

### Does Concurrency Control stop concurrent execution?

```text
No.

It allows

concurrent execution,

but in a controlled manner.
```

---

### What problems does it solve?

```text
Lost Update

Dirty Read

Non-Repeatable Read

Phantom Read
```

---

# Mental Model

Imagine

a public washroom.

Everyone

can enter

different cabins

at the same time.

But

two people

cannot occupy

the same cabin

simultaneously.

The door lock

ensures

safe usage.

A database lock

works

exactly

the same way.

---

# Bridge

Question:

How does

the DBMS

actually stop

two transactions

from modifying

the same data

at the same time?

The answer is

```text
Locks.
```

A lock

is the simplest

and most widely used

Concurrency Control mechanism.

# 2. Lock-Based Protocols

In the previous topic,

we learned that

Concurrent Execution

can cause problems like

- Lost Update
- Dirty Read
- Non-Repeatable Read
- Phantom Read

Question:

How can the DBMS prevent these problems?

The simplest solution is surprisingly intuitive.

```text
Lock the data
before using it.
```

Just like we lock a room before entering,

a DBMS locks data before allowing transactions to access it.

This idea forms the basis of

```text
Lock-Based Concurrency Control.
```

---

# What Is A Lock?

A **Lock** is a permission granted by the DBMS.

Before a transaction can

- Read data
- Update data
- Delete data

it must first obtain the appropriate lock.

Think of it as asking permission.

```text
Transaction

↓

Request Lock

↓

Lock Granted?

↓

YES → Access Data

NO  → Wait
```

---

# Why Do We Need Locks?

Suppose

Initial Balance

```text
₹10,000
```

Two ATM machines

try to withdraw money simultaneously.

Without Locks

```text
ATM 1

Read 10000

-----------------

ATM 2

Read 10000

-----------------

ATM 1

Write 8000

-----------------

ATM 2

Write 7000
```

Final Balance

```text
₹7000
```

Correct answer

```text
₹5000
```

One transaction's work

was overwritten.

This is

```text
Lost Update.
```

---

Now suppose

the DBMS places

a lock

on the account.

```text
ATM 1

Acquire Lock

↓

Read

↓

Update

↓

Write

↓

Release Lock
```

Meanwhile,

ATM 2 requests

the same lock.

The DBMS replies

```text
Wait...
```

Only after

ATM 1 releases the lock

does ATM 2 continue.

Final Balance

```text
₹5000
```

Correct.

---

# Lock Granularity

Question:

What exactly

can a DBMS lock?

Different databases

can lock

different levels.

```text
Entire Database

↓

Table

↓

Page

↓

Row (Record)

↓

Column (Rare)
```

Most modern databases

(MySQL InnoDB,

PostgreSQL,

Oracle)

primarily use

**Row-Level Locks**,

because they allow

maximum concurrency.

---

## Example

Suppose

Employee table

| ID | Name | Salary |
|----|------|--------|
|1|Alice|50000|
|2|Bob|60000|

If

Transaction T1

updates Alice,

there is no reason

to stop

another transaction

from updating Bob.

Therefore,

locking only

the required row

gives much better performance.

---

# Types Of Locks

There are two

fundamental lock types.

```text
1.

Shared Lock (S Lock)

2.

Exclusive Lock (X Lock)
```

Everything else

is built on these.

=====================================================

# 2.1 Shared Lock (S Lock)

A Shared Lock

is used

when a transaction

only wants

to read data.

Notice

reading

doesn't change

the database.

Therefore,

multiple users

can safely

read

the same row

at the same time.

---

## Example

Suppose

three users

check

their bank balance.

None of them

modifies anything.

All three

may read

simultaneously.

```text
Account

↓

T1 Read

↓

T2 Read

↓

T3 Read
```

No conflict.

---

## Why Is It Called "Shared"?

Because

the same lock

can be shared

among multiple transactions.

Visual

```text
Row A

──────────────

T1  Read ✔

T2  Read ✔

T3  Read ✔
```

Everyone reads.

Nobody writes.

Perfectly safe.

---

## SQL Example

```sql
SELECT *

FROM Employee

WHERE ID = 10;
```

The DBMS

may place

a Shared Lock

on that row

while reading.

---

# Characteristics

✔ Multiple transactions

can hold

Shared Locks

on the same data.

✔ Reading

never blocks

another read.

✔ Reading

does block

certain writes

(as we'll see shortly).

---

=====================================================

# 2.2 Exclusive Lock (X Lock)

Suppose

a transaction

wants to

modify data.

Example

```sql
UPDATE Employee

SET Salary = 70000

WHERE ID = 10;
```

Now

allowing another transaction

to read

or write

the same row

may produce

incorrect results.

Therefore,

the DBMS

places

an

```text
Exclusive Lock.
```

---

## Main Idea

Exclusive means

```text
Only me.
```

If one transaction

owns

an Exclusive Lock,

no other transaction

may

- Read
- Write

that data

until

the lock

is released.

---

## Example

Suppose

T1

updates

Alice's salary.

```text
Acquire X Lock

↓

Update Salary

↓

Commit

↓

Release Lock
```

Meanwhile

T2 tries

to read

Alice's salary.

The DBMS says

```text
Wait.
```

Only after

T1 commits

does

T2 continue.

---

## Why Even Block Reading?

Imagine

the salary

has been changed

but

the transaction

later rolls back.

If another transaction

had already read

that temporary value,

we get

```text
Dirty Read.
```

Exclusive Locks

prevent this.

---

# Characteristics

✔ Only one transaction

can hold

an Exclusive Lock.

✔ Blocks

all other Reads.

✔ Blocks

all other Writes.

✔ Prevents

Dirty Reads

and

Lost Updates.

---

# Shared vs Exclusive

| Property | Shared Lock | Exclusive Lock |
|-----------|-------------|----------------|
| Purpose | Read | Write |
| Multiple Transactions Allowed | ✅ Yes | ❌ No |
| Allows Reading | ✅ Yes | Only Owner |
| Allows Writing | ❌ No | Only Owner |

---

# Lock Compatibility

This is one of

the most important tables

for interviews.

Suppose

Transaction T1

already owns

a lock.

Question:

Can T2

also obtain

another lock

on the same row?

| Existing Lock | Requested Lock | Allowed? |
|---------------|----------------|----------|
| Shared | Shared | ✅ Yes |
| Shared | Exclusive | ❌ No |
| Exclusive | Shared | ❌ No |
| Exclusive | Exclusive | ❌ No |

Easy way

to remember

```text
Read + Read

↓

Allowed

-------------------

Anything involving

Write

↓

Blocked
```

Since

Exclusive Locks

represent

writing,

they cannot coexist

with

any other lock.

---

# Complete Example

Suppose

T1 executes

```sql
SELECT Balance

FROM Account

WHERE ID = 1;
```

Shared Lock

acquired.

Now

T2 also performs

a SELECT.

Allowed.

Both read together.

---

Suppose

instead

T2 executes

```sql
UPDATE Account

SET Balance = Balance - 1000;
```

The DBMS

must wait

until

all Shared Locks

are released.

Only then

can it grant

the Exclusive Lock.

---

# Real-World Analogy

Think of

a library book.

Many students

may sit

and read

the same reference book.

That is

a

Shared Lock.

But

if someone

takes the book home

to write notes

or modify it,

nobody else

can access it.

That is

an

Exclusive Lock.

---

# Interview Questions

### What is a Lock?

```text
A mechanism used by the DBMS

to control concurrent access

to data.
```

---

### Why are Locks needed?

```text
To prevent

concurrency problems

such as

Lost Updates

and

Dirty Reads.
```

---

### What is a Shared Lock?

```text
A Read Lock.

Multiple transactions

can hold it simultaneously.
```

---

### What is an Exclusive Lock?

```text
A Write Lock.

Only one transaction

may hold it,

and it blocks

all other reads

and writes.
```

---

### Which lock allows multiple transactions?

```text
Shared Lock.
```

---

### Which lock is used for UPDATE?

```text
Exclusive Lock.
```

---

# Mental Model

Imagine

a whiteboard

in a classroom.

Students

may stand around

and read

the whiteboard

together.

That's

a Shared Lock.

Now imagine

the teacher

starts writing

on the whiteboard.

Nobody else

should read

or write

while the teacher

is still changing it.

That's

an Exclusive Lock.

---

# Bridge

Locks solve

many concurrency problems.

However,

another question arises.

Suppose

a transaction

acquires

one lock,

then another,

then another...

Question:

Can it

release locks

whenever it wants?

If yes,

the database

may still become inconsistent.

To solve this,

DBMS follows

a strict rule called

```text
Two-Phase Locking (2PL).
```

```text

so A, B logically inside a transaction is 1 operation problem occurs when A is done b is pending and other T changes B.
so 2pl says these logically 1 group of queries should have 2 phases aquiring of locks and relesing of locks insted of each query having a lock and unlock !

better way :
A transaction is one logical unit of work. Even if it contains many SQL queries, it should first acquire all the locks it needs (Growing Phase).
Only after it has finished acquiring locks should it start releasing them (Shrinking Phase). It should not lock–unlock after every query,
because that allows other transactions to observe or interfere with a partially completed transaction.

Q : so is it like , all logically 1 operation i.e set of queries as 2pl later some other set of queries 2pl inside a transaction or entrie transaction as 2pl ?
A : 2PL is applied to the entire transaction, not to arbitrary groups of queries inside a transaction.


Growing Phase:
    Lock(A)
    Lock(B)
    Lock(C)
    Lock(D)

    (Locks can also be acquired as needed.)

Shrinking Phase:
    Unlock(...)
    Unlock(...)
    Unlock(...)



with concurrency independent transactions can without any problem do what ever, like different rows, when it comes to same row the lock aquiring matters,
that too in 2pl fashion so that entire transaction is logically 1 unit , in A, B example, after we perform A we can release A and others can use A but not B !
i got this ! 

```

````md
### Example: T1 transfers ₹50 from A → B

Transaction T1:

```text
A = A - 50
B = B + 50
```

**Basic 2PL (safe):**

```text
Lock(A)
Lock(B)

Update(A)
Update(B)

Unlock(A)
Unlock(B)
```

Both locks are acquired before any lock is released, so no other transaction can access the data while T1 is only partially complete.

### Why not this?

```text
Lock(A)
Lock(B)

Update(A)
Unlock(A)

Update(B)
Unlock(B)
```

- ✅ **Basic 2PL:** Valid, because after the first unlock, no new locks are acquired.
- ❌ **Strict 2PL (used by most DBMSs):** Not allowed, because `A` is unlocked before `COMMIT`. Another transaction could read or modify `A`
while T1 hasn't finished (or before rollback if T1 crashes), causing dirty reads or inconsistent results.

**Rule to remember:**
- **Basic 2PL:** No new locks after the first unlock.
- **Strict 2PL:** Hold write locks until `COMMIT`.
````



# 3. Two-Phase Locking (2PL)

In the previous topic,

we learned that a DBMS uses **Locks** to prevent concurrent transactions from corrupting data.

Question:

If every transaction acquires locks, can it release them whenever it wants?

It might seem reasonable.

Example

```text
Acquire Lock(A)

↓

Read A

↓

Release Lock(A)

↓

Acquire Lock(B)

↓

Update B
```

Unfortunately,

this can still produce incorrect schedules.

Simply using locks is **not enough**.

The DBMS also needs rules about **when locks can be acquired and released**.

This is exactly what **Two-Phase Locking (2PL)** provides.

---

# Why Normal Locking Is Not Enough

Suppose we have two transactions.

Transaction T1

```text
Read(A)

↓

Update(A)

↓

Read(B)

↓

Update(B)
```

Transaction T2

```text
Read(A)

↓

Update(A)
```

Imagine T1 does this:

```text
Lock(A)

↓

Update(A)

↓

Unlock(A)

↓

Lock(B)
```

The moment T1 releases A,

T2 acquires it.

Now T2 updates A.

Meanwhile,

T1 is still running and later updates B.

The final execution becomes

```text
T1 partially executes

↓

T2 executes

↓

T1 continues
```

The operations become interleaved in a dangerous way.

Even though both transactions used locks,

the schedule may no longer behave like a serial execution.

---

# The Main Idea

The DBMS introduces one simple rule.

```text
Keep acquiring locks

until

you no longer need any new locks.

Only then

start releasing them.
```

In other words,

a transaction cannot

```text
Acquire

↓

Release

↓

Acquire Again
```

Once it starts releasing locks,

it is **never allowed to acquire another lock.**

This rule is called

```text
Two-Phase Locking.
```

---

# Why Is It Called "Two-Phase"?

Every transaction is divided into exactly two phases.

```text
Growing Phase

↓

Shrinking Phase
```

Think of climbing a hill.

```text
Go Up

↓

Reach Peak

↓

Come Down
```

You cannot

```text
Go Up

↓

Come Down

↓

Go Up Again
```

Exactly the same rule applies to locks.

---

# Phase 1 - Growing Phase

During the Growing Phase,

the transaction

may

```text
Acquire Locks
```

But

it is **not allowed**

to release any lock.

Visual

```text
Acquire A ✔

↓

Acquire B ✔

↓

Acquire C ✔

↓

Acquire D ✔
```

Everything is being collected.

Nothing is returned.

---

# Phase 2 - Shrinking Phase

Once the transaction releases its **first lock**,

it enters the Shrinking Phase.

Now it may

```text
Release Locks ✔
```

But it is **never allowed**

to acquire another lock.

Visual

```text
Release D ✔

↓

Release B ✔

↓

Release A ✔
```

No new locks can be requested.

---

# The Golden Rule

Remember only this.

```text
Growing Phase

Acquire ✔

Release ✘

-------------------

Shrinking Phase

Acquire ✘

Release ✔
```

This single rule defines 2PL.

---

# Valid Example

```text
Acquire(A)

↓

Acquire(B)

↓

Acquire(C)

↓

Release(C)

↓

Release(B)

↓

Release(A)
```

Notice

all acquisitions happen first.

After the first release,

no new locks are acquired.

This satisfies 2PL.

---

# Invalid Example

```text
Acquire(A)

↓

Acquire(B)

↓

Release(A)

↓

Acquire(C)

↓

Release(B)

↓

Release(C)
```

Question:

Why is this invalid?

Because after entering

the Shrinking Phase,

the transaction acquired

a new lock

on C.

That violates

Two-Phase Locking.

---

# Lock Point

An interesting concept in 2PL is the

```text
Lock Point.
```

The Lock Point is

the exact moment

when a transaction acquires

its **last lock**.

Visual

```text
Acquire(A)

↓

Acquire(B)

↓

Acquire(C)

↓

⭐ Lock Point

↓

Release(C)

↓

Release(B)

↓

Release(A)
```

Everything before the Lock Point

belongs to

the Growing Phase.

Everything after

belongs to

the Shrinking Phase.

---


````md
# 2PL, Strict 2PL & WAL (Quick Revision)

## Why is unlocking early bad?
If a transaction unlocks a resource before it finishes, another transaction can access **half-completed data**, leading to problems like **dirty reads**.

---

## Why can't we acquire a new lock after unlocking one?
There is **no simple practical reason** like dirty reads.

The rule exists so that the execution remains **conflict serializable**.

> **Conflict Serializability:** Even though transactions execute concurrently, the final result should be the same as if they had executed **one after another** (serially).

---

## Basic 2PL
- **Growing Phase:** Only acquire locks.
- **Shrinking Phase:** Only release locks.
- **Rule:** Once the first lock is released, **no new locks can be acquired.**

---

## Strict 2PL
- Follows Basic 2PL.
- Additionally, **all write locks are held until COMMIT**.
- Prevents:
  - Dirty Reads
  - Cascading Rollbacks
- Simplifies crash recovery.

---

## Example: Transfer ₹50 from A → B

```text
BEGIN

Lock(A)
A = A - 50

Lock(B)   // Wait if B is locked

B = B + 50

COMMIT

Unlock(A)
Unlock(B)
```

The updates happen immediately after acquiring each lock, but **locks are released only after COMMIT**.

---

# Crash Recovery

### Crash before COMMIT
- Transaction is **UNDO**ne.
- Database returns to the old state.

### Crash after COMMIT
- Transaction is permanent.
- If database pages weren't written yet, DBMS **REDO**s the committed changes using the log.

---

# Why use Logs instead of directly updating the database?

Logs are **append-only (sequential)**, while database pages are **scattered**.

```text
Log:
Append → Append → Append ✅ (Fast)

Database:
Update Page 5
Update Page 827
Update Page 42 ❌ (Slower)
```

Because sequential appends are much faster, DBMS:
1. Writes the log to disk first (WAL).
2. Updates the actual database pages later in batches.

**Memory Trick:**  
**Sequential append = Fast** 🚀  
**Random page updates = Slower** 🗄️
````



# Why Does 2PL Work?

The goal of 2PL is

to make concurrent execution

behave

like

serial execution.

Instead of

randomly releasing locks,

transactions hold them

until they have acquired

everything they need.

This greatly reduces

conflicts between transactions.

---

# Does 2PL Eliminate Every Problem?

No.

2PL solves many problems,

including

- Lost Update
- Dirty Read
- Many inconsistent schedules

However,

it introduces

a new problem.

```text
Deadlock.
```

---

# Example Of Deadlock

Transaction T1

```text
Lock(A)

↓

Waiting for B
```

Transaction T2

```text
Lock(B)

↓

Waiting for A
```

Now,

both transactions

wait forever.

Neither can continue.

This situation is called

```text
Deadlock.
```

We'll study it next.

---

# Types Of Two-Phase Locking

In practice,

DBMS uses different versions of 2PL.

## Basic 2PL

Follows only

the Growing and Shrinking rules.

Locks may be released

before the transaction commits.

---

## Strict 2PL ⭐ (Most Important)

Exclusive Locks

are held

until

COMMIT

or

ROLLBACK.

Visual

```text
Acquire X Lock

↓

Execute

↓

Execute

↓

COMMIT

↓

Release Lock
```

This prevents

Dirty Reads

and simplifies recovery.

Most commercial databases

use Strict 2PL.

---

## Rigorous 2PL

Even Shared Locks

are held

until COMMIT.

Both

Shared

and

Exclusive

locks

are released together

after the transaction finishes.

This is even stricter

than Strict 2PL.

---

# Comparison

| Protocol | Shared Locks | Exclusive Locks |
|----------|--------------|-----------------|
| Basic 2PL | May release early | May release early |
| Strict 2PL | May release early | Released only after Commit |
| Rigorous 2PL | Released after Commit | Released after Commit |

For interviews,

knowing

Basic

and

Strict 2PL

is usually sufficient.

---

# Advantages Of 2PL

- Produces conflict-serializable schedules.
- Prevents many concurrency problems.
- Easy to understand.
- Widely used in commercial DBMS.

---

# Disadvantages Of 2PL

- Transactions may wait longer.
- Can reduce concurrency.
- Can create deadlocks.
- Requires lock management overhead.

---

# Interview Questions

### What is Two-Phase Locking?

```text
A locking protocol in which

a transaction first acquires locks

(Growing Phase)

and later releases locks

(Shrinking Phase),

without acquiring new locks

after releasing the first one.
```

---

### Why is it called Two-Phase Locking?

```text
Because every transaction

has two phases:

1. Growing Phase

2. Shrinking Phase
```

---

### What is the Lock Point?

```text
The moment

a transaction acquires

its last lock.
```

---

### Which version of 2PL is most commonly used?

```text
Strict Two-Phase Locking.
```

---

### Does 2PL guarantee serializability?

```text
Yes.

It guarantees

Conflict Serializability.
```

---

### What major problem can 2PL introduce?

```text
Deadlock.
```

---

# Mental Model

Imagine entering

a supermarket.

During shopping,

you only

put items

into your cart.

```text
Growing Phase
```

Once you reach

the billing counter,

you stop picking new items.

Now,

you only remove them

for billing.

```text
Shrinking Phase
```

You never

go back into the store

to pick more items

after billing starts.

Two-Phase Locking

works exactly the same way.

---

# Bridge

Locks solved

concurrency problems.

2PL made locking

safe and predictable.

However,

transactions can now

wait for each other.

Sometimes,

they wait forever.

This situation is called

```text
Deadlock.
```

Deadlocks are one of the most frequently asked DBMS interview topics and naturally follow from 2PL.

Concurrent Execution

↓

Locks

↓

2PL

↓

Deadlocks (Side Effect of 2PL)

# 5. Timestamp Ordering Protocol

So far,

every concurrency control technique we studied relied on **Locks**.

```text
Transaction

↓

Acquire Lock

↓

Read / Write

↓

Release Lock
```

Locks work well.

However,

they introduce new problems:

- Waiting
- Blocking
- Deadlocks

Question:

Can a DBMS avoid all of these?

```text
Yes.
```

Instead of making transactions wait,

the DBMS can decide

**which transaction gets priority**

using timestamps.

This approach is called

```text
Timestamp Ordering Protocol (TO).
```

---

# The Main Idea

Every transaction

receives a unique timestamp

when it begins.

Example

```text
T1 → Timestamp = 5

T2 → Timestamp = 8

T3 → Timestamp = 11
```

Smaller timestamp

means

```text
Older Transaction
```

Larger timestamp

means

```text
Newer Transaction
```

The DBMS always tries

to execute transactions

in timestamp order.

---

# Real-Life Analogy

Imagine

customers

standing in a queue.

Each customer

receives

a token.

```text
101

102

103

104
```

Question

Who gets served first?

Obviously,

Token 101.

Nobody can jump ahead.

Timestamp Ordering

works exactly

the same way.

The timestamp

acts like

a token number.

---

# Why Do We Need This?

Suppose

two transactions

want to update

the same account.

```text
T1

Timestamp = 5

-------------------

T2

Timestamp = 10
```

Question

If T2 updates first,

should T1 still update later?

No.

That would violate

timestamp order.

Instead,

the DBMS aborts

the younger transaction

or rejects the conflicting operation,

depending on the protocol.

---

# Read Timestamp (RTS)

Every data item

stores

the timestamp

of the **last successful read**.

Example

Suppose

Row A

contains

```text
RTS(A) = 15
```

Meaning

```text
The newest transaction

that successfully read A

had timestamp 15.
```

---

# Write Timestamp (WTS)

Similarly,

every data item

stores

the timestamp

of the **last successful write**.

Example

```text
WTS(A) = 12
```

Meaning

```text
The newest transaction

that modified A

had timestamp 12.
```

Every row

therefore keeps

two timestamps.

```text
Row A

↓

RTS

↓

WTS
```

---

# How Read Operations Work

Suppose

Transaction T

wants to read

Row A.

The DBMS compares

```text
TS(T)

with

WTS(A)
```

### Case 1

```text
TS(T)

≥

WTS(A)
```

Safe.

The read is allowed.

The DBMS updates

```text
RTS(A)
```

if necessary.

---

### Case 2

```text
TS(T)

<

WTS(A)
```

Problem.

A newer transaction

has already modified

the row.

Allowing

the older transaction

to read

would violate

timestamp order.

The transaction

is aborted

and restarted.

---

# How Write Operations Work

Suppose

Transaction T

wants to write

Row A.

The DBMS checks

two things.

```text
TS(T)

vs

RTS(A)

and

WTS(A)
```

---

## Rule 1

If

```text
TS(T)

<

RTS(A)
```

Abort.

Reason

A newer transaction

has already read

this value.

Allowing

an older write

would make

that previous read

incorrect.

---

## Rule 2

If

```text
TS(T)

<

WTS(A)
```

Abort.

Reason

A newer transaction

has already written

the row.

We cannot

go backwards

in time.

---

## Otherwise

The write

is allowed.

The DBMS updates

```text
WTS(A)
```

to the transaction's timestamp.

---

# Example

Suppose

```text
WTS(A) = 20
```

Transaction

```text
T1

Timestamp = 15
```

tries to write A.

Question

Should it succeed?

No.

A newer transaction

(timestamp 20)

already modified

the row.

The database

cannot allow

an older transaction

to overwrite

newer data.

T1

is aborted.

---

# Why Doesn't Timestamp Ordering Deadlock?

Notice

transactions

never wait.

Either

```text
Operation Allowed
```

or

```text
Transaction Aborted
```

Nobody blocks.

Nobody waits.

Therefore

```text
Deadlocks

cannot occur.
```

This is

the biggest advantage

of Timestamp Ordering.

---

# Advantages

- Deadlock-free
- No waiting
- Simple ordering
- High concurrency
- Ensures serializability

---

# Disadvantages

Transactions

may be aborted

frequently.

Imagine

a long-running transaction.

Just before finishing,

it violates

timestamp ordering.

Everything

must restart.

Repeated restarts

can reduce performance.

---

# Timestamp Ordering vs Locking

| Locking | Timestamp Ordering |
|----------|--------------------|
| Uses Locks | Uses Timestamps |
| Transactions may wait | Transactions never wait |
| Deadlocks possible | Deadlocks impossible |
| May block other transactions | May abort transactions |

---

# Interview Questions

### What is Timestamp Ordering?

```text
A concurrency control protocol

that executes transactions

according to

their timestamps.
```

---

### Why are timestamps assigned?

```text
To determine

the execution order

of transactions.
```

---

### Does Timestamp Ordering use locks?

```text
No.
```

---

### Can Timestamp Ordering cause deadlocks?

```text
No.

Transactions

never wait.

They are either

allowed

or

aborted.
```

---

### Main disadvantage?

```text
Frequent transaction restarts.
```

---

# Mental Model

Imagine

a race.

Every runner

has

a bib number.

Officials decide

the finishing order

based on

those numbers.

If someone

tries to claim

they finished earlier

than their assigned order,

the officials reject it.

Timestamp Ordering

works exactly

the same way.

The database

never allows

transactions

to violate

their timestamp order.

---

# Bridge

So far,

we have studied

three major

Concurrency Control techniques.

```text
Locks

↓

Two-Phase Locking

↓

Timestamp Ordering
```

Question

How do we know

whether

the final execution

of multiple transactions

is actually

**correct**?

Even if

operations are interleaved,

the final result

should be

the same

as executing

the transactions

one after another.

This important concept

is called

```text
Serializability.
```

Serializability is considered

the mathematical foundation

of concurrency control

and is one of the most important DBMS interview topics.

# 7. Recoverable Schedules

Transactions

often depend

on each other.

Example

T1

writes data.

T2

reads

that data.

Question

Should T2

commit

before

T1 commits?

No.

Because

if T1 fails,

T2 has already

used incorrect data.

---

# Recoverable Schedule

Rule

```text
If

T2 reads data

written by T1,

then

T2

must commit

only

after

T1 commits.
```

This is called

a Recoverable Schedule.

---

# Example

Correct

```text
T1 Write(A)

↓

T2 Read(A)

↓

T1 Commit

↓

T2 Commit
```

Safe.

---

Wrong

```text
T1 Write(A)

↓

T2 Read(A)

↓

T2 Commit

↓

T1 Rollback
```

Now

T2 committed

using

invalid data.

Impossible

to recover.

---

# Cascading Rollback

Suppose

T2

reads

uncommitted data

from T1.

Later

T1 rolls back.

Question

What about T2?

It must also

rollback.

Suppose

T3

used T2's data.

Now

T3

must rollback.

```text
T1

↓

Rollback

↓

T2

↓

Rollback

↓

T3

↓

Rollback
```

Like

dominoes falling.

This is called

```text
Cascading Rollback.
```

---

# Cascadeless Schedule

To avoid

Cascading Rollback,

the DBMS

does not allow

transactions

to read

uncommitted data.

In other words,

```text
Read

Only

Committed Data.
```

Simple.

---

# Strict Schedule

Even stronger.

A transaction

cannot

Read

or

Write

a data item

until

the previous writer

commits.

Strict Schedules

prevent

Dirty Reads,

Cascading Rollbacks,

and simplify recovery.

Most databases

prefer

Strict Schedules.

---

# Comparison

| Schedule | Safe? |
|----------|-------|
| Recoverable | ✅ |
| Cascadeless | Better |
| Strict | Best |

Relationship

```text
Strict

↓

Cascadeless

↓

Recoverable
```

Every Strict Schedule

is Cascadeless.

Every Cascadeless Schedule

is Recoverable.

Reverse

is not true.

---

# Interview Questions

### What is a Recoverable Schedule?

```text
A schedule

where

a transaction commits

only after

the transaction

whose data it read

has committed.
```

---

### What is Cascading Rollback?

```text
Rollback

of one transaction

forces

other dependent

transactions

to rollback.
```

---

### Which schedule

is most preferred?

```text
Strict Schedule.
```

---

# DBMS07 Revision

```text
Concurrency Control

↓

Locks

↓

2PL

↓

Deadlocks

↓

Timestamp Ordering

↓

Serializability

↓

Recoverable Schedules
```

---

