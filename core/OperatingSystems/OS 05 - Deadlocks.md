# OS 05 - Deadlocks

## Navigation

- [Part 1 - Deadlock Fundamentals](#part-1---deadlock-fundamentals)
  - [1. What Is A Deadlock?](#1-what-is-a-deadlock)
  - [2. Why Deadlocks Occur](#why-deadlocks-occur)
  - [3. Resource Types](#resource-types)
  - [4. Resource Allocation Graph (RAG)](#4-resource-allocation-graph-rag)
  - [5. Coffman's Four Necessary Conditions](#5-coffmans-four-necessary-conditions)
    - [Mutual Exclusion](#1-mutual-exclusion)
    - [Hold And Wait](#2-hold-and-wait)
    - [No Preemption](#3-no-preemption)
    - [Circular Wait](#4-circular-wait)
  - [6. Can Deadlock Always Happen?](#6-can-deadlock-always-happen)
  - [7. Deadlock vs Starvation](#7-deadlock-vs-starvation)
  - [8. Deadlock vs Livelock](#8-deadlock-vs-livelock)
  - [9. Interview FAQs](#9-interview-faqs)
  - [10. Why Do Deadlocks Happen in Real Programs?](#4-why-do-deadlocks-happen-in-real-programs)

- [Part 2 - Handling Deadlocks](#part-2---handling-deadlocks)
  - [1. Why Do We Need Deadlock Handling?](#1-why-do-we-need-deadlock-handling)
  - [2. Deadlock Prevention](#2-deadlock-prevention)
  - [3. Deadlock Avoidance](#3-deadlock-avoidance)
  - [4. Safe State](#4-safe-state)
  - [5. Unsafe State](#5-unsafe-state)
  - [6. Banker's Algorithm](#6-bankers-algorithm)
  - [7. Deadlock Detection](#7-deadlock-detection)
  - [8. Deadlock Recovery](#8-deadlock-recovery)
  - [9. Ostrich Algorithm](#9-ostrich-algorithm)
  - [10. Interview FAQs](#interview-faqs)
  - [11. Part 2 Revision](#part-2-revision)

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

# why deadlocks occur

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

# Part 2 - Handling Deadlocks

---

# 1. Why Do We Need Deadlock Handling?

Suppose a deadlock has occurred.

```text
Thread A
Holding Resource A
Waiting For Resource B

Thread B
Holding Resource B
Waiting For Resource A
```

System makes:

```text
No Progress
```

The OS must decide:

```text
Prevent It?

Avoid It?

Detect It?

Recover From It?
```

These are the four major approaches.

---

# Deadlock Handling Methods

```text
Deadlock Handling

│

├── Prevention

├── Avoidance

├── Detection

└── Recovery
```

---

# 2. Deadlock Prevention

## Idea

Instead of waiting for a deadlock,

prevent it from ever occurring.

How?

```text
Break At Least One

Of Coffman's Four Conditions.
```

Remember:

```text
Mutual Exclusion

Hold And Wait

No Preemption

Circular Wait
```

If any one is removed,

deadlock becomes impossible.

---

## Breaking Mutual Exclusion

Idea:

```text
Share Resources
Whenever Possible
```

Example:

```text
Read-Only Files
```

Many processes can read simultaneously.

No exclusive ownership.

Problem:

Not all resources are shareable.

Examples:

```text
Printer

Mutex
```

still require mutual exclusion.

---

## Breaking Hold And Wait

Idea:

```text
Request All Resources

Before Starting
```

Example:

Instead of

```text
Request A

Later Request B
```

do

```text
Request A And B

Together
```

If both available

↓

Start.

Otherwise

↓

Wait.

No resource is held while requesting another.

Deadlock prevented.

Disadvantage:

```text
Poor Resource Utilization
```

Resources stay unused for long periods.

---

## Breaking No Preemption

Idea:

```text
Take Resource Away
```

If process cannot obtain another resource,

OS forces it to release

everything it already owns.

Example:

```text
Holding Printer

Needs Scanner

Scanner Busy

↓

Release Printer
```

Disadvantage:

Only works for

```text
Preemptible Resources
```

Cannot safely take away:

```text
Printer

Mutex
```

during use.

---

## Breaking Circular Wait

Idea:

Assign every resource

a unique number.

Example:

```text
Mutex A = 1

Mutex B = 2

Mutex C = 3
```

Rule:

```text
Always Request

Lower Number

Before Higher Number
```

Correct:

```text
A

↓

B

↓

C
```

Wrong:

```text
C

↓

A
```

Since every process follows

the same order,

circular waiting cannot form.

This is one of the most common

real-world prevention techniques.

---

# Summary

```text
Break

Mutual Exclusion

or

Hold And Wait

or

No Preemption

or

Circular Wait

↓

Deadlock Impossible
```

---

# Interview Trick

Question:

```text
How Does Prevention Work?
```

Answer:

```text
By Breaking At Least One
Of Coffman's Four Conditions.
```

---

# 3. Deadlock Avoidance

Deadlock prevention is very strict.

It often wastes resources.

Instead,

Deadlock Avoidance asks:

```text
"If I Grant This Resource,

Will The System

Still Remain Safe?"
```

If:

```text
YES
```

Grant it.

Otherwise:

```text
NO
```

Make the process wait.

Avoidance is therefore

more intelligent than prevention.

---

# Deadlock Prevention vs Deadlock Avoidance

This is one of the most commonly asked OS interview questions. The key difference is **when the OS acts**. 🎯

| Deadlock Prevention                                                                  | Deadlock Avoidance                                                                                |
| ------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------- |
| Prevents deadlock by design.                                                         | Allows requests, but checks whether they are safe first.                                          |
| Breaks at least one of the four necessary conditions for deadlock.                   | Keeps all four conditions, but avoids entering an unsafe state.                                   |
| Decisions are made before deadlock becomes possible.                                 | Decisions are made at each resource request.                                                      |
| Simpler to implement.                                                                | More complex.                                                                                     |
| May reduce resource utilization and concurrency.                                     | Better resource utilization.                                                                      |
| Does not need maximum future resource requirements.                                  | Needs to know each process's maximum resource requirements in advance (e.g., Banker's Algorithm). |
| Example: Force processes to request all resources at once or impose a lock ordering. | Example: Banker's Algorithm grants a request only if the system remains in a safe state.          |

---

# Prevention 🛑

**Idea:** Make deadlock impossible by violating one of the four necessary conditions.

## Example

**Rule:**

```text
Always acquire Lock A before Lock B.
```

Now every thread follows the same order.

```text
Thread A: Lock A → Lock B

Thread B: Lock A → Lock B
```

Since no circular wait can form, deadlock is prevented.

---

# Avoidance 🚦

**Idea:** Deadlock is possible, but before granting a resource request, the OS asks:

> **"If I grant this request, will the system still be in a safe state?"**

* ✅ Yes → Grant the request.
* ❌ No → Make the process wait.

## Example

```text
P1 requests Printer.

OS checks:
Granting it keeps the system safe? ✔️
→ Grant it.

Later...

P2 requests another resource.

OS checks:
Granting it makes the system unsafe? ❌
→ Do not grant it now.
```

This avoids deadlock by refusing unsafe allocations.

---

# Memory Trick

```text
Prevention = Stop deadlock from ever becoming possible.

Avoidance = Deadlock is possible, but avoid unsafe decisions.
```

---

# Interview Answer (30 Seconds)

> **Deadlock prevention makes deadlock impossible by breaking one of the four necessary conditions, even if that reduces concurrency. Deadlock avoidance, on the other hand, allows resource requests but checks whether granting each request keeps the system in a safe state. If the state would become unsafe, the request is delayed. Banker's Algorithm is the classic deadlock avoidance algorithm.**

# 4. Safe State

A system is in a

Safe State

if

there exists

at least one order

in which

every process

can finish.

Example:

```text
P1

↓

P2

↓

P3
```

All complete successfully.

System is safe.

Important:

```text
Safe

≠

Deadlock
```

Safe simply means

deadlock can still be avoided.

---

# 5. Unsafe State

Unsafe does NOT mean

deadlock has already happened.

It means:

```text
Deadlock

May Occur

In Future.
```

Think of it as

standing near the edge of a cliff.

You have not fallen yet,

but one wrong step

causes disaster.

Memory Trick:

```text
Safe

Guaranteed Escape

Unsafe

No Guarantee
```

---

# Safe vs Unsafe

```text
Safe State

↓

Deadlock Impossible
```

```text
Unsafe State

↓

Deadlock Possible
```

Not guaranteed.

Possible.

---

# 6. Banker's Algorithm

Purpose:

```text
Avoid Deadlocks
```

Idea:

Before granting resources,

pretend they are allocated.

Then check:

```text
Can Every Process

Still Finish?
```

If yes

↓

Grant.

Otherwise

↓

Reject.

Banker's Algorithm therefore

works only for

Deadlock Avoidance.

---

## Information Required

For every process,

OS stores:

```text
Maximum Need

Allocated Resources

Remaining Need

Available Resources
```

Formula:

```text
Need

=

Maximum

-

Allocated
```

This formula is extremely important.

---

# Why Is It Called Banker's Algorithm?

Imagine a bank.

Customers request loans.

Bank grants a loan

only if

it is confident

everyone can eventually

repay.

Otherwise

loan is rejected.

Operating System

does exactly the same

with resources.

---

# 7. Deadlock Detection

Unlike prevention

or avoidance,

Detection allows

deadlocks to happen.

OS periodically checks

whether

deadlock exists.

If detected,

OS takes action.

Detection is useful

when deadlocks

are rare.

---

# 8. Deadlock Recovery

After detecting

deadlock,

OS must recover.

Methods:

---

## 1. Kill Process

Terminate

one or more processes.

Deadlock disappears.

Disadvantage:

```text
Work Lost
```

---

## 2. Resource Preemption

Take resources away

from selected processes.

Give them

to others.

Disadvantage:

Not always possible.

---

## 3. Rollback

Restore

a previously saved

checkpoint.

Retry execution.

Common in

Database Systems.

---

# 9. Ostrich Algorithm

Idea:

```text
Ignore Deadlocks
```

Why?

Because

deadlocks happen

very rarely.

Handling them

may cost

more than

simply restarting

the program.

Example:

Linux

UNIX

often follow

this philosophy

for certain resources.

---

# Comparison

```text
Prevention

↓

Break Coffman Conditions

------------------------

Avoidance

↓

Stay In Safe State

------------------------

Detection

↓

Allow Then Detect

------------------------

Recovery

↓

Kill

Rollback

Preempt

------------------------

Ostrich

↓

Ignore
```

---

# Interview FAQs

### Difference Between Prevention And Avoidance

```text
Prevention

Breaks Coffman Conditions.

Avoidance

Checks Safe State

Before Allocation.
```

---

### Safe State Means?

```text
Every Process

Can Finish

In Some Order.
```

---

### Unsafe State Means?

```text
Deadlock

May Happen.

Not Guaranteed.
```

---

### Formula Asked Most Frequently

```text
Need

=

Maximum

-

Allocated
```

---

# Part 2 Revision

```text
Deadlock Handling

↓

Prevention

↓

Avoidance

↓

Detection

↓

Recovery

↓

Ostrich
```

# Appendix - Concurrency Mental Models

## Why Doesn't a Thread Release Its Lock While Waiting?

Suppose:

```cpp
lock(A);
x = x + 5;
lock(B);
y = x + y;
unlock(B);
unlock(A);
```

If Thread A cannot get **Lock B**, should it release **Lock A**?

**No.**

Why?

Because it has only completed **half of its work**.

If it releases **Lock A**, another thread can acquire it and modify `x`.

When Thread A wakes up, its earlier assumption about `x` is no longer true.

The operation is no longer atomic and may produce an incorrect result (race-condition-like behavior).

---

# Deadlock vs Livelock

## Deadlock

```text
Wait...
Wait...
Wait...
```

* Threads are blocked.
* No execution.
* No progress.

---

## Livelock

```text
Execute...
Retry...
Execute...
Retry...
```

* Threads keep executing.
* No useful progress is made.

**Important:**

Executing instructions **does not mean** the task is progressing.

The threads keep executing only their **retry logic**, not completing the actual work.

---

## Why Is There No Progress?

Core example:

```text
Thread A:
Gets Lock A
Needs Lock B
Can't get B
Releases Lock A

Thread B:
Gets Lock B
Needs Lock A
Can't get A
Releases Lock B
```

Timeline:

```text
A gets A.
B gets B.

A releases A.
B releases B.

A gets A again.
B gets B again.

Repeat...
```

They keep **undoing their own work**.

Neither thread ever owns **both locks** at the same time, so the real task never completes.

---

## Why "Forever"?

"Forever" assumes both threads always retry with exactly the same timing.

In real systems, scheduling usually changes, allowing one thread to proceed.

---

# Is This a Spinlock?

No.

A **spinlock** is simply busy waiting.

```cpp
while(lock is busy) {
    // Keep checking
}
```

Example:

```text
Thread A:
Has Lock L

Thread B:
Checks if L is free.
Checks again.
Checks again.

A releases L.

B gets L.
Continues.
```

Here, Thread B is only **waiting**.

It does **not** keep changing its behavior.

Eventually, it gets the lock and makes progress.

---

# Spinlock vs Livelock

### Spinlock

```text
Wait...
Wait...
Wait...
Lock becomes free.
Continue.
```

* Busy waiting.
* Waiting for one lock.
* Eventually progresses.

---

### Livelock

```text
Acquire.
Release.
Retry.

Acquire.
Release.
Retry.
```

* Keeps changing state.
* Threads react to each other.
* Keep interfering.
* No useful progress.

---

## Memory Trick

```text
Deadlock = Sleeping forever.

Spinlock = Waiting forever (until the lock is released).

Livelock = Moving forever without progress.
```

---

## Interview One-Liners

* **Deadlock:** Threads wait forever.
* **Spinlock:** Thread continuously checks for a lock until it becomes available.
* **Livelock:** Threads keep executing and retrying, but because they continuously interfere with each other, no useful work is completed.
## Spin Lock is one of reason for hold and wait in deadlock.

# Part 3 - Numerical Problems, Interview Questions & Revision

---

# 1. Numerical Problems

## Resource Allocation Graph (RAG)

Know how to identify:

- Resource Request Edge
- Resource Allocation Edge
- Cycle
- Deadlock

Interview Fact:

```text
Single Resource Instance

Cycle
=
Deadlock

-------------------------

Multiple Resource Instances

Cycle
≠
Always Deadlock
```

---

## Banker's Algorithm

Be able to solve:

```text
Available

Maximum

Allocated

Need
```

Formula:

```text
Need

=

Maximum

-

Allocated
```

Interview Questions:

- Find Need Matrix.
- Find Safe Sequence.
- Is the system Safe or Unsafe?
- Should the OS grant this request?

---

# 2. Most Asked Interview Questions

## Q1. What is a Deadlock?

```text
Processes wait forever because each waits for a resource held by another process.
```

---

## Q2. What are Coffman's Conditions?

```text
Mutual Exclusion

Hold And Wait

No Preemption

Circular Wait
```

---

## Q3. Difference between Prevention and Avoidance?

```text
Prevention

Breaks Coffman's Conditions.

--------------------------

Avoidance

Allows Requests

Only If

System Remains Safe.
```

---

## Q4. Safe State vs Unsafe State?

```text
Safe

Every Process
Can Finish.

-------------------

Unsafe

Deadlock
May Occur.
```

---

## Q5. Difference between Deadlock and Starvation?

```text
Deadlock

Nobody Progresses.

---------------------

Starvation

Others Progress

One Process Waits Forever.
```

---

## Q6. Difference between Deadlock and Livelock?

```text
Deadlock

No Execution.

--------------------

Livelock

Execution Happens

No Useful Progress.
```

---

## Q7. What is Banker's Algorithm?

```text
Deadlock Avoidance Algorithm

Checks Whether

Granting Resources

Keeps System Safe.
```

---

## Q8. Formula Asked Most Often

```text
Need

=

Maximum

-

Allocated
```

---

# 3. Complete Deadlock Flow

```text
Multiple Processes
        ↓
Limited Resources
        ↓
Processes Hold Resources
        ↓
Processes Request More Resources
        ↓
Four Coffman Conditions
Satisfied
        ↓
Deadlock

-------------------------

Handle Deadlock

↓

Prevention

or

Avoidance

or

Detection

↓

Recovery
```

---

# 4. One-Page Revision

```text
Deadlock
=
Processes Wait Forever

----------------------

4 Conditions

Mutual Exclusion

Hold And Wait

No Preemption

Circular Wait

----------------------

Break Any One

↓

Deadlock Impossible

----------------------

Avoidance

↓

Safe State

↓

Banker's Algorithm

----------------------

Detection

↓

Find Deadlock

↓

Recovery

Kill

Rollback

Preemption

----------------------

Ostrich

↓

Ignore Deadlock
```

---

# Interview Checklist ✅

```text
✓ What is Deadlock?

✓ Coffman's Conditions

✓ Resource Allocation Graph

✓ Safe vs Unsafe State

✓ Prevention

✓ Avoidance

✓ Banker's Algorithm

✓ Detection

✓ Recovery

✓ Ostrich Algorithm

✓ Deadlock vs Starvation

✓ Deadlock vs Livelock

✓ Need = Maximum - Allocated
```
