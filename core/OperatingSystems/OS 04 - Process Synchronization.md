# OS 04 - Process Synchronization

## Navigation

### Part 1 - Why Synchronization Exists
- [1. Why Do We Need Synchronization?](#1-why-do-we-need-synchronization)
- [2. Shared Resources](#2-shared-resources)
- [3. Race Condition](#3-race-condition)
- [4. Why Race Conditions Occur](#4-why-race-conditions-occur)
- [5. Critical Section](#5-critical-section)
- [6. Critical Section Problem](#6-critical-section-problem)
- [7. Requirements Of A Good Solution](#7-requirements-of-a-good-solution)
  - [Mutual Exclusion](#mutual-exclusion)
  - [Progress](#progress)
  - [Bounded Waiting](#bounded-waiting)
- [8. Hardware View Of Race Conditions](#8-hardware-view-of-race-conditions)
- [9. Atomic Operations](#9-atomic-operations)
- [10. Busy Waiting](#10-busy-waiting)

### Part 2 - Synchronization Tools
- [11. Mutex](#11-mutex)
- [12. Semaphore](#12-semaphore)
- [13. Binary Semaphore](#13-binary-semaphore)
- [14. Counting Semaphore](#14-counting-semaphore)
- [15. Spinlock](#15-spinlock)
- [16. Monitor](#16-monitor)
- [17. Condition Variables](#17-condition-variables)

### Part 3 - Classical Synchronization Problems
- [Producer Consumer Problem](#producer-consumer-problem)
- [Classical Synchronization Problems](#classical-synchronization-problems)
  - [Readers-Writers Problem](#1-readers-writers-problem)
  - [Dining Philosophers Problem](#2-dining-philosophers-problem)
  - [Producer-Consumer Problem](#3-producer-consumer-problem)
- [Synchronization vs Deadlock](#synchronization-vs-deadlock)
- [Interview FAQs](#interview-faqs)
  - [Race Condition](#race-condition)
  - [Critical Section](#critical-section)
  - [Synchronization vs Communication](#synchronization-vs-communication)
  - [Mutex](#mutex)
  - [Semaphore](#semaphore)
  - [Binary Semaphore vs Mutex](#binary-semaphore-vs-mutex)
  - [Spinlock vs Mutex](#spinlock-vs-mutex)
  - [Monitor](#monitor)
  - [Condition Variable](#condition-variable)
- [Quick Revision](#quick-revision)
- [OS 04 - Quick Code Reference](#os-04---quick-code-reference-high-roi)
  - [Mutex](#1-mutex)
  - [Binary Semaphore](#2-binary-semaphore)
  - [Counting Semaphore](#3-counting-semaphore)
  - [Condition Variable](#4-condition-variable)
  - [Producer-Consumer Code](#5-producer-consumer-classical-solution)
- [Why `while` Instead of `if`?](#why-do-we-use-while-instead-of-if-with-a-condition-variable)

---

# Part 1 - Why Synchronization Exists

---

# 1. Why Do We Need Synchronization?

Before learning:

```text
Mutex
Semaphore
Monitor
```

we must understand:

```text
What Problem Are They Solving?
```

---

Most students memorize:

```text
Mutex = Lock

Semaphore = Counter
```

and fail interviews.

Interviewers want:

```text
Why Was Mutex Invented?
```

---

The answer begins with:

```text
Shared Resources
```

---

# 2. Shared Resources

A shared resource is any resource that multiple processes or threads can access.

Examples:

```text
Shared Variable

File

Database Row

Printer

Network Socket

Memory Location
```

---

Example

```c
int count = 0;
```

Suppose:

```text
Thread A

Thread B
```

both access:

```c
count
```

Then:

```text
count
```

becomes a:

```text
Shared Resource
```

---

# Why Shared Resources Are Dangerous

data from global memory is loaded into registers, operated on, and before it is written back another thread may have updated the actual variable, so my thread is performing calculations on an outdated value

Suppose:

```c
count++;
```

looks harmless.

Most beginners think:

```text
One Line
=
One Operation
```

Wrong.

CPU never sees:

```c
count++;
```

as a single operation.

---

Internally:

```text
Load count into Register

Add 1

Store Result Back
```

---

Example

Initially:

```text
count = 5
```

---

Thread A:

```text
Load 5
```

Thread B:

```text
Load 5
```

Thread A:

```text
Add 1
```

Result:

```text
6
```

Thread B:

```text
Add 1
```

Result:

```text
6
```

Thread A:

```text
Store 6
```

Thread B:

```text
Store 6
```

Final:

```text
6
```

Expected:

```text
7
```

---

One increment disappeared.

This is the beginning of synchronization.

---

# 3. Race Condition

Definition:

A race condition occurs when multiple execution units access shared data concurrently and the final result depends on execution order.

Race condition occurs because one thread may operate on a stale copy of shared data stored in its registers while another thread has already updated the actual shared memory.
---


Simple Meaning

Threads are:

```text
Racing
```

to modify data.

Who executes first changes outcome.

---

Example

Expected:

```text
count = 7
```

Actual:

```text
count = 6
```

or

```text
count = 7
```

depending on scheduling.

---

Output becomes:

```text
Unpredictable
```

---

# Interview Definition

```text
Race Condition occurs when multiple threads or processes
access shared data simultaneously and the result depends
on the timing of execution.
```

---

# 4. Why Race Conditions Occur

Root cause:

```text
Context Switching
```

---

Remember OS 02?

Thread can stop at any instruction.

Example:

```text
Thread A

Load Count
```

OS performs:

```text
Context Switch
```

Now:

```text
Thread B
```

runs.

---

Because execution can stop anywhere:

```text
Shared Data Can Be Corrupted
```

---

Without context switching:

```text
No Race Condition
```

would occur.

---

# 5. Critical Section

Critical Section is the most important term in synchronization.

---

Definition

Part of code that accesses shared resources.

---

Example

```c
count++;
```

is a critical section.

---

Example

```c
balance = balance - 100;
```

is a critical section.

---

Example

```c
insertIntoDatabase();
```

may be a critical section.

---

Why Critical?

Because incorrect execution causes:

```text
Data Corruption
```

---

# Non-Critical Section

Example:

```c
int x = 10;
```

inside a thread's local stack.

Not shared.

Not critical.

---

# Easy Rule

Ask:

```text
Can Another Thread Access This Data?
```

If:

```text
Yes
```

likely critical.

---

# 6. Critical Section Problem

Question:

```text
How Do We Allow Multiple Threads
To Run Safely?
```

---

Bad Solution

```text
Allow Everyone Inside
```

Race conditions occur.

---

Desired Solution

```text
One Thread Enters

Others Wait
```

---

Diagram

```text
Critical Section

Thread A
   ↓
Inside

Thread B
   ↓
Wait

Thread C
   ↓
Wait
```

---

When A leaves:

```text
B enters
```

---

Goal:

```text
Protect Shared Data
```

---

# 7. Requirements Of A Good Solution

Any synchronization solution must satisfy:

```text
Mutual Exclusion

Progress

Bounded Waiting
```

Interview favorite.

---

# Mutual Exclusion

Most important requirement.

---

Definition

At most one thread may execute inside critical section at a time.

---

Example

Allowed:

```text
Thread A Inside

Thread B Waiting
```

Not Allowed:

```text
Thread A Inside

Thread B Inside
```

simultaneously.

---

Why?

Race condition returns.

---

# Progress

If no thread is inside critical section:

```text
Someone Should Be Allowed In
```

---

Bad System

```text
Nobody Inside

Everyone Waiting
```

forever.

---

Progress prevents this.

---

# Bounded Waiting

No thread should wait forever.

---

Bad System

```text
A enters

B waits

A enters again

B waits

A enters again

B waits forever
```

---

Bounded waiting guarantees:

```text
Eventually B Gets Turn
```

---

# Easy Memory Trick

```text
Mutual Exclusion
=
One At A Time

Progress
=
Don't Freeze

Bounded Waiting
=
No Starvation
```

---

# 8. Hardware View Of Race Conditions

Remember:

```c
count++;
```

is actually:

```text
LOAD

ADD

STORE
```

---

CPU executes instructions individually.

Context switch can occur between them.

---

Example

```text
LOAD
     ↑
Context Switch Here
```

Disaster.

---

Race conditions occur because:

```text
Operations Are Not Atomic
```

---

# 9. Atomic Operations

Atomic means:

```text
Indivisible
```

---

Cannot be interrupted midway.

---

Example

Think:

```text
Single Unit
```

---

CPU guarantees operation completes entirely.

---

Example

```text
Atomic Operation

Start
 ↓
Finish
```

No interruption.

---

Atomic operations are foundation of:

```text
Mutex

Semaphores

Locks
```

---

# 10. Busy Waiting

Suppose lock unavailable.

Thread continuously checks:

```c
while(lock == 1)
{
}
```

---

Thread keeps running.

CPU keeps working.

No useful work performed.

---

Called:

```text
Busy Waiting
```

or:

```text
Spinning
```

---

Problem

```text
Wastes CPU
```

---

Why Learn This?

Because:

```text
Mutex

Semaphore

Spinlock
```

will all discuss:

```text
Busy Waiting
```

later.

---

# End Of Part 1

You now understand:

```text
Shared Resource
      ↓
Race Condition
      ↓
Critical Section
      ↓
Critical Section Problem
      ↓
Need Mutual Exclusion
      ↓
Need Synchronization Tools
```

Next:

```text
Mutex

Semaphore

Monitor

Condition Variables
```

which are solutions to everything learned above.

# 11. Mutex

Most common synchronization tool.

Interview favorite.

---

## What Problem Does Mutex Solve?

Suppose:

```c
count++;
```

is critical section.

Multiple threads executing it causes:

```text
Race Condition
```

Need:

```text
One Thread At A Time
```

---

## Idea

Mutex means:

```text
Mutual Exclusion
```

Meaning:

```text
If One Thread Is Inside

Others Must Wait
```

---

## Real Life Analogy

Think:

```text
Bathroom
```

with:

```text
One Key
```

---

If:

```text
Thread A
```

has key:

```text
Inside
```

---

Thread B:

```text
Waits
```

---

When A leaves:

```text
Returns Key
```

B can enter.

---

## Internal View

Mutex contains:

```text
Locked
```

or

```text
Unlocked
```

state.

---

## Operations

```text
Lock()

Unlock()
```

---

## Example

```c
lock();

count++;

unlock();
```

---

Execution:

```text
Thread A
Lock
 ↓
Critical Section
 ↓
Unlock

Thread B
Wait
```

---

## Why Mutex Works

Because:

```text
Read
Modify
Write
```

becomes protected.

No other thread can interfere.

---

## Advantages

```text
Simple

Fast

Easy To Understand
```

---

## Disadvantages

If thread forgets:

```c
unlock();
```

other threads may wait forever.

---

# 12. Semaphore

More powerful than mutex.

Introduced by:

```text
Edsger Dijkstra
```

Important interview fact.

---

## Why Do We Need Semaphore?

Mutex manages:

```text
One Resource
```

at a time.

Sometimes:

```text
Multiple Resources
```

exist.

Need more flexibility.

---

## Semaphore Idea

Semaphore is:

```text
Integer Counter
```

used to control access.

---

## Two Operations

```text
wait()
```

and

```text
signal()
```

Historically:

```text
P()
V()
```

---

## wait()

Requests resource.

Counter decreases.

---

## signal()

Returns resource.

Counter increases.

---

## Example

Initial:

```text
Semaphore = 3
```

Three resources available.

---

Thread A:

```text
wait()
```

Counter:

```text
3 → 2
```

---

Thread B:

```text
wait()
```

Counter:

```text
2 → 1
```

---

Thread C:

```text
wait()
```

Counter:

```text
1 → 0
```

---

Thread D:

```text
wait()
```

Counter:

```text
0
```

must wait.

---

# Why Semaphore Works

Counter tracks:

```text
Available Resources
```

---

# Semaphore Mental Model

Semaphore is basically a counter.

It keeps track of how many instances of a resource are available.

If semaphore value = N:

- Up to N threads may proceed.
- Each thread entering decreases the count.
- Each thread leaving increases the count.
- When count becomes 0, new threads must wait.

Use semaphore when there are multiple identical resource instances available.

Examples:
- 5 buffer slots
- 20 worker threads
- 4 GPUs
- 10 database connections

Memory Trick:

Mutex = Who may enter? (Only 1)

Semaphore = How many may enter? (Up to N)

NOT:
"N threads in a critical section"

BETTER:
"N threads accessing N available resource instances"

Because a critical section that modifies shared data is usually still protected by a mutex (one thread at a time). Semaphore is more about controlling access to a pool of available resources

# 13. Binary Semaphore

Special semaphore.

---

## Values

Only:

```text
0

1
```

---

Looks similar to:

```text
Mutex
```

---

## Example

Initial:

```text
1
```

Thread enters:

```text
wait()
```

Counter:

```text
1 → 0
```

---

Next thread:

```text
Must Wait
```

---

## Difference From Mutex

Mutex:

```text
Ownership Exists
```

Only owner unlocks.

---

Binary Semaphore:

```text
No Ownership Concept
```

Any thread may signal.

---

## Interview Answer

```text
Binary Semaphore behaves like mutex
but does not enforce ownership.
```

---

# Binary Semaphore vs Mutex

## Similarity

Both can allow:

```text
Only One Thread
```

at a time.

Binary Semaphore values:

```text
0 or 1
```

Mutex values:

```text
Locked or Unlocked
```

So they often look identical.

---

## Core Difference

### Mutex

```text
Ownership Exists
```

Thread that does:

```text
lock()
```

must do:

```text
unlock()
```

Only the owner can release it.

---

### Binary Semaphore

```text
No Ownership
```

Thread A may do:

```text
wait()
```

Thread B may do:

```text
signal()
```

Perfectly valid.

---

## Mental Model

```text
Mutex
=
Lock With Owner
```

```text
Binary Semaphore
=
0/1 Counter
```

---

## When To Use

### Mutex

Protect:

```text
Shared Variable

Critical Section

Shared Data
```

### Binary Semaphore

Used for:

```text
Thread Synchronization

Producer → Consumer Signaling

Event Notification
```

---

## Interview One-Liner

```text
Mutex enforces ownership:
the thread that locks must unlock.

Binary Semaphore has no ownership:
one thread may wait() and another may signal().
```



# 14. Counting Semaphore

General semaphore.

---

## Values

Can be:

```text
0
1
2
3
...
N
```

---

## Example

Suppose:

```text
5 Database Connections
```

available.

Semaphore:

```text
5
```

---

Five threads may use database simultaneously.

---

Sixth thread:

```text
Waits
```

---

## Use Cases

```text
Connection Pools

Printer Pools

Thread Pools
```

---

## Waiting Behavior

When a thread tries to acquire a locked mutex:

- OS usually blocks (sleeps) the thread.
- Thread is moved to a waiting state.
- CPU is given to another runnable thread.
- When mutex becomes available, the waiting thread is awakened.

Therefore:

Mutex
=
Sleep While Waiting
(No CPU Wastage)

# 15. Spinlock

Special lock.

Very important in kernels.

---

## Problem

Suppose lock unavailable.

Thread waits.

Question:

```text
How?
```

---

Spinlock Answer

```c
while(lock)
{
}
```

---

Thread continuously checks.

---

## Why Called Spinlock?

Thread:

```text
Spins
```

inside loop.

---

## Advantage

No context switch.

---

## Disadvantage

Consumes CPU.

---

## Use Case

Very short critical sections.

Common inside:

```text
Operating System Kernel
```

---

# Mutex vs Spinlock

Mutex:

```text
Sleep While Waiting
```

---

Spinlock:

```text
Keep Running
And Checking
```

---

## Rule

Short Wait:

```text
Spinlock
```

Long Wait:

```text
Mutex
```

---
# Threads in Mutex vs Spinlock

```text
Mutex
=
Sleep While Waiting

Spinlock
=
Keep Checking While Waiting
```

If lock wait time is long:

```text
Use Mutex
```

because sleeping avoids CPU wastage.

If lock wait time is extremely short:

```text
Use Spinlock
```

because it avoids expensive context-switch and wake-up overhead.

Memory Trick:

```text
Mutex = Sleep

Spinlock = Spin
```


# 16. Monitor

High-level synchronization construct.

---

## Problem

Programmers forget:

```text
Lock

Unlock
```

---

Leads to bugs.

---

## Idea

Synchronization built into structure itself.

---

Think:

```text
Smart Mutex
```

---

Monitor automatically ensures:

```text
One Thread Inside
```

---

## Example

Java:

```java
synchronized
```

uses monitor concept.

---

## Advantages

```text
Safer

Cleaner

Less Error-Prone
```

---

## Disadvantages

More complex internally.

```text
Monitor
=
High-Level Synchronization Construct
```

Automatically provides:

```text
Mutual Exclusion
```

and manages locking internally.

```text
Mutex = Manual Lock/Unlock

Monitor = Safer Automatic Locking
```

Helps avoid lock/unlock mistakes and makes synchronization code cleaner.


# 17. Condition Variables

```text
Mutex
=
Who May Enter?

Condition Variable
=
When Should A Waiting Thread Wake Up?
```

Used when a thread cannot proceed until some condition becomes true.

Example:

```text
Buffer Empty
→ Consumer Sleeps
```

Producer adds data:

```text
notify()
```

Consumer wakes up and tries to acquire the mutex.

```text
Sleep
↓
Wake Up
↓
Get Mutex
↓
Continue
```

Sleeping avoids wasting CPU by repeatedly checking the condition.

# Mutex vs Condition Variable - Core Mental Model

At a high level, both follow the same workflow:

```text
Cannot Proceed
        ↓
Sleep
        ↓
Something Changes
        ↓
Wake Up
        ↓
Continue
```

The difference is **what they are waiting for**.

```text
Mutex
Waiting For
→ Lock To Become Free
```

```text
Condition Variable
Waiting For
→ Some Condition To Become True
```

Think of it this way:

```text
Mutex
→ "Is the critical section free?"
```

```text
Condition Variable
→ "Can I continue now?"
```

or

```text
"Is there work to do?"
```

A mutex typically wakes **one** waiting thread when the lock is released.

A condition variable can wake:

```text
notify()/signal()
→ One Waiting Thread

notify_all()/broadcast()
→ All Waiting Threads
```

In Producer-Consumer:

```text
Mutex
→ Ensures Only One Thread Accesses The Buffer

Condition Variable
→ Sleeps/Wakes Producer Or Consumer
When Buffer State Changes
```

### Memory Trick

```text
Mutex
=
Who May Enter?

Condition Variable
=
When Should I Wake Up?
```
if the scenarios is i have to make a thread wait and awake it based on condition then conditional variable ,

# Part 2 Summary

Problem:

```text
Race Condition
```

---

Solutions:

```text
Mutex
=
One Thread At A Time

Semaphore
=
Counter Based Access Control

Binary Semaphore
=
0 Or 1 Resource

Counting Semaphore
=
N Resources

Spinlock
=
Busy Waiting

Monitor
=
High Level Synchronization

Condition Variable
=
Wait For Event
```
so mutex is there but the thread after waking can get or still wait for some time ? Y 
---

# Connection To Part 3

Now we know:

```text
Mutex

Semaphore

Monitor
```

Next question:

```text
Can We Use Them
To Solve Real Problems?
```

That leads to:

```text
Producer Consumer

Readers Writers

Dining Philosophers
```

 
# Part 3 - Classical Synchronization Problems

---

# Producer Consumer Problem

Shared Buffer:

```text
Producer → Adds Data

Consumer → Removes Data
```

Problems:

```text
Producer must not add if Buffer Full

Consumer must not remove if Buffer Empty
```

Solution:

```text
Mutex
→ Protect Buffer

Semaphore / Condition Variable
→ Track Empty/Full State
```

---
in simple so mutex for only one can access buffer and condition varable (old was semaphore here )to wake up producer or consumer .. ? Y
```text
Mutex
=
Protect Shared Buffer

Condition Variable
=
Sleep/Wake Threads Based On Buffer State

Buffer Empty
→ Consumer Sleeps

Buffer Full
→ Producer Sleeps

Producer/Consumer Wake Each Other
When Condition Changes
```

# Classical Synchronization Problems

---

# 1. Readers-Writers Problem

## Scenario

A shared database/file is accessed by:

```text
Readers
Writers
```

---

## Rules

```text
Multiple Readers Allowed

Only One Writer Allowed

Reader And Writer Cannot Access Together
```

---

## Why?

```text
Reading Does Not Modify Data

Writing Modifies Data
```

Therefore:

```text
Many Readers Can Read Simultaneously

Writer Needs Exclusive Access
```

---

## Standard Idea

```text
First Reader
→ Blocks Writers

Additional Readers
→ Join Immediately

Last Reader
→ Allows Writers Again
```

---

## Interview Catch

### Reader Starvation

If readers continuously arrive:

```text
read_count never becomes 0
```

Writer may wait forever.

---

# 2. Dining Philosophers Problem

## Scenario

```text
5 Philosophers

5 Forks
```

To eat:

```text
Need Left Fork

Need Right Fork
```

---

## Deadlock Scenario

Everyone picks:

```text
Left Fork
```

Then waits for:

```text
Right Fork
```

Result:

```text
Nobody Can Proceed

Deadlock
```

---

## Solution 1: Resource Ordering

Rule:

```text
Odd Philosophers
→ Left Then Right

Even Philosophers
→ Right Then Left
```

Why?

```text
Breaks Circular Wait
```

---

## Solution 2: Waiter / Arbitrator

Rule:

```text
Ask Permission Before Taking Forks
```

Waiter allows eating only when both forks are available.

Why?

```text
Prevents Hold And Wait
```

---

## Solution 3: Limit Philosophers

Allow:

```text
Maximum 4 Philosophers
```

for:

```text
5 Forks
```

Why?

```text
At Least One Philosopher
Can Always Obtain Two Forks
```

Deadlock becomes impossible.

---

# 3. Producer-Consumer Problem

## Scenario

```text
Producer
→ Adds Data

Consumer
→ Removes Data
```

Shared Buffer:

```text
Fixed Size = N
```

---

## Constraints

```text
Buffer Full
→ Producer Must Wait

Buffer Empty
→ Consumer Must Wait
```

---

## Problems To Solve

### Problem 1

```text
Producer And Consumer
Cannot Modify Buffer Together
```

Solution:

```text
Mutex
```

---

### Problem 2

```text
Consumer Should Sleep
When Buffer Empty

Producer Should Sleep
When Buffer Full
```

Solution:

```text
Condition Variable
(or Semaphores)
```

---

## Mental Model

```text
Mutex
=
Protect Buffer

Condition Variable
=
Wake Sleeping Producer/Consumer

Semaphore
=
Track Empty/Full Slots
```

---

# Interview Summary

```text
Readers-Writers
→ Shared Data Access

Dining Philosophers
→ Deadlock Due To Resource Allocation

Producer-Consumer
→ Synchronization + Sleep/Wake Coordination
```


# Synchronization vs Deadlock

Synchronization:

```text
Used To Prevent Race Conditions
```

Deadlock:

```text
May Occur Because Of Synchronization
```

Example:

```text
Thread A Holds Lock 1
Waiting For Lock 2

Thread B Holds Lock 2
Waiting For Lock 1
```

Both wait forever.

---

# Interview FAQs

## Race Condition

```text
Multiple Threads Access Shared Data

Result Depends On Execution Order
```

---


## Critical Section

```text
Code That Accesses Shared Data
```

---
## Synchronization vs Communication

```text
Synchronization
=
Coordinate Access To Shared Data

Communication
=
Exchange Data Between Threads/Processes
```

---

## Mutex

```text
Who May Enter?

Owner Must Unlock
```

---

## Semaphore

```text
Counter Of Available Resources

Controls How Many Threads May Proceed
```

---

## Binary Semaphore vs Mutex

```text
Mutex
→ Ownership Exists

Binary Semaphore
→ No Ownership
```

---

## Spinlock vs Mutex

```text
Mutex
→ Sleep While Waiting

Spinlock
→ Keep Checking While Waiting
```

---

## Monitor

```text
Safer High-Level Synchronization

Automatic Lock Management
```

---

## Condition Variable

```text
When Should A Waiting Thread Wake Up?
```

---

# Quick Revision

```text
Race Condition
        ↓
Critical Section
        ↓
Need Synchronization
        ↓
Mutex
Semaphore
Monitor
        ↓
Condition Variable
        ↓
Producer Consumer
Readers Writers
Dining Philosophers
        ↓
Deadlock
```

# OS 04 - Quick Code Reference (High ROI)

---

# 1. Mutex

**Purpose**

```text
Protect Critical Section
```

```cpp
lock(mutex);

/* Critical Section */

unlock(mutex);
```

---

# 2. Binary Semaphore

**Purpose**

```text
Acts Like Mutex
(No Ownership)
```

```cpp
wait(binarySemaphore);

/* Critical Section */

signal(binarySemaphore);
```

---

# 3. Counting Semaphore

**Purpose**

```text
Control N Resource Instances
```

```cpp
wait(S);

/* Use Resource */

signal(S);
```

---

# 4. Condition Variable

**Purpose**

```text
Sleep Until Condition Becomes True
```

```cpp
lock(mutex);

while(!condition)
    wait(cv, mutex);

/* Critical Section */

unlock(mutex);
```

---

# 5. Producer-Consumer (Classical Solution)

```cpp
semaphore mutex = 1;
semaphore empty = N;
semaphore full = 0;
```

Producer:

```cpp
wait(empty);
wait(mutex);

/* Insert Item */

signal(mutex);
signal(full);
```

Consumer:

```cpp
wait(full);
wait(mutex);

/* Remove Item */

signal(mutex);
signal(empty);
```

---

# Interview Points ⭐

```text
Mutex
→ Protect Shared Data

Binary Semaphore
→ Mutex Without Ownership

Counting Semaphore
→ Count Available Resources

Condition Variable
→ Sleep Until Condition Becomes True

Producer-Consumer
→ Mutex + Semaphore
(Classical OS)

or

Mutex + Condition Variable
(Modern Programming)
```

---

# Must Remember

```text
Mutex
→ Wait For Lock

Condition Variable
→ Wait For Condition

Semaphore
→ Wait For Resource
```

# Why do we use `while` instead of `if` with a Condition Variable?

```cpp
lock(mutex);

while (!condition)
    wait(cv, mutex);

/* Critical Section */

unlock(mutex);
```

### Why the `while` loop?

The loop is **not** because `wait()` sleeps repeatedly.

It is used to **recheck the condition after every wake-up**.

### Thread Execution

1. Lock the mutex.
2. Check the condition.
3. If the condition is `false`:
   - `wait(cv, mutex)` **atomically**:
     - Releases the mutex.
     - Puts the thread to sleep.
4. Another thread changes the condition and calls `notify_one()` or `notify_all()`.
5. The waiting thread wakes up.
6. Before `wait()` returns, it **reacquires the mutex**.
7. The `while` loop checks the condition **again**.

### Why check again?

Because waking up **does not guarantee** the condition is true.

Possible reasons:
- **Spurious wakeup** (OS may wake a thread without notification).
- Another thread changed the condition before this thread got the mutex.
- Multiple waiting threads woke up, but only one could use the resource.

### Example

Suppose two consumers are waiting for an item.

```
Producer:
itemCount = 1;
notify_all();
```

Both consumers wake up.

- Consumer B gets the mutex first.
- Removes the item (`itemCount = 0`).
- Consumer A gets the mutex later.

If Consumer A used:

```cpp
if (!condition)
    wait(cv, mutex);
```

it would continue into the critical section even though `itemCount == 0`. ❌

With:

```cpp
while (!condition)
    wait(cv, mutex);
```

Consumer A checks the condition again, finds it false, and goes back to sleep. ✅

### Rule to Remember

> **Always use `while`, never `if`, with condition variables.**

The `while` loop exists to **recheck the condition after every wake-up**, ensuring correctness even with spurious wakeups or multiple waiting threads.

