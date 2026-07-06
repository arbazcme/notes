# DBMS 06 - Transactions

# Navigation

- [1. Why Do We Need Transactions?](#1-why-do-we-need-transactions)
  - [Real World Problem](#real-world-problem)
  - [What Is A Transaction?](#what-is-a-transaction)
  - [Examples](#examples)
  - [Transaction States](#transaction-states)
  - [Interview Questions](#interview-questions)

- [2. ACID Properties](#2-acid-properties)
  - [Atomicity](#21-atomicity)
  - [Consistency](#22-consistency)
  - [Isolation](#23-isolation)
  - [Durability](#24-durability)
  - [Examples](#examples-1)
  - [Interview Questions](#interview-questions-1)

- [3. Transaction States](#3-transaction-states)
  - [Active](#active)
  - [Partially Committed](#partially-committed)
  - [Committed](#committed)
  - [Failed](#failed)
  - [Aborted](#aborted)
  - [State Diagram](#state-diagram)

- [4. Transaction Schedules](#4-transaction-schedules)
  - [Serial Schedule](#serial-schedule)
  - [Concurrent Schedule](#concurrent-schedule)
  - [Advantages](#advantages)
  - [Disadvantages](#disadvantages)

- [5. Problems In Concurrent Execution](#5-problems-in-concurrent-execution)
  - [Lost Update](#lost-update)
  - [Dirty Read](#dirty-read)
  - [Non-Repeatable Read](#non-repeatable-read)
  - [Phantom Read](#phantom-read)
  - [Examples](#examples-2)
  - [Interview Questions](#interview-questions-2)

- [6. Complete Revision](#6-complete-revision)

- [7. Interview Cheat Sheet](#7-interview-cheat-sheet)



# 1. Why Do We Need Transactions?

Until now,

we have learned

how to design

a good database.

We know

how to reduce redundancy,

avoid anomalies,

and organize data.

Question:

After the database

is designed,

how do people

actually use it?

Every day,

millions of users

are reading,

writing,

updating,

and deleting data.

Sometimes,

everything works perfectly.

Sometimes,

the system crashes.

Sometimes,

two users

try to update

the same data

at the same time.

How does

the database

ensure

that

the data

never becomes incorrect?

The answer is

```text
Transactions.
```

---

# Real World Problem

Suppose

Arbaaz

has

₹10,000

in his bank account.

Rahul

has

₹5,000.

Current database

| Account | Balance |
|----------|---------|
| Arbaaz | 10000 |
| Rahul | 5000 |

Now,

Arbaaz

sends

₹2,000

to Rahul.

Simple,

right?

Most people think

the database

does

one operation.

Actually,

it performs

two completely different operations.

Step 1

Remove money

from Arbaaz.

```text
10000

↓

8000
```

Step 2

Add money

to Rahul.

```text
5000

↓

7000
```

The complete transfer is

```text
Debit

↓

Credit
```

Two operations.

Not one.

---

# What If Something Goes Wrong?

Imagine

the database

finishes

Step 1.

```text
Arbaaz

10000

↓

8000
```

Before

Step 2,

the server crashes.

Power failure.

Electricity gone.

Database shuts down.

Current database

| Account | Balance |
|----------|---------|
| Arbaaz | 8000 |
| Rahul | 5000 |

Question:

Where did

₹2,000

go?

They disappeared.

Money

has been

lost forever.

This should

never happen.

---

# Another Situation

Suppose

Step 2

happens first.

Rahul receives

₹2,000.

```text
5000

↓

7000
```

Before

Arbaaz

is debited,

the server crashes.

Current database

| Account | Balance |
|----------|---------|
| Arbaaz | 10000 |
| Rahul | 7000 |

Question:

Where did

the extra

₹2,000

come from?

The database

created money

from nothing.

This is

equally wrong.

---

# The Core Problem

Notice something.

A money transfer

is actually

multiple operations.

```text
Read Balance

↓

Subtract Money

↓

Update Account

↓

Read Receiver

↓

Add Money

↓

Update Receiver
```

If

only

half

the work

finishes,

the database

becomes incorrect.

---

# We Need One Rule

Either

```text
Every step succeeds.
```

OR

```text
None of the steps succeed.
```

There should never be

a situation

where

only

some operations

are completed.

This is exactly

what a Transaction guarantees.

---

# What Is A Transaction?

A Transaction

is

a group

of one or more

database operations

that are treated

as

a single unit of work.

The database

does not care

whether

there are

2 operations,

20 operations,

or

200 operations.

To the DBMS,

they are

one logical task.

---

## Definition

A Transaction is

```text
A sequence of database operations

that executes

completely

or

not at all.
```

This is called

```text
All-or-Nothing Execution.
```

---

# Think Of It Like A Movie

Suppose

you're watching

a movie.

Would you like

to watch

only

the first half?

No.

Either

the entire movie

plays,

or

it doesn't.

A Transaction

works exactly

the same way.

```text
Start

↓

Operation 1

↓

Operation 2

↓

Operation 3

↓

Operation 4

↓

Finish
```

If

Operation 3 fails,

the database

goes back

to the beginning,

as if

nothing ever happened.

---

# Everyday Examples

## ATM Withdrawal

When you withdraw

₹5,000,

the ATM performs

many operations.

```text
Check Account

↓

Verify Balance

↓

Debit Account

↓

Dispense Cash

↓

Update Database

↓

Print Receipt
```

Imagine

cash is dispensed,

but

the account

is not debited.

Free money.

Now imagine

the account

is debited,

but

cash

never comes out.

You lose money.

Both situations

are unacceptable.

Therefore,

the entire withdrawal

is one Transaction.

---

## Online Shopping

You click

"Buy Now."

The database

must

```text
Create Order

↓

Reduce Stock

↓

Process Payment

↓

Generate Invoice

↓

Send Confirmation
```

Suppose

payment succeeds,

but

the order

is never created.

Your money

is gone.

Again,

this entire workflow

must be

one Transaction.

---

## Railway Ticket Booking

When you book

one train ticket,

the database

must

```text
Check Seat

↓

Reserve Seat

↓

Process Payment

↓

Generate Ticket
```

Imagine

payment succeeds,

but

the seat

was never reserved.

Impossible.

That's why

ticket booking

is implemented

using Transactions.

---

# Transaction Boundary

Every transaction

has

a beginning

and

an end.

```sql
BEGIN TRANSACTION;

...

SQL Statements

...

COMMIT;
```

If everything succeeds,

the database executes

```sql
COMMIT;
```

meaning

```text
Save everything permanently.
```

---

If

something fails,

the database executes

```sql
ROLLBACK;
```

meaning

```text
Undo every change

and

return to

the previous state.
```

---

# Simple Example

Transfer

₹2,000.

```sql
BEGIN;

UPDATE Account
SET Balance = Balance - 2000
WHERE Name='Arbaaz';

UPDATE Account
SET Balance = Balance + 2000
WHERE Name='Rahul';

COMMIT;
```

If

both updates succeed,

the database

stores them permanently.

---

Suppose

the second UPDATE fails.

Instead of saving

only

the first UPDATE,

the DBMS performs

```sql
ROLLBACK;
```

Now,

both balances

return

to their original values.

Exactly

as if

the transfer

never started.

---

# Mental Model

Imagine

writing

an important exam.

You answer

100 questions.

When you finish,

you submit

the paper.

Until you submit,

the examiner

doesn't evaluate

anything.

You may

erase,

rewrite,

or

change answers.

Only after

submission

does

everything become final.

A Transaction

works similarly.

```text
BEGIN

↓

Make Changes

↓

More Changes

↓

Even More Changes

↓

COMMIT

(Final)

or

ROLLBACK

(Discard Everything)
```

---

# Interview Questions

## What is a Transaction?

```text
A Transaction

is a sequence

of database operations

that execute

as one logical unit.

Either

all operations succeed,

or

none of them succeed.
```

---

## Why do we need Transactions?

```text
To ensure

data remains

correct

even if

a system crash,

power failure,

or software error

occurs

during execution.
```

---

## Give a real-world example.

```text
Bank Transfer

ATM Withdrawal

Railway Reservation

Online Shopping

UPI Payment
```

---

# Bridge

Now we know

what a Transaction is.

Question:

How does

the database

guarantee

that

Transactions

are reliable?

How does it ensure

```text
No money disappears?

No duplicate money?

No partial updates?

No corrupted database?
```

To achieve this,

every DBMS

follows

four fundamental rules.

They are called

```text
ACID Properties.
```
# 2. ACID Properties

In the previous topic,

we learned

what a Transaction is.

Question:

Suppose

a transaction

is running.

How does

the database

guarantee

that

everything remains correct?

For example,

how does it guarantee

that

- Money is never lost?
- Data never becomes inconsistent?
- Two users don't corrupt each other's work?
- A committed transaction is never forgotten?

Every modern DBMS

follows

four important rules.

Together,

they are called

```text
ACID Properties
```

```text
A → Atomicity

C → Consistency

I → Isolation

D → Durability
```

Every transaction

must satisfy

all four.

Without ACID,

banks,

ATMs,

UPI,

shopping websites,

and airline booking systems

would constantly

produce incorrect data.

---

# Think Of ACID As Four Promises

Whenever

the database

executes

a transaction,

it promises:

```text
1.

I will either

do everything

or

nothing.

-------------------

2.

I will never

leave

the database

in an invalid state.

-------------------

3.

Other transactions

cannot interfere

with yours.

-------------------

4.

Once I say

"Done",

I will never

forget it.
```

These four promises

are exactly

Atomicity,

Consistency,

Isolation,

and

Durability.

======================================================

# 2.1 Atomicity

Atomicity

comes from

the word

```text
Atom
```

An atom

cannot be divided

into smaller parts.

Similarly,

a transaction

cannot be

partially completed.

It is treated

as

one indivisible unit.

---

## Main Idea

Either

```text
Everything happens.
```

OR

```text
Nothing happens.
```

There is

no middle ground.

---

## Bank Transfer Example

Suppose

Arbaaz

transfers

₹2,000

to Rahul.

Transaction

```text
Step 1

Subtract

₹2,000

from Arbaaz.

↓

Step 2

Add

₹2,000

to Rahul.
```

Question

What if

the server crashes

after

Step 1?

Current database

| Account | Balance |
|----------|---------|
|Arbaaz|8000|
|Rahul|5000|

Money disappeared.

Atomicity says

this is illegal.

Instead,

the DBMS performs

```text
ROLLBACK
```

Balances become

| Account | Balance |
|----------|---------|
|Arbaaz|10000|
|Rahul|5000|

Exactly

as if

the transaction

never started.

---

## Another Example

Suppose

online shopping.

Transaction

```text
Create Order

↓

Reduce Stock

↓

Process Payment

↓

Generate Invoice
```

Imagine

Payment succeeds,

but

Order Creation fails.

Without Atomicity,

customer loses money.

With Atomicity,

the payment

is rolled back.

Everything returns

to the original state.

---

## Important Point

Atomicity

does NOT mean

the transaction

cannot fail.

Transactions

can fail.

Atomicity simply says

```text
If one operation fails,

undo

every operation.
```

---

## How Is Atomicity Implemented?

Using

```text
Undo Logs

Rollback

Recovery System
```

We'll study these

later

in the Recovery chapter.

For now,

remember

Atomicity

is mainly achieved

using

Rollback.

---

## Interview Questions

### What is Atomicity?

```text
A transaction

executes

completely

or

not at all.
```

---

### Which SQL command

supports Atomicity?

```sql
ROLLBACK;
```

======================================================

# 2.2 Consistency

Students often confuse

Consistency

with

Atomicity.

They are

completely different.

Atomicity asks

```text
Did every operation finish?
```

Consistency asks

```text
Is the database

still valid?
```

---

## What Is Consistency?

A transaction

must move

the database

from

one valid state

to

another valid state.

It must never

leave

the database

in an invalid state.

---

## Example

Suppose

a bank has

only one rule.

```text
Total Money

must remain

₹15,000.
```

Initially

| Account | Balance |
|----------|---------|
|Arbaaz|10000|
|Rahul|5000|

Total

```text
15000
```

Transfer

₹2,000.

After transaction

| Account | Balance |
|----------|---------|
|Arbaaz|8000|
|Rahul|7000|

Total

```text
15000
```

Database

is still valid.

Consistency preserved.

---

## Invalid Example

Suppose

after a crash

database becomes

| Account | Balance |
|----------|---------|
|Arbaaz|8000|
|Rahul|5000|

Total

```text
13000
```

Money vanished.

Business rules

are broken.

Consistency

is violated.

---

## Another Example

Suppose

Age

must always

be positive.

Valid

```text
Age = 20
```

Invalid

```text
Age = -5
```

The database

should reject

such data.

Consistency

ensures

constraints

are never violated.

---

## What Maintains Consistency?

Primary Keys

Foreign Keys

Unique Constraints

Check Constraints

Triggers

Application Logic

All together

help maintain

Consistency.

---

## Interview Questions

### What is Consistency?

```text
A transaction

moves

the database

from one valid state

to another valid state.
```

---

### Does Consistency

prevent crashes?

```text
No.

It prevents

invalid data.
```

======================================================

# 2.3 Isolation

Now imagine

multiple users

using

the database

at the same time.

Suppose

Arbaaz

is transferring

₹2,000.

At exactly

the same time,

Rahul

is checking

his balance.

Question

Should Rahul

see

half-finished data?

No.

---

## Example

Transaction T1

```text
Subtract

₹2,000

from Arbaaz.
```

Not yet committed.

Meanwhile,

Transaction T2

reads

Arbaaz's account.

It sees

₹8,000.

Question

What if

T1 later

fails

and rolls back?

Real balance

becomes

₹10,000.

But

T2 already saw

₹8,000.

This is

incorrect.

---

Isolation says

```text
One transaction

should not

see

another transaction's

unfinished work.
```

Every transaction

should behave

as if

it is

running alone.

---

## Think Of An Examination

Suppose

students

are writing

an exam.

Would you allow

one student

to continuously erase

another student's answers?

No.

Each student

works independently.

Only after

submitting

does everyone

see

the final paper.

Isolation

works

the same way.

---

## Isolation Prevents

```text
Dirty Reads

Lost Updates

Non-Repeatable Reads

Phantom Reads
```

These are

the next topics

of this chapter.

---

## Interview Questions

### What is Isolation?

```text
Transactions

should not

interfere

with

each other.
```

---

### Why is Isolation needed?

```text
To prevent

concurrent transactions

from reading

or modifying

unfinished data.
```

======================================================

# 2.4 Durability

Imagine

you transfer

₹5,000.

Database says

```text
Transaction Successful.
```

One second later,

power goes off.

Server crashes.

Question

Should

your money

disappear?

Never.

---

## What Is Durability?

Once

a transaction

is committed,

its changes

must remain

permanently,

even if

the system crashes.

---

## Example

Suppose

Google Pay

shows

```text
Payment Successful.
```

Immediately

after that,

the server room

loses electricity.

When

the database

starts again,

your payment

must still exist.

If not,

nobody would trust

online banking.

---

## How Is Durability Achieved?

Using

```text
Disk Storage

Redo Logs

Write-Ahead Logging (WAL)

Checkpoints

Recovery Algorithms
```

We'll study these

later

in Recovery.

---

## Durability vs Atomicity

Students

often confuse

these two.

Atomicity

asks

```text
Did everything happen?
```

Durability asks

```text
Will committed work

remain forever?
```

Very different.

---

## Interview Questions

### What is Durability?

```text
Once committed,

the transaction

is permanently stored,

even after

system failure.
```

======================================================

# Complete ACID Summary

| Property | Meaning |
|-----------|---------|
| Atomicity | Everything or Nothing |
| Consistency | Valid State → Valid State |
| Isolation | Transactions do not interfere |
| Durability | Committed data is permanent |

---

# How To Remember ACID

```text
A

All or Nothing

-------------------

C

Correct Database

-------------------

I

Independent Transactions

-------------------

D

Data Never Lost
```

======================================================

# Real Bank Transfer

```text
BEGIN

↓

Atomicity

Either both accounts update

or rollback.

↓

Consistency

Total money remains constant.

↓

Isolation

Other users cannot see

half-finished balances.

↓

Durability

Once committed,

balances survive crashes.

↓

COMMIT
```

======================================================

# Mental Model

Imagine

building a bridge.

Atomicity says

either

the entire bridge

is completed,

or

construction is cancelled.

Consistency says

the bridge

must satisfy

all engineering rules.

Isolation says

workers

don't interfere

with each other's tasks.

Durability says

once completed,

the bridge

remains standing,

even after

storms.

A database transaction

works exactly

the same way.

---

# Bridge

Now we know

what guarantees

a transaction provides.

Question:

What happens

inside the DBMS

while

a transaction

is executing?

Does it instantly

become committed?

Or

does it pass through

multiple stages?

Every transaction

moves through

a sequence of states.

These are called

```text
Transaction States.
```

# 3. Transaction States

So far, we know:

```text
Transaction

↓

Group of SQL operations

↓

Either all succeed

or

none succeed.
```

Question:

Does a transaction go directly from

```text
BEGIN

↓

COMMIT
```

No.

Every transaction passes through **multiple states** before it finishes.

Understanding these states helps us know **what the DBMS is doing internally**.

---

# Why Do We Need Transaction States?

Suppose you transfer ₹2,000 using UPI.

Internally, the DBMS performs:

```text
BEGIN

↓

Check Sender Balance

↓

Debit Sender

↓

Credit Receiver

↓

Update Database

↓

COMMIT
```

Question:

What if the server crashes after "Debit Sender"?

The transaction has **not finished** yet.

The DBMS must know:

- Which transactions are still running?
- Which ones have completed?
- Which ones failed?
- Which ones need rollback?

This is why Transaction States exist.

---

# Complete State Diagram

```text
                  BEGIN
                    │
                    ▼
                Active
                    │
          All Operations Done
                    │
                    ▼
         Partially Committed
              │          │
      Success │          │ Failure
              ▼          ▼
         Committed      Failed
                            │
                            ▼
                         Aborted
                            │
            Restart? ── Yes ─┘
                 │
                 ▼
              Active
```

Every transaction moves through one or more of these states.

---

# 3.1 Active State

The transaction starts here.

As soon as we execute

```sql
BEGIN;
```

the transaction becomes **Active**.

The DBMS is currently executing SQL statements.

Example

```sql
BEGIN;

UPDATE Account
SET Balance = Balance - 2000
WHERE Name='Arbaaz';

UPDATE Account
SET Balance = Balance + 2000
WHERE Name='Rahul';
```

At this point,

the transaction is still Active.

Nothing has been permanently saved.

---

## Important Point

During the Active state,

changes are **temporary**.

If an error occurs,

everything can still be rolled back.

Think of it like writing an exam.

Until you submit the paper,

you can erase,

rewrite,

or change answers.

---

# 3.2 Partially Committed State

Suppose every SQL statement has executed successfully.

Example

```sql
UPDATE ...

UPDATE ...

INSERT ...

DELETE ...
```

Everything finished.

But...

the DBMS has **not yet permanently saved** the changes.

The transaction enters the

```text
Partially Committed
```

state.

Think of it as

```text
Work Finished

↓

Waiting To Save Permanently
```

---

## Why Does This State Exist?

Saving data permanently is not instantaneous.

The DBMS may still need to

- write logs,
- flush data to disk,
- verify constraints.

Only after all of this succeeds,

the transaction becomes Committed.

---

# 3.3 Committed State

This is the goal of every transaction.

When the DBMS executes

```sql
COMMIT;
```

all changes become permanent.

Example

Before transaction

| Account | Balance |
|----------|---------|
|Arbaaz|10000|
|Rahul|5000|

After Commit

| Account | Balance |
|----------|---------|
|Arbaaz|8000|
|Rahul|7000|

Even if

the server crashes immediately,

these values remain stored.

This is where **Durability** takes effect.

---

# 3.4 Failed State

Suppose something goes wrong.

Examples:

- Power failure
- Server crash
- Disk failure
- Constraint violation
- Division by zero
- Network failure

The transaction immediately enters

```text
Failed
```

state.

Example

```sql
BEGIN;

UPDATE Account ...

-- Server crashes here
```

The DBMS knows

the transaction

cannot continue.

---

# 3.5 Aborted State

After failure,

the DBMS performs

```text
ROLLBACK
```

Every change made during the transaction is undone.

The database returns to its previous state.

Example

Before

| Account | Balance |
|----------|---------|
|Arbaaz|10000|
|Rahul|5000|

Transaction fails after debit.

Instead of leaving

```text
8000

5000
```

the DBMS restores

```text
10000

5000
```

The transaction is now

```text
Aborted.
```

---

# Can An Aborted Transaction Restart?

Yes.

Sometimes,

the failure was temporary.

Example

- Network timeout
- Deadlock
- Temporary server issue

The DBMS may restart the transaction automatically.

Flow:

```text
Failed

↓

Rollback

↓

Aborted

↓

Restart

↓

Active
```

---

# Complete Example

Suppose

you order food online.

```text
BEGIN

↓

Create Order

↓

Reserve Restaurant

↓

Process Payment

↓

Generate Invoice
```

### Case 1 — Everything succeeds

```text
Active

↓

Partially Committed

↓

Committed
```

Order placed successfully.

---

### Case 2 — Payment fails

```text
Active

↓

Failed

↓

Rollback

↓

Aborted
```

Order disappears.

Money is refunded.

Exactly as if

the order never existed.

---

# Summary Table

| State | Meaning |
|--------|---------|
| Active | Transaction is executing |
| Partially Committed | Execution finished, waiting for permanent save |
| Committed | Successfully completed and permanently saved |
| Failed | Error occurred, cannot continue |
| Aborted | Rollback completed, database restored |

---

# Interview Questions

### What is the first state of a transaction?

```text
Active
```

---

### Which state comes before Commit?

```text
Partially Committed
```

---

### When does a transaction enter the Failed state?

```text
Whenever an error or crash prevents
successful completion.
```

---

### What happens after the Failed state?

```text
ROLLBACK

↓

Aborted
```

---

### Can an Aborted transaction execute again?

```text
Yes.

The DBMS may restart it
if appropriate.
```

---

# Mental Model

Think of sending an email.

```text
Draft
   ↓
Writing
   ↓
Ready to Send
   ↓
Sent
```

If the internet disconnects,

```text
Writing

↓

Failed

↓

Draft Restored
```

A database transaction behaves the same way.

---

# Bridge

So far,

we have studied

- Transactions
- ACID Properties
- Transaction States

Everything assumed

only **one transaction** was running.

But real databases serve

thousands of users simultaneously.

Question:

What happens if

two transactions

run at the same time?

Should the DBMS execute

one after another,

or allow them to execute together?

This brings us to

```text
Transaction Schedules.
```

# 4. Transaction Schedules

Until now,

we assumed only **one transaction** was running.

Example

```text
T1

BEGIN
↓

Read Balance
↓

Update Balance
↓

COMMIT
```

Simple.

No conflicts.

No problems.

---

## But Real Databases Don't Work Like That

Imagine Amazon.

Thousands of users are:

- Ordering products
- Cancelling orders
- Updating addresses
- Making payments

at the **same time**.

Similarly,

a bank may process

millions of transactions simultaneously.

Question:

Should the DBMS wait for one transaction to finish before starting the next?

If yes,

the database becomes extremely slow.

Instead,

modern DBMS executes **multiple transactions together**.

The order in which database operations are executed is called a

```text
Transaction Schedule.
```

---

# What Is A Transaction Schedule?

A Transaction Schedule is simply

```text
The order

in which operations

from one or more

transactions are executed.
```

Notice

we are talking about

the **order of execution**,

not the SQL statements themselves.

---

# Example Transactions

Suppose we have

Transaction T1

```text
Read(A)

↓

A = A - 100

↓

Write(A)
```

Transaction T2

```text
Read(B)

↓

B = B + 100

↓

Write(B)
```

A Schedule decides

how these operations

will execute.

---

# Types Of Schedules

There are two major types.

```text
1. Serial Schedule

2. Concurrent Schedule
```

Everything in DBMS

starts here.

---

# 4.1 Serial Schedule

In a Serial Schedule,

one transaction finishes completely

before another begins.

Example

```text
T1

Read(A)

↓

Write(A)

↓

COMMIT

-------------------

T2

Read(B)

↓

Write(B)

↓

COMMIT
```

Visual

```text
Time →

T1 ██████████

T2           ██████████
```

No overlap.

---

## Characteristics

✔ Easy to understand.

✔ No conflicts.

✔ No data inconsistency.

✔ Easy to implement.

---

## Disadvantage

Suppose

T1

takes

5 seconds.

T2

must wait

all 5 seconds,

even if

both transactions

work on completely different tables.

This wastes CPU,

memory,

and disk resources.

Poor performance.

---

# Example

Imagine

a railway reservation system.

Only one booking

is processed

at a time.

Customer 2

must wait

until

Customer 1 finishes.

Even though

they are booking

different trains.

Clearly,

this is inefficient.

---

# 4.2 Concurrent Schedule

Instead of waiting,

the DBMS

mixes operations

from multiple transactions.

Example

```text
Time →

T1 : Read(A)

T2 : Read(B)

T1 : Update(A)

T2 : Update(B)

T1 : Write(A)

T2 : Write(B)

T1 : Commit

T2 : Commit
```

Visual

```text
Time →

T1 ███  ███  ███

T2   ███  ███  ███
```

Operations overlap.

Notice

T1

is not finished

before

T2 starts.

---

## Why Use Concurrent Execution?

Suppose

T1

is waiting

for disk access.

Instead of

keeping the CPU idle,

the DBMS executes

T2.

While T2 waits,

it may execute T3.

This greatly improves

resource utilization.

Exactly like

CPU Scheduling

in Operating Systems.

---

# Benefits Of Concurrent Schedules

### Better CPU Utilization

Instead of waiting,

another transaction runs.

---

### Higher Throughput

More transactions

complete every second.

Example

```text
Serial

↓

100 transactions/sec

Concurrent

↓

600 transactions/sec
```

(The numbers are illustrative.)

---

### Better Response Time

Users don't wait

for every previous transaction

to finish.

The system feels

much faster.

---

# The Problem

Concurrent execution

introduces

a new challenge.

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

at exactly

the same time.

Both think

balance is

₹10,000.

Final database

might become

```text
₹8,000
```

or

```text
₹7,000
```

instead of

```text
₹5,000
```

One update

gets lost.

This is called

```text
Lost Update.
```

We'll study it

next.

---

# Serial vs Concurrent

| Feature | Serial | Concurrent |
|----------|---------|------------|
| Transactions overlap | ❌ No | ✅ Yes |
| Performance | Lower | Higher |
| Throughput | Lower | Higher |
| Easy to implement | ✅ Yes | ❌ More difficult |
| Risk of conflicts | Almost None | High |

---

# Why Doesn't Every DBMS Use Only Serial Execution?

Because

serial execution

would make

modern applications

extremely slow.

Imagine

Instagram,

WhatsApp,

Google Pay,

or Amazon

processing

only one request

at a time.

Millions of users

would have to wait.

Therefore,

every modern DBMS

uses concurrent execution,

but

carefully controls it

to avoid errors.

---

# Interview Questions

### What is a Transaction Schedule?

```text
The order

in which operations

of one or more transactions

are executed.
```

---

### What is a Serial Schedule?

```text
One transaction

completes entirely

before

the next transaction starts.
```

---

### What is a Concurrent Schedule?

```text
Operations from multiple

transactions

are interleaved

to improve performance.
```

---

### Which schedule gives better performance?

```text
Concurrent Schedule.
```

---

### Which schedule is safer?

```text
Serial Schedule,

because

transactions never overlap.
```

---

# Mental Model

Imagine

two students

using

a library.

Serial

```text
Student A

finishes reading

↓

Student B enters
```

Concurrent

```text
Student A

reads Book 1

while

Student B

reads Book 2
```

Both use

the library

simultaneously,

making better use

of resources.

A DBMS works

the same way.

---

# Bridge

Concurrent execution

makes databases

much faster.

Unfortunately,

it also introduces

new problems.

Multiple transactions

may

read,

write,

or update

the same data

at the same time.

These problems are called

```text
Concurrency Problems.
```

The four most important ones are

- Lost Update
- Dirty Read
- Non-Repeatable Read
- Phantom Read

These are among the most frequently asked DBMS interview questions.

# 5. Problems in Concurrent Execution

In the previous topic,

we learned that

Concurrent Schedules improve performance.

However,

they also introduce

a new challenge.

Two or more transactions

may access

the same data

at the same time.

If the DBMS

does not coordinate them,

the database

may become incorrect.

These problems are called

```text
Concurrency Problems
```

The four most important ones are

```text
1. Lost Update

2. Dirty Read

3. Non-Repeatable Read

4. Phantom Read
```

These are frequently asked in interviews.

---

# Running Example

Assume

Arbaaz has

₹10,000

in his account.

| Account | Balance |
|----------|---------|
| Arbaaz | 10000 |

Transaction T1

```text
Withdraw ₹2,000
```

Transaction T2

```text
Withdraw ₹3,000
```

Initially,

both transactions

start together.

---

# 5.1 Lost Update

This is

the most common

concurrency problem.

---

## Example

Both transactions

read the balance

at the same time.

```text
Balance = 10000
```

Timeline

```text
T1                    T2

Read(10000)

                      Read(10000)

Balance = 8000

                      Balance = 7000

Write(8000)

                      Write(7000)
```

Question

What should

the final balance be?

Correct answer

```text
10000

↓

-2000

↓

-3000

↓

5000
```

Actual database

```text
7000
```

The update made by T1

was overwritten

by T2.

One update

was completely lost.

Hence,

```text
Lost Update.
```

---

## Why Did This Happen?

Because

both transactions

read

the old value

before

either transaction

finished updating it.

---

## Definition

```text
Lost Update

occurs when

one transaction's update

is overwritten

by another transaction.
```

---

## Real World Example

Two employees

edit

the same Google Sheet.

Employee A

changes salary

to

₹50,000.

Employee B

still has

the old sheet open.

He saves

₹48,000.

Employee A's update

disappears.

Lost Update.

---

# 5.2 Dirty Read

Question:

Can one transaction

read

another transaction's

unfinished data?

It should not.

---

## Example

T1

starts

withdrawing

₹2,000.

Before committing,

balance becomes

```text
8000
```

Meanwhile,

T2

reads

the balance.

It sees

```text
8000
```

Now,

T1 fails.

ROLLBACK happens.

Actual balance

returns to

```text
10000
```

But

T2 already used

the incorrect value

```text
8000
```

T2 read

data

that never actually existed.

This is called

```text
Dirty Read.
```

---

## Timeline

```text
T1                    T2

Balance=8000

                      Read(8000)

Rollback

Balance=10000
```

T2 read

"dirty"

(uncommitted)

data.

---

## Definition

```text
Dirty Read

occurs when

one transaction

reads

another transaction's

uncommitted data.
```

---

## Real World Example

Imagine

Amazon temporarily shows

"Payment Successful."

Another service

reads this information.

Later,

payment fails

and is rolled back.

The second service

used

incorrect information.

---

# 5.3 Non-Repeatable Read

Suppose

T1

reads

Arbaaz's balance.

```text
10000
```

Before T1

reads again,

T2 updates

the balance.

```text
10000

↓

7000
```

Now

T1 reads again.

It gets

```text
7000
```

Question

Why did

the same query

return

two different answers

inside

the same transaction?

This is called

```text
Non-Repeatable Read.
```

---

## Timeline

```text
T1                    T2

Read(10000)

                      Update(7000)

                      Commit

Read(7000)
```

Same query.

Different result.

---

## Definition

```text
Non-Repeatable Read

occurs when

the same row

returns

different values

within

the same transaction.
```

---

## Real World Example

Suppose

you open

your bank app.

Balance shows

₹10,000.

Before refreshing,

another ATM

withdraws money.

You refresh.

Now balance shows

₹7,000.

Same account.

Different answer.

---

# 5.4 Phantom Read

Unlike

Non-Repeatable Read,

here

the problem

is not

a changed row.

Instead,

new rows

appear

or disappear.

---

## Example

Suppose

T1 executes

```sql
SELECT *

FROM Employee

WHERE Salary > 50000;
```

Result

```text
5 Employees
```

Meanwhile,

T2 inserts

a new employee

earning

₹60,000.

```sql
INSERT ...
```

T2 commits.

Now

T1 executes

the same query

again.

Result

```text
6 Employees
```

Question

Where did

the extra employee

come from?

The row

appeared

like a ghost.

Hence,

```text
Phantom Read.
```

---

## Timeline

```text
T1                    T2

SELECT

5 rows

                      INSERT

                      Commit

SELECT

6 rows
```

---

## Definition

```text
Phantom Read

occurs when

re-executing

the same query

returns

a different set of rows.
```

Notice

the rows themselves

didn't change.

The

result set

changed.

---

## Difference From Non-Repeatable Read

Non-Repeatable Read

```text
Same row

↓

Different value
```

Phantom Read

```text
Different rows

↓

Result size changes
```

---

# Summary Table

| Problem | What Changes? |
|----------|---------------|
| Lost Update | One update overwrites another |
| Dirty Read | Read uncommitted data |
| Non-Repeatable Read | Same row changes value |
| Phantom Read | Result set changes (rows added/removed) |

---

# Easy Way To Remember

```text
Lost Update

↓

Someone's work disappeared.

-------------------------

Dirty Read

↓

Read unfinished work.

-------------------------

Non-Repeatable Read

↓

Same row

Different value.

-------------------------

Phantom Read

↓

Same query

Different number of rows.
```

---

# Interview Questions

### Which problem reads uncommitted data?

```text
Dirty Read
```

---

### Which problem causes one update to disappear?

```text
Lost Update
```

---

### Which problem changes the value of the same row?

```text
Non-Repeatable Read
```

---

### Which problem changes the number of rows returned?

```text
Phantom Read
```

---

# Mental Model

Imagine

you're reading

a class attendance sheet.

```text
Lost Update

↓

Someone erased
your changes.

------------------

Dirty Read

↓

You read
someone's rough draft.

------------------

Non-Repeatable Read

↓

Same student's attendance
changed.

------------------

Phantom Read

↓

A completely new student
appeared in the class.
```

---

# Bridge

We now know

why concurrent execution

can be dangerous.

Question

How does

a DBMS

prevent

these problems?

Should it

lock the data?

Should it

delay transactions?

Should it

allow only one user

at a time?

The techniques used

to solve these problems

are called

```text
Concurrency Control.
```

This is the next major chapter of DBMS.
