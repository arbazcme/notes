# OS 05 - Deadlocks

## Navigation

- [Part 1 - Deadlock Fundamentals](#part-1---deadlock-fundamentals)
  - [1. What Is A Deadlock?](#1-what-is-a-deadlock)
  - [2. Why Deadlocks Occur](#2-why-deadlocks-occur)
  - [3. Resource Types](#3-resource-types)
  - [4. Resource Allocation Graph (RAG)](#4-resource-allocation-graph-rag)
  - [5. Coffman's Four Necessary Conditions](#5-coffmans-four-necessary-conditions)
    - [Mutual Exclusion](#mutual-exclusion)
    - [Hold And Wait](#hold-and-wait)
    - [No Preemption](#no-preemption)
    - [Circular Wait](#circular-wait)
  - [6. Can Deadlock Always Happen?](#6-can-deadlock-always-happen)
  - [7. Deadlock vs Starvation](#7-deadlock-vs-starvation)
  - [8. Deadlock vs Livelock](#8-deadlock-vs-livelock)
  - [9. Interview FAQs](#9-interview-faqs)

- [Part 2 - Handling Deadlocks](#part-2---handling-deadlocks)
  - [1. Deadlock Prevention](#1-deadlock-prevention)
  - [2. Deadlock Avoidance](#2-deadlock-avoidance)
  - [3. Safe State](#3-safe-state)
  - [4. Unsafe State](#4-unsafe-state)
  - [5. Banker's Algorithm](#5-bankers-algorithm)
  - [6. Deadlock Detection](#6-deadlock-detection)
  - [7. Deadlock Recovery](#7-deadlock-recovery)
  - [8. Ostrich Algorithm](#8-ostrich-algorithm)
  - [9. Interview FAQs](#9-interview-faqs-1)

- [Part 3 - Numerical Problems, Interview Questions & Revision](#part-3---numerical-problems-interview-questions--revision)


# Part 1 - Deadlock Fundamentals

---

# 1. What Is A Deadlock?

A **deadlock** is a situation where two or more processes/threads wait forever because each is waiting for a resource held by another.

None of them can continue.

---

## Simple Example

Suppose we have:

```text
Thread A

Thread B
```

and two resources:

```text
Printer

Scanner
```

Initially:

```text
Thread A
→ Holds Printer

Thread B
→ Holds Scanner
```

Now:

```text
Thread A
Needs Scanner
```

and

```text
Thread B
Needs Printer
```

Result:

```text
Thread A
Waiting For Scanner

Thread B
Waiting For Printer
```

Neither releases its current resource.

Both wait forever.

This is:

```text
Deadlock
```

---

## Diagram

```text
Thread A
│
│ Holds
▼
Printer

Thread B
│
│ Holds
▼
Scanner

Thread A
──────────────► Scanner

Thread B
──────────────► Printer
```

Both wait forever.

---

## Real Life Analogy

Imagine:

```text
Person A
```

has:

```text
Bike
```

needs:

```text
Helmet
```

while

```text
Person B
```

has:

```text
Helmet
```

needs:

```text
Bike
```

Neither gives up what they already have.

Everyone waits forever.

Exactly the same idea.

---

# Why Does Deadlock Occur?

Deadlock is **not** caused by multiple threads alone.

It happens because threads compete for:

```text
Resources
```

Examples:

```text
Mutex

Semaphore

File

Printer

Scanner

Database Lock

Memory Block

Network Socket
```

Whenever resources are limited,

deadlock becomes possible.

---

# Synchronization Created Deadlock?

In OS 04 we learned:

```text
Mutex
```

prevents race conditions.

Great.

But suppose:

Thread A:

```cpp
lock(Mutex1);

lock(Mutex2);
```

Thread B:

```cpp
lock(Mutex2);

lock(Mutex1);
```

Suppose:

```text
Thread A
gets Mutex1

Thread B
gets Mutex2
```

Now:

```text
A wants Mutex2

B wants Mutex1
```

Nobody releases anything.

Deadlock.

---

## Important Observation

Synchronization prevents:

```text
Race Conditions
```

but

Improper synchronization creates:

```text
Deadlocks
```

Interviewers love asking this.

---

# Resources

A resource is anything a process/thread must obtain before it can continue.

Examples:

```text
CPU

Memory

Printer

Disk

Mutex

Semaphore

Database Connection

Socket

GPU
```

---

# Resource Types

Resources are mainly of two types.

---

## 1. Preemptible Resource

Can be taken away safely.

Example:

```text
CPU
```

Scheduler can interrupt a running process.

Another example:

```text
Memory Page
```

OS may swap it.

---

## 2. Non-Preemptible Resource

Cannot be taken away safely.

Examples:

```text
Printer

Mutex

Scanner

DVD Writer
```

Imagine taking away a printer while a document is printing.

Output becomes corrupted.

Therefore:

```text
Cannot Be Forcibly Taken
```

Deadlocks mostly involve:

```text
Non-Preemptible Resources
```

---

# Why Limited Resources Cause Deadlock

Suppose:

```text
100 Printers

2 Threads
```

Deadlock?

Very unlikely.

Now suppose:

```text
1 Printer

2 Threads
```

Both require it.

Competition begins.

Limited resources create waiting.

Waiting creates the possibility of deadlock.

---

# Resource Allocation

Every resource follows:

```text
Request
↓

Allocate
↓

Use
↓

Release
```

Example:

```text
Thread

↓

Requests Printer

↓

OS Gives Printer

↓

Prints

↓

Releases Printer
```

Deadlock happens when:

```text
Release

Never Happens
```

because everyone waits.

---

# Key Difference

Race Condition

```text
Wrong Result
```

Deadlock

```text
No Result
```

Race Condition:

```text
Program Continues

Incorrectly
```

Deadlock:

```text
Program Never Continues
```

---

# Deadlock Characteristics

```text
Processes Are Alive

Processes Are Not Crashed

CPU May Be Idle

Memory Exists

Resources Exist

Only Progress Stops
```

---

# Can Deadlock Resolve Automatically?

Normally:

```text
NO
```

Every process keeps waiting forever.

Without external action,

deadlock remains forever.

---

# Interview Example

Suppose:

```cpp
Thread A

lock(A);

lock(B);
```

Thread B

```cpp
lock(B);

lock(A);
```

Possible?

```text
YES
```

Can both wait forever?

```text
YES
```

Deadlock.

---

# Memory Trick

```text
Race Condition

=
Wrong Execution

Deadlock

=
No Execution
```

---

# Interview Summary

```text
Deadlock

↓

Limited Resources

↓

Processes Hold Resources

↓

Processes Wait For Other Resources

↓

Nobody Releases

↓

System Stops Making Progress
```

---

# Quick Revision

```text
Deadlock
        ↓
Processes Wait Forever
        ↓
Limited Resources
        ↓
Hold Resource
        ↓
Request Another
        ↓
Nobody Releases
        ↓
No Progress
```
# Why Doesn't A Thread Release Resources Before Sleeping?

Suppose a thread executes:

```cpp
lock(A);

x = x + 5;

lock(B);

y = x + y;

unlock(B);
unlock(A);
```

Execution:

```text
Thread Gets A
        ↓
Updates x
        ↓
Needs B
```

If B is busy, should Thread A release A?

```text
NO
```

Why?

Because Thread A has only completed **half of its work**.

If it releases A:

```text
Another Thread
        ↓
Gets A
        ↓
Modifies x
```

Now when Thread A wakes up:

```text
Its Earlier Assumption About x
Is No Longer True
```

The operation becomes incorrect.

### Memory Trick

```text
Acquire Resource
        ↓
Finish Entire Operation
        ↓
Release Resource
```

Not

```text
Acquire
        ↓
Half Finish
        ↓
Release
        ↓
Continue Later
```

Otherwise other threads may change shared data in between, breaking program correctness.
# 4. Resource Allocation Graph (RAG)

A **Resource Allocation Graph (RAG)** is a graph used to visualize which process holds a resource and which process is waiting for one.

It helps us determine whether a deadlock may exist.

---

## Components

```text
Circle
→ Process (P1, P2...)

Square
→ Resource (R1, R2...)
```

Edges:

```text
Process ─────► Resource
```

Means:

```text
Process Is Requesting Resource
```

---

```text
Resource ─────► Process
```

Means:

```text
Resource Has Been Allocated
To The Process
```

---

## Example

```text
P1 ─────► R2

R1 ─────► P1

P2 ─────► R1

R2 ─────► P2
```

Meaning:

```text
P1 Holds R1
Needs R2

P2 Holds R2
Needs R1
```

Both wait forever.

Deadlock.

---

## Cycle In RAG

```text
P1 → R2 → P2 → R1 → P1
```

A cycle means:

```text
Processes Are Waiting
For Each Other
```

### Important Interview Point

If every resource has **only one instance**:

```text
Cycle
=
Deadlock
```

If resources have **multiple instances**:

```text
Cycle
≠
Always Deadlock
```

Further analysis is required.

---

# 5. Coffman's Four Necessary Conditions

A deadlock can occur **only if all four conditions are true simultaneously**.

If even **one** condition is removed,

deadlock cannot occur.

---

## 1. Mutual Exclusion

Definition:

```text
Only One Process
Can Use A Resource
At A Time
```

Example:

```text
Printer

Mutex

Scanner
```

Reason:

These resources cannot be shared simultaneously.

---

## 2. Hold And Wait

Definition:

```text
Process Holds
One Resource

While Waiting
For Another
```

Example:

```text
Thread A

Holding Printer

Waiting For Scanner
```

This is exactly what makes deadlock possible.

---

## 3. No Preemption

Definition:

```text
Resources Cannot
Be Taken Away
Forcefully
```

Only the process holding the resource can release it.

Example:

```text
Mutex

Printer

Scanner
```

The OS cannot simply take them away safely.

---

## 4. Circular Wait

Definition:

Processes form a cycle where each waits for a resource held by another.

Example:

```text
P1 Waits For P2

↓

P2 Waits For P3

↓

P3 Waits For P1
```

Nobody proceeds.

---

# Memory Trick

```text
Mutual Exclusion

↓

Hold And Wait

↓

No Preemption

↓

Circular Wait

↓

Deadlock
```

Remember:

```text
M H N C
```

---

# 6. Can Deadlock Always Happen?

No.

Deadlock happens **only if all four Coffman conditions exist simultaneously**.

Example:

If resources are shareable,

```text
No Mutual Exclusion
```

Deadlock becomes impossible.

If processes release resources before requesting new ones,

```text
No Hold And Wait
```

Deadlock becomes impossible.

Breaking **any one** condition prevents deadlock.

---

# 7. Deadlock vs Starvation

## Deadlock

```text
Processes Wait Forever

Waiting For Each Other
```

Nobody progresses.

---

## Starvation

```text
Process Waits Forever

Because Others
Keep Getting CPU
Or Resources
```

Other processes continue running.

Only one (or a few) suffer.

---

## Example

Priority Scheduling:

```text
High Priority Jobs

Keep Arriving
```

Low priority process may never execute.

This is:

```text
Starvation
```

Not Deadlock.

---

## Memory Trick

```text
Deadlock
=
Nobody Moves

Starvation
=
Others Move
One Suffers
```

---

# 8. Deadlock vs Livelock

Deadlock:

```text
Processes Wait

Do Nothing
```

Livelock:

```text
Processes Keep Running

But Make
No Progress
```

---

## Example

Two people meet in a corridor.

Both move left.

Both move right.

Both move left again.

They never collide,

but neither passes.

This is:

```text
Livelock
```

---

## Memory Trick

```text
Deadlock

=
Sleeping Forever

Livelock

=
Moving Forever
Without Progress
```

---

# 9. Interview FAQs

### Q1. What is the difference between Deadlock and Starvation?

```text
Deadlock
→ Everyone Waits

Starvation
→ One Waits
Others Continue
```

---

### Q2. Can Deadlock occur with one process?

```text
No
```

A process must wait for another process holding a needed resource.

---

### Q3. Can Deadlock occur without resource sharing?

```text
No
```

Deadlock requires competition for limited resources.

---

### Q4. What is the most important reason behind deadlock?

```text
Processes Hold Resources

While Waiting

For Other Resources
```

---

### Q5. How do we prevent deadlock?

```text
Break Any One

Of Coffman's

Four Conditions
```

---

# Part 1 Revision

```text
Limited Resources
        ↓
Processes Compete
        ↓
Hold Resources
        ↓
Wait For Others
        ↓
Four Coffman Conditions
Satisfied
        ↓
Deadlock

Break Any One Condition
        ↓
No Deadlock
```

## 4. Why Do Deadlocks Happen in Real Programs?

A common question is:

> **"If programmers know deadlocks are dangerous, why do they still occur?"**

The answer is that **real programs often need to access multiple shared resources at the same time.**

For better performance, each shared resource usually has **its own mutex** instead of using one global lock for the entire program. This allows multiple threads to work on different resources concurrently.

However, some operations require access to **more than one shared resource**, so a thread must acquire **multiple locks**.

### Example

Suppose there are two shared resources:

* Mutex `A` protects Resource A.
* Mutex `B` protects Resource B.

Programmer 1 writes:

```c
lock(A);
...
lock(B);
...
unlock(B);
unlock(A);
```

Programmer 2 (or another module) writes:

```c
lock(B);
...
lock(A);
...
unlock(A);
unlock(B);
```

Now imagine this execution:

```text
Thread 1:
lock(A);    // Acquired
lock(B);    // Waiting...

Thread 2:
lock(B);    // Acquired
lock(A);    // Waiting...
```

Current state:

```text
Thread 1 holds A and waits for B.
Thread 2 holds B and waits for A.
```

Neither thread can continue because each is waiting for the other to release its lock.

This creates a **deadlock (circular wait)**.

### How Is This Prevented?

Professional software avoids this by enforcing a **global lock order**.

For example:

```text
A → B → C → D
```

Every thread **must acquire locks in this order only**.

If every module follows the same lock order, **circular wait cannot occur**, eliminating one of the necessary conditions for deadlock.

> **Interview Tip:** Deadlocks often happen not because programmers intentionally write incorrect code, but because different modules or developers acquire the same locks in different orders.

