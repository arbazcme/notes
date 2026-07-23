#  LEC 17 - Concurrency & Parallelism

# Navbar

- [17A - Why Concurrency Matters](#lec-17a---why-concurrency-matters)
- [17B - Concurrency vs Parallelism](#lec-17b---concurrency-vs-parallelism)
- [17C - I/O-bound vs CPU-bound](#lec-17c---io-bound-vs-cpu-bound)
- [17D - Threads](#lec-17d---threads-deep-dive)
- [17E - Event Loop](#lec-17e---event-loop-deep-dive)
- [17F - Thread Model vs Event Loop Model](#lec-17f---thread-model-vs-event-loop-model)
- [17G - Goroutines (Go's Concurrency Model)](#lec-17g---goroutines-gos-concurrency-model)
- [17H - Choosing the Right Concurrency Model](#lec-17h---choosing-the-right-concurrency-model)

# LEC 17A - Concurrency & Parallelism (Part 1) - Why Concurrency Matters

# Why Do Backend Servers Need Concurrency?

A backend server rarely serves just **one user**.

In production,

thousands (or millions) of users may send requests simultaneously.

```text
User A ─┐
User B ─┤
User C ─┤
User D ─┤
        ▼
   Backend Server
```

If the server handled **only one request at a time**,

everyone else would have to wait.

That is unacceptable for modern applications. :contentReference[oaicite:0]{index=0}

---

# A Typical Backend Request

Every request usually follows this flow.

```text
Client

↓

Backend

↓

Database / Cache / External API

↓

Backend

↓

Client
```

Most backend requests need to communicate with another service before responding. :contentReference[oaicite:1]{index=1}

---

# The Hidden Problem

Suppose your backend sends a query to the database.

```text
Backend

↓

Database

↓

Waiting...
```

The database may take

- 2 ms (localhost)
- 30 ms (same region)
- 100 ms (different region)

During this waiting period,

what is the CPU doing?

👉 **Nothing.**

It simply waits for the database response. :contentReference[oaicite:2]{index=2}

---

# CPU Idle Time

Modern CPUs execute billions of instructions every second.

If your server waits

```text
100 ms
```

for a database response,

the CPU could have executed **hundreds of millions of instructions**.

Instead,

it executes **zero**.

```text
CPU

Working ❌

Waiting ✔
```

This is wasted processing power. :contentReference[oaicite:3]{index=3}

---

# Real Backend Requests

A single API usually performs multiple I/O operations.

Example

```text
Request

↓

Database

↓

Redis

↓

Email Service

↓

File Storage

↓

Response
```

Each external service introduces waiting time. :contentReference[oaicite:4]{index=4}

---

# Where Does Time Go?

Example

```text
CPU Work

10 ms
```

```text
Waiting for I/O

250 ms
```

Total

```text
260 ms
```

Only

```text
10 ms
```

used the CPU.

The remaining

```text
250 ms
```

was spent waiting.

So the CPU was idle for roughly

```text
95%
```

of the request lifetime. :contentReference[oaicite:5]{index=5}

---

# What is I/O?

I/O (Input/Output) means communicating with external resources.

Examples

- Database queries
- Redis
- HTTP API calls
- File system
- Network sockets
- Reading files
- Writing files

Whenever your program waits for another system,

it is performing **I/O**. :contentReference[oaicite:6]{index=6}

---

# The Core Problem

Without concurrency

```text
Request A

↓

Wait for Database

↓

CPU Idle
```

The CPU wastes valuable time.

---

# The Solution

Instead of waiting,

the CPU should process another request.

```text
Request A

↓

Waiting for Database

↓

CPU switches

↓

Request B

↓

Request C

↓

Request D
```

When Request A's database response arrives,

the CPU resumes Request A.

This is the basic idea behind **concurrency**. :contentReference[oaicite:7]{index=7}

---

# Mental Model

Think of a chef.

Without concurrency

```text
Cook Rice

↓

Stand Still

↓

Wait 20 Minutes
```

Most of the chef's time is wasted.

With concurrency

```text
Cook Rice

↓

While Rice Cooks

↓

Prepare Curry

↓

Cut Vegetables

↓

Serve Drinks
```

The chef keeps working while waiting.

Backend servers work the same way.

---

# Why This Matters

Concurrency allows the server to

- Keep the CPU busy
- Handle many requests simultaneously
- Improve throughput
- Reduce wasted resources

It **does not make the database faster**.

It simply avoids wasting CPU time while waiting.

---

# Best Practices

- Identify I/O-heavy operations.
- Never block the CPU while waiting for external systems.
- Keep the CPU busy with other work whenever possible.
- Design backend applications assuming most work involves waiting.

---

# Interview Takeaways

Know

- CPU Idle Time
- I/O
- Blocking Operations
- Waiting Time
- Resource Utilization
- Why Backend Servers Need Concurrency

---

# Mental Model

```text
Request

↓

Needs Database

↓

Waiting

↓

CPU Works on Another Request

↓

Database Responds

↓

Resume Original Request
```

**Golden Rule**

> **Concurrency exists to keep the CPU productive while requests wait for slow I/O operations. It improves resource utilization, not the speed of external services.**


# LEC 17B - Concurrency & Parallelism (Part 2) - Concurrency vs Parallelism

# Two Commonly Confused Terms

Many developers think

**Concurrency**

and

**Parallelism**

mean the same thing.

They don't.

They solve different problems.

---

# What is Parallelism?

Parallelism means

> **Executing multiple tasks at exactly the same time.**

This requires

**multiple CPU cores.**

Example

```text
Core 1

↓

Request A
```

```text
Core 2

↓

Request B
```

Both requests execute simultaneously.

This is **true simultaneous execution**. :contentReference[oaicite:0]{index=0}

---

# What is Concurrency?

Concurrency means

> **Managing multiple tasks at the same time, even if only one task is executing at any instant.**

Example

```text
Single CPU

↓

Request A

↓

Waiting for Database

↓

CPU switches

↓

Request B

↓

Waiting

↓

CPU switches

↓

Request C
```

Only one request uses the CPU at any moment,

but multiple requests are making progress together. :contentReference[oaicite:1]{index=1}

---

# Key Difference

## Parallelism

```text
Core 1 → Request A

Core 2 → Request B
```

Both execute

**at the same instant.**

---

## Concurrency

```text
Single Core

↓

Request A

↓

Pause

↓

Request B

↓

Pause

↓

Request A

↓

Pause

↓

Request C
```

Tasks take turns using the CPU.

---

# Easy Analogy

## Parallelism

Imagine

two chefs.

```text
Chef 1 → Pasta

Chef 2 → Pizza
```

Both cook simultaneously.

---

## Concurrency

Imagine

one chef.

```text
Start Rice

↓

Rice Cooking

↓

Cut Vegetables

↓

Prepare Curry

↓

Serve Food

↓

Rice Ready
```

The chef switches between tasks while waiting.

Nothing is wasted.

---

# Why Concurrency Works on One Core

A single CPU core can execute

only **one instruction at a time**.

However,

during I/O waiting,

the CPU becomes free.

Instead of staying idle,

it switches to another task.

```text
CPU

↓

Request A

↓

Database Wait

↓

Request B

↓

API Wait

↓

Request C

↓

Resume A
```

This creates the illusion of doing many things at once. :contentReference[oaicite:2]{index=2}

---

# Timeline Example

Suppose

Request A

needs a database query.

```text
0ms ─────► 5ms

CPU Processing
```

Then

```text
5ms ─────► 40ms

Waiting for Database
```

Instead of wasting

35 ms,

the CPU starts Request B.

```text
Time

0-----5-----15------40------50

Req A : CPU ─ Waiting ─ CPU

Req B :      CPU ─ Waiting ─ CPU
```

The CPU remains busy almost the entire time. :contentReference[oaicite:3]{index=3}

---

# Parallel Timeline

If two CPU cores exist,

both requests execute simultaneously.

```text
Core 1

Request A

───────────────
```

```text
Core 2

Request B

───────────────
```

No switching is required.

---

# Simple Comparison

| Concurrency | Parallelism |
|-------------|-------------|
| Deals with many tasks | Executes many tasks |
| One core is enough | Requires multiple cores |
| Tasks take turns | Tasks run together |
| Great for I/O | Great for CPU work |
| Improves utilization | Improves computation speed |

---

# Important Observation

Concurrency

does **not**

make a single CPU faster.

Instead,

it ensures the CPU is rarely idle.

Parallelism

actually increases computing power

by using additional CPU cores.

---

# When Do We Use Them?

## Concurrency

Best for

- Web servers
- Database requests
- API calls
- File I/O
- Redis
- Network communication

---

## Parallelism

Best for

- Image processing
- Video encoding
- Machine Learning
- Compression
- Encryption
- Scientific computation

---

# Best Practices

- Use concurrency for I/O-heavy backend systems.
- Use parallelism for heavy computation.
- Understand that most web backends are dominated by waiting, not computing.
- Don't confuse "doing many things" with "executing many things simultaneously."

---

# Interview Takeaways

Know

- Concurrency
- Parallelism
- Single-core concurrency
- Multi-core execution
- CPU utilization
- Task scheduling

---

# Mental Model

```text
Concurrency

Task A

↓

Pause

↓

Task B

↓

Pause

↓

Task A


Parallelism

Core 1 → Task A

Core 2 → Task B
```

**Golden Rule**

> **Concurrency is about efficiently managing many tasks, while parallelism is about executing many tasks simultaneously. Concurrency improves resource utilization; parallelism improves computational throughput.**

# LEC 17C - Concurrency & Parallelism (Part 3) - I/O Bound vs CPU Bound

# Why This Distinction Matters

Not every backend task stresses the CPU.

Some tasks spend most of their time

**waiting**.

Others spend most of their time

**computing**.

Understanding which type of workload you have determines whether you should use **concurrency** or **parallelism**. :contentReference[oaicite:0]{index=0}

---

# I/O Bound Tasks

An **I/O-bound task** spends most of its time waiting for external resources.

The CPU has almost nothing to do while waiting.

```text
CPU

↓

Send Request

↓

Wait...

↓

Receive Response
```

The waiting time is much larger than the computation time.

---

# Examples of I/O Bound Tasks

Backend applications perform I/O constantly.

Examples

- Database queries
- Redis operations
- HTTP API calls
- Email services
- Reading files
- Writing files
- Logging
- Uploading files
- Downloading files
- Network communication

All of these involve waiting for another system to respond. :contentReference[oaicite:1]{index=1}

---

# CPU Bound Tasks

A **CPU-bound task** spends most of its time performing calculations.

Instead of waiting,

the CPU is continuously busy executing instructions.

```text
CPU

↓

Compute

↓

Compute

↓

Compute

↓

Result
```

---

# Examples of CPU Bound Tasks

Heavy computation workloads include

- Image processing
- Video encoding
- Encryption
- Compression
- Machine Learning inference
- Matrix multiplication
- Scientific computation

These tasks consume CPU cycles instead of waiting for I/O. :contentReference[oaicite:2]{index=2}

---

# Backend Reality

A typical backend request looks like this.

```text
Receive Request

↓

Validate Input

↓

Database Query

↓

Redis

↓

External API

↓

Response
```

Notice

Only validation uses the CPU.

Everything else waits for external systems.

Therefore,

**most backend applications are I/O-bound.** :contentReference[oaicite:3]{index=3}

---

# Why Concurrency Helps I/O

Suppose

Request A

is waiting for a database.

```text
Request A

↓

Database

↓

Waiting...
```

Instead of wasting CPU time,

the server starts Request B.

```text
CPU

↓

Request A

↓

Wait

↓

Request B

↓

Wait

↓

Request C
```

The CPU stays productive.

---

# Why Parallelism Helps CPU Work

Imagine resizing thousands of images.

```text
Image 1

↓

CPU
```

```text
Image 2

↓

CPU
```

These are pure computations.

Using multiple CPU cores lets several images be processed simultaneously.

```text
Core 1

↓

Image 1
```

```text
Core 2

↓

Image 2
```

This significantly reduces total processing time.

---

# Choosing the Right Tool

## I/O-bound Work

Use

- Concurrency
- Async programming
- Event loops
- Coroutines

Goal

```text
Keep CPU Busy
```

---

## CPU-bound Work

Use

- Parallelism
- Multiple threads
- Multiple CPU cores
- Worker processes

Goal

```text
Finish Computation Faster
```

---

# Common Misconception

Many developers think

> "More threads always make applications faster."

Not true.

For I/O-bound applications,

adding too many threads only increases

- Memory usage
- Context switching
- Scheduler overhead

The real bottleneck is waiting for external systems,

not CPU power. :contentReference[oaicite:4]{index=4}

---

# Real Backend Examples

| Operation | Type |
|-----------|------|
| SQL Query | I/O Bound |
| Redis GET | I/O Bound |
| HTTP API Call | I/O Bound |
| Reading File | I/O Bound |
| Uploading Image | I/O Bound |
| JWT Verification | CPU Bound |
| Password Hashing | CPU Bound |
| Image Compression | CPU Bound |
| Video Encoding | CPU Bound |
| AI Inference | CPU Bound |

---

# Mental Model

Imagine a restaurant.

### I/O Bound

```text
Waiter

↓

Take Order

↓

Kitchen Cooking

↓

Waiting
```

The waiter should serve another table while waiting.

This is **concurrency**.

---

### CPU Bound

```text
Chef

↓

Cooking

↓

Cooking

↓

Cooking
```

Cooking requires active work.

Adding another chef speeds things up.

This is **parallelism**.

---

# Best Practices

- Identify whether your workload is I/O-bound or CPU-bound.
- Use concurrency to hide waiting time.
- Use parallelism to speed up heavy computations.
- Don't optimize CPU usage when the real bottleneck is I/O.
- Most backend APIs benefit far more from good concurrency than additional CPU cores.

---

# Interview Takeaways

Know

- I/O Bound
- CPU Bound
- Blocking Operations
- Waiting vs Computing
- Concurrency for I/O
- Parallelism for CPU

---

# Mental Model

```text
I/O Bound

Work

↓

Wait

↓

Work

↓

Wait


CPU Bound

Work

↓

Work

↓

Work
```

**Golden Rule**

> **Most backend applications are I/O-bound, so concurrency keeps the CPU busy while waiting. Parallelism should be reserved for workloads that spend most of their time performing computations.**

# LEC 17D - Threads (Deep Dive)

# Why Do We Need Threads?

We already know that backend applications spend most of their time waiting for I/O.

If one request is waiting for the database, the CPU should execute another request instead of sitting idle.

The operating system provides **Threads** as one mechanism to achieve this concurrency. :contentReference[oaicite:0]{index=0}

---

# What is a Thread?

A **Thread** is the smallest unit of execution managed by the operating system.

Think of it as an independent execution path inside a process.

```text
Process

├── Thread 1
├── Thread 2
├── Thread 3
└── Thread 4
```

Each thread executes its own instructions while sharing the same application's resources.

---

# What Does Every Thread Have?

Every thread gets its own execution state.

## 1. Stack

Each thread has its own stack.

The stack stores

- Function calls
- Local variables
- Return addresses

Example

```cpp
void foo() {
    int x = 10;
    bar();
}
```

During execution

```text
Stack

bar()

↓

foo()

↓

main()
```

Every function call pushes a new stack frame.

When the function returns,

that frame is removed.

Each thread has **its own independent stack**, so threads do not interfere with each other's function calls. :contentReference[oaicite:1]{index=1}

---

## 2. Instruction Pointer

The OS must remember

> "Where was this thread executing?"

The **Instruction Pointer (Program Counter)** stores the address of the next instruction.

```text
Thread

↓

Instruction Pointer

↓

Current Line of Code
```

Without it,

the OS would not know where to resume execution after switching back to the thread. :contentReference[oaicite:2]{index=2}

---

# The OS Scheduler

Suppose there are 100 runnable threads.

Only a few CPU cores exist.

Who decides

- which thread runs?
- when it stops?
- when another thread starts?

The answer is

**The Operating System Scheduler.**

```text
Threads

↓

OS Scheduler

↓

CPU
```

The scheduler continuously chooses the next thread to execute. :contentReference[oaicite:3]{index=3}

---

# Time Slicing

The scheduler gives every thread a small CPU time window.

Example

```text
Thread A → 2 ms

↓

Thread B → 2 ms

↓

Thread C → 2 ms

↓

Thread D → 2 ms
```

After a short time,

the scheduler pauses one thread and switches to another.

This happens thousands of times every second.

To humans,

everything appears to run simultaneously.

In reality,

the CPU is rapidly switching between threads. :contentReference[oaicite:4]{index=4}

---

# Blocking I/O

Suppose Thread A executes

```cpp
SELECT * FROM users;
```

Database query

↓

Wait...

While waiting,

the CPU cannot help.

Instead of wasting CPU time,

the thread tells the OS

> "I'm blocked. Give the CPU to someone else."

The scheduler immediately switches to another runnable thread.

```text
Thread A

↓

Database Waiting

↓

Blocked

↓

Scheduler

↓

Thread B
```

Once the database responds,

Thread A becomes runnable again and waits for another CPU time slice. :contentReference[oaicite:5]{index=5}

---

# Thread States

A simplified lifecycle

```text
New

↓

Runnable

↓

Running

↓

Blocked (I/O)

↓

Runnable

↓

Running

↓

Finished
```

Important distinction

**Blocked ≠ Finished**

A blocked thread is simply waiting for an external event.

---

# Threads Inside Processes

```text
Process A

├── Thread 1
├── Thread 2

Process B

├── Thread 1
├── Thread 2
```

Threads inside

**different processes**

cannot directly access each other's memory.

This provides

- Security
- Isolation
- Stability

However,

threads inside the **same process** share memory. :contentReference[oaicite:6]{index=6}

---

# Shared Memory

Inside one process

```text
Heap

↓

Shared Object
```

Thread A

↓

Reads Object

Thread B

↓

Writes Object

Both threads can access the same memory location.

This makes communication extremely fast because nothing needs to be copied.

```text
Thread A

↓

Shared Heap

↑

Thread B
```

Instead of sending messages,

they simply read and write the same object. :contentReference[oaicite:7]{index=7}

---

# Why Shared Memory is Dangerous

Imagine

```cpp
counter++;
```

executed simultaneously by two threads.

Both may read

```
counter = 5
```

Both increment

```
6
```

Both write

```
6
```

Expected

```
7
```

Actual

```
6
```

This is called a **Race Condition**.

Because threads share memory,

developers need synchronization mechanisms such as

- Mutexes
- Locks
- Semaphores
- Atomic Operations

to prevent inconsistent data.

(The lecture mentions shared memory being powerful but dangerous; synchronization concepts build on this idea.) :contentReference[oaicite:8]{index=8}

---

# Threads and Parallelism

If the machine has multiple CPU cores

```text
Core 1

↓

Thread A
```

```text
Core 2

↓

Thread B
```

Both threads truly execute simultaneously.

Therefore,

threads provide both

- Concurrency
- Parallelism

when hardware allows it. :contentReference[oaicite:9]{index=9}

---

# Thread Overheads

Threads are powerful,

but they are expensive.

---

## 1. Memory Overhead

Every thread owns its own stack.

Thousands of threads

↓

Thousands of stacks

↓

Gigabytes of memory

```text
10,000 Threads

↓

10,000 Stacks

↓

Huge Memory Consumption
```

This is why creating one thread per request does not scale well. :contentReference[oaicite:10]{index=10}

---

## 2. Thread Creation Cost

Creating a thread is not free.

The operating system must

- Allocate stack
- Allocate metadata
- Create instruction pointer
- Register thread
- Add it to the scheduler

All this requires kernel work.

Even microseconds matter when creating thousands of threads every second. :contentReference[oaicite:11]{index=11}

---

## 3. Context Switching

When switching

```text
Thread A

↓

Thread B
```

the OS must

- Save CPU registers
- Save instruction pointer
- Save stack state
- Load Thread B registers
- Restore Thread B state

Only then can Thread B continue.

```text
CPU

↓

Save A

↓

Load B

↓

Resume B
```

No useful application work happens during this time.

This is pure scheduling overhead.

With hundreds or thousands of threads,

context switching alone can become a noticeable performance bottleneck. :contentReference[oaicite:12]{index=12}

---

# Why Thread-per-Request Doesn't Scale

Imagine

```text
20,000 HTTP Requests

↓

20,000 Threads
```

Problems

- Huge memory usage
- Heavy scheduler load
- Massive context switching
- Increased latency
- Poor scalability

This is why modern backend runtimes often avoid creating one OS thread for every request.

Instead,

they rely on lighter concurrency models like **event loops** or **lightweight threads (goroutines, virtual threads, etc.)**. :contentReference[oaicite:13]{index=13}

---

# Thread Workflow

```text
HTTP Request

↓

Thread

↓

CPU Processing

↓

Database Query

↓

Blocked

↓

OS Scheduler

↓

Another Thread

↓

Database Responds

↓

Runnable

↓

CPU

↓

Response
```

---

# Best Practices

- Use threads for true parallel computation.
- Avoid creating thousands of native threads.
- Minimize blocking operations inside threads.
- Be careful when sharing memory between threads.
- Reduce unnecessary context switching.

---

# Interview Takeaways

Know

- Thread
- Stack
- Instruction Pointer
- OS Scheduler
- Time Slice
- Blocking
- Runnable State
- Shared Memory
- Race Condition
- Context Switching
- Thread Creation Cost
- Memory Overhead
- Why Thread-per-request doesn't scale

---

# Mental Model

Think of a restaurant.

```text
Chef (CPU)

↓

Worker A cooks

↓

Waiting for Oven

↓

Chef assigns Worker B

↓

Worker A returns

↓

Continues Cooking
```

The chef (CPU) never sits idle while someone waits.

---

# Golden Rules

> **Threads allow multiple execution paths inside one process by sharing memory and letting the OS scheduler switch between them. They enable concurrency and parallelism but become expensive at very high scale because of memory usage, creation cost, and context-switch overhead.** 


# LEC 17E - Event Loop (Deep Dive)

# Why Do We Need an Event Loop?

Threads solve the waiting problem,

but they introduce their own problems:

- Memory overhead
- Context switching
- Expensive thread creation

Modern backend runtimes like **Node.js** ask:

> **"Can we handle thousands of I/O requests without creating thousands of OS threads?"**

The answer is the **Event Loop**.

Instead of creating one thread per request,

a small number of threads (often just one main thread) coordinate many I/O operations efficiently.

---

# Core Idea

Instead of blocking while waiting,

the program

1. Starts an I/O operation.
2. Registers what should happen when it finishes.
3. Continues working on other requests.
4. Returns later when the I/O completes.

```text
Request A

↓

Start DB Query

↓

Don't Wait

↓

Process Request B

↓

Process Request C

↓

DB Response Arrives

↓

Resume Request A
```

Notice

**The CPU never wastes time waiting.**

---

# Traditional Thread Model

```text
Thread

↓

Database Query

↓

WAIT...

↓

Continue
```

The thread cannot do anything until the database responds.

---

# Event Loop Model

```text
Request

↓

Start Database Query

↓

Register Callback

↓

Continue Processing Other Requests

↓

Database Responds

↓

Callback Added to Queue

↓

Event Loop Executes Callback
```

No thread is blocked waiting.

---

# What is an Event Loop?

An **Event Loop** is a program that continuously checks for completed work and executes it.

Simplified loop

```text
while(true){

    Check Completed Events

    Execute Ready Tasks

    Repeat
}
```

It keeps running for the lifetime of the server.

---

# Non-Blocking I/O

Suppose

```js
const user = await db.findUser();
```

Internally

this does **not** mean

```text
CPU

↓

Wait...
```

Instead

```text
CPU

↓

Send DB Request

↓

Continue Other Work

↓

DB Responds

↓

Resume await
```

The waiting happens outside the CPU.

---

# Callback Registration

Imagine

```text
Database Query

↓

"When finished,

call this function."
```

The runtime stores that callback.

Later

```text
Database Finished

↓

Callback Queue

↓

Event Loop

↓

Execute Callback
```

The callback is **not executed immediately**.

It waits until the Event Loop reaches it.

---

# Queues

Completed work is placed inside a queue.

```text
Completed Events

↓

Queue

↓

Event Loop

↓

Execute
```

Example

```text
Email Finished

↓

Queue

File Read Finished

↓

Queue

Database Finished

↓

Queue
```

The Event Loop processes them one by one.

---

# Async/Await

Developers write

```js
const user = await db.find();
```

It looks synchronous.

Internally

the runtime performs

```text
Start I/O

↓

Suspend Function

↓

Return Event Loop

↓

I/O Completes

↓

Resume Function
```

So **async/await is mostly syntax**.

Underneath,

the Event Loop still manages execution.

---

# What Happens During await?

Example

```js
async function getUser(){

    const user = await db.find();

    console.log(user);
}
```

Execution

```text
Start Function

↓

Send DB Query

↓

Pause Function

↓

Return Control

↓

Event Loop Continues

↓

Database Responds

↓

Resume Function

↓

console.log(user)
```

The function pauses,

not the entire server.

---

# Why Event Loops Scale Well

Suppose

100,000 requests

are waiting for databases.

Thread Model

```text
100,000 Threads
```

Event Loop

```text
1 Event Loop

+

OS handles waiting

+

Callbacks
```

Huge memory savings.

Almost zero context-switch overhead.

---

# Event Loop Doesn't Do Everything

Important

The Event Loop **does not perform database work itself.**

It simply

- starts the request
- waits for notification
- resumes execution later

The database,

network card,

or operating system performs the waiting.

---

# epoll / kqueue (High-Level Idea)

The operating system provides mechanisms like

- **epoll** (Linux)
- **kqueue** (BSD/macOS)

They efficiently notify the Event Loop when I/O is ready.

Instead of repeatedly asking

```text
Finished?

Finished?

Finished?
```

the OS tells the Event Loop

```text
Database Finished

↓

Resume Request
```

This avoids wasting CPU cycles checking repeatedly.

(For interviews, knowing that epoll/kqueue notify the event loop when I/O is ready is usually sufficient.)

---

# Never Block the Event Loop

The Event Loop works well because every task finishes quickly.

Bad

```js
while(true){}
```

or

```js
Huge Image Processing
```

Now

```text
Event Loop Busy

↓

Cannot Process Other Requests

↓

Entire Server Slows
```

One CPU-heavy task blocks everyone.

---

# CPU-bound Work

Event Loops excel at

- Database queries
- HTTP requests
- Redis
- Files
- Network sockets

They are **not ideal** for

- AI inference
- Image compression
- Video encoding
- Heavy encryption

Those tasks should run in

- Worker Threads
- Separate Processes
- Multiple CPU Cores

---

# Thread Model vs Event Loop

| Thread Model | Event Loop |
|--------------|------------|
| One thread may block | Never blocks on I/O |
| Higher memory usage | Very low memory usage |
| Context switching | Minimal switching |
| Easier CPU parallelism | Excellent for I/O |
| Expensive at huge scale | Handles massive concurrent connections |

---

# Backend Request Lifecycle

```text
Client

↓

Backend

↓

Start Database Query

↓

Return To Event Loop

↓

Process Another Request

↓

Database Responds

↓

Queue Callback

↓

Event Loop Executes Callback

↓

Send Response
```

---

# Production Best Practices

- Never block the Event Loop with CPU-heavy work.
- Keep callbacks small.
- Use async/await for readable code.
- Move heavy computation to worker threads/processes.
- Use Event Loops primarily for I/O-heavy servers.

---

# Interview Takeaways

Know

- Event Loop
- Callback
- Queue
- Non-blocking I/O
- async/await
- epoll
- kqueue
- Callback Queue
- Resume Execution
- Why Node.js scales well
- Why CPU-heavy work blocks the Event Loop

---

# Mental Model

Think of a receptionist.

```text
Customer Arrives

↓

Submit Form

↓

Receptionist Doesn't Wait

↓

Helps Next Customer

↓

Form Completed

↓

Receptionist Calls Customer Back
```

The receptionist never sits idle waiting for paperwork.

---

# Golden Rule

> **An Event Loop achieves concurrency by never waiting for I/O. It starts the operation, lets the operating system handle the waiting, and resumes execution only when the work is complete. This makes it extremely efficient for I/O-bound backend applications.**


# LEC 17F - Thread Model vs Event Loop Model

# Two Different Approaches

Modern backend servers mainly use two approaches to achieve concurrency.

## 1. Thread-based Model

One (or a pool of) OS threads execute requests.

```text
Request

↓

Thread

↓

CPU

↓

Database

↓

Thread Waits

↓

Database Responds

↓

Continue
```

Examples

- Java
- Spring Boot
- C#
- Older web servers
- C/C++ servers using pthreads

---

## 2. Event Loop Model

Instead of dedicating one thread to one request,

the runtime keeps a small number of threads alive.

When one request starts waiting,

the Event Loop immediately begins another.

```text
Request A

↓

DB Query

↓

Continue Request B

↓

Continue Request C

↓

DB Responds

↓

Resume A
```

Examples

- Node.js
- Deno
- Nginx
- Redis

---

# Thread-Based Request Lifecycle

Suppose

```text
Client

↓

Login API
```

Execution

```text
Receive Request

↓

Assign Thread

↓

Validate Input

↓

Database Query

↓

WAIT

↓

Database Returns

↓

Generate Response

↓

Send Response
```

During the database wait,

that thread cannot execute another request.

---

# Event Loop Request Lifecycle

```text
Receive Request

↓

Validate Input

↓

Start Database Query

↓

Register Callback

↓

Return To Event Loop

↓

Handle Other Requests

↓

Database Finishes

↓

Callback Queue

↓

Resume Request

↓

Send Response
```

Notice

The request pauses,

not the server.

---

# Visual Comparison

## Thread Model

```text
Thread 1

↓

Request A

↓

Waiting

❌ Cannot help others
```

## Event Loop

```text
Request A Waiting

↓

CPU Free

↓

Request B

↓

Request C

↓

Request D
```

The CPU keeps working.

---

# Which Uses More Memory?

## Thread Model

Each thread owns

- Stack
- Registers
- Metadata

Thousands of requests

↓

Thousands of threads

↓

Large memory usage

---

## Event Loop

One main execution thread

+

Callback queue

↓

Much lower memory usage

---

# Context Switching

## Thread Model

```text
CPU

↓

Thread A

↓

Save Registers

↓

Load Thread B

↓

Continue
```

Thousands of switches

↓

CPU overhead

---

## Event Loop

Normally

```text
Current Task

↓

Next Callback

↓

Next Callback
```

Almost no expensive OS context switching.

---

# CPU Utilization

Thread Model

```text
Waiting Thread

↓

CPU May Become Idle
```

Event Loop

```text
Waiting Request

↓

CPU Handles Another Request
```

Much better utilization for I/O-heavy systems.

---

# But Event Loops Have a Weakness

Suppose

```js
for(let i=0;i<10000000000;i++){}
```

The Event Loop cannot process

- HTTP Requests
- Database Results
- Timers
- Callbacks

Everything waits.

Entire server becomes slow.

---

# Worker Threads

Heavy computation should be moved elsewhere.

```text
Client

↓

Event Loop

↓

Worker Thread

↓

Heavy Image Processing

↓

Return Result

↓

Send Response
```

The Event Loop stays responsive.

---

# Database Calls

A common misunderstanding

The Event Loop does **NOT**

execute SQL itself.

Instead

```text
Event Loop

↓

Start SQL Query

↓

Operating System

↓

Database

↓

Result Ready

↓

Notify Event Loop

↓

Resume Function
```

The waiting happens outside the Event Loop.

---

# Which Model Should You Use?

## Thread Model

Best when

- Heavy computation
- CPU intensive work
- Parallel execution required

Examples

- Video encoding
- Scientific simulations
- Machine learning
- Image processing

---

## Event Loop

Best when

- REST APIs
- CRUD services
- Authentication
- Chat servers
- Proxy servers
- Database-driven applications
- WebSockets

These workloads spend most of their time waiting.

---

# Why Node.js Became Popular

Most web servers spend

- 5–10 ms computing
- 100–300 ms waiting

Instead of creating thousands of threads,

Node.js simply switches to another request.

Result

- Lower memory
- Higher scalability
- More simultaneous connections

---

# Complete Backend Flow

```text
Client

↓

Express Route

↓

Validation

↓

Database Query

↓

Event Loop Handles Others

↓

Database Ready

↓

Resume

↓

JSON Response
```

---

# Thread Model vs Event Loop

| Feature | Thread Model | Event Loop |
|---------|--------------|------------|
| Concurrency | ✔ | ✔ |
| Parallelism | ✔ | Limited (needs workers) |
| Memory Usage | High | Low |
| Context Switching | High | Very Low |
| I/O Performance | Good | Excellent |
| CPU Work | Excellent | Poor (without workers) |
| Scalability | Moderate | Very High |

---

# If I Build a MERN Backend?

Node.js uses

```text
JavaScript

↓

Event Loop

↓

libuv

↓

Operating System

↓

Database
```

When MongoDB responds,

the Event Loop resumes your JavaScript function.

This is why Node.js is an excellent choice for

- Express APIs
- CRUD apps
- Authentication services
- Real-time chat
- Dashboards

---

# Production Advice

- Use Event Loops for I/O-heavy applications.
- Never perform long CPU calculations on the Event Loop.
- Use Worker Threads or separate services for CPU-heavy work.
- Understand your bottleneck before choosing a concurrency model.

---

# Interview Takeaways

Know

- Thread-per-request
- Event Loop
- Callback Queue
- Worker Threads
- Blocking vs Non-blocking
- Why Node.js scales
- Why Event Loops are bad for CPU-heavy work
- Thread Model vs Event Loop

---

# Final Mental Model

Imagine a restaurant.

## Thread Model

```text
One waiter

↓

One table

↓

Waits for kitchen

↓

Cannot serve others
```

## Event Loop

```text
Take Order

↓

Kitchen Cooks

↓

Serve Other Tables

↓

Kitchen Ready

↓

Deliver Food
```

The waiter never wastes time waiting.

---

# Golden Rules

> **Threads dedicate execution resources to requests, while Event Loops dedicate execution time. Threads excel at computation; Event Loops excel at waiting. Choose the model based on whether your workload is CPU-bound or I/O-bound.**


# LEC 17G - Goroutines (Go's Concurrency Model)

# Why Did Go Introduce Goroutines?

Native OS threads are powerful,

but they have drawbacks:

- Large memory usage
- Expensive creation
- Context switching overhead
- Poor scalability with hundreds of thousands of tasks

Go solves this by introducing **Goroutines**.

A **Goroutine** is a **lightweight thread** managed by the Go runtime instead of the operating system.

---

# OS Threads vs Goroutines

## Native Thread

```text
Application

↓

Operating System

↓

Thread

↓

CPU
```

The OS manages every thread.

---

## Goroutine

```text
Application

↓

Go Runtime

↓

Many Goroutines

↓

Few OS Threads

↓

CPU
```

The Go runtime schedules goroutines onto a small number of OS threads.

This makes goroutines much cheaper than native threads.

---

# Lightweight Threads

Creating a native thread requires the OS to

- Allocate memory
- Allocate stack
- Register thread
- Schedule thread

Creating a goroutine only requires the Go runtime.

Result

- Much faster creation
- Very little memory
- Can create hundreds of thousands (or even millions) of goroutines

---

# M:N Scheduling

One of Go's biggest ideas is **M:N Scheduling**.

Instead of

```text
1 Thread

↓

1 Task
```

Go uses

```text
Many Goroutines

↓

Few OS Threads

↓

CPU
```

Example

```text
100,000 Goroutines

↓

8 OS Threads

↓

8 CPU Cores
```

The runtime decides which goroutine runs next.

The operating system only schedules a small number of threads.

---

# Go Scheduler

Instead of relying entirely on the OS,

Go has its own scheduler.

It decides

- Which goroutine runs
- Which one waits
- Which one resumes
- Which OS thread should execute it

```text
Goroutines

↓

Go Scheduler

↓

OS Threads

↓

CPU
```

This reduces expensive OS scheduling.

---

# Blocking I/O

Suppose one goroutine performs

```text
Database Query

↓

Waiting...
```

The Go runtime simply pauses that goroutine

and runs another one.

```text
Goroutine A

↓

Waiting

↓

Scheduler

↓

Goroutine B

↓

CPU
```

The CPU remains busy.

---

# Channels

Threads usually communicate through

**shared memory**.

Shared memory can cause

- Race Conditions
- Locks
- Deadlocks
- Complex synchronization

Go encourages a different approach.

Instead of

> Share memory to communicate

Go says

> **Communicate instead of sharing memory.**

It uses **Channels**.

---

# What is a Channel?

A Channel is a safe communication pipe between goroutines.

```text
Goroutine A

↓

Channel

↓

Goroutine B
```

Instead of both goroutines modifying the same object,

they send data through the channel.

This reduces synchronization bugs.

---

# Why Channels?

Without Channels

```text
Thread A

↓

Shared Variable

↑

Thread B
```

Need

- Mutex
- Lock
- Semaphore

With Channels

```text
Worker

↓

Channel

↓

Consumer
```

No shared variable is required.

Communication becomes easier to reason about.

---

# Advantages of Goroutines

- Extremely lightweight
- Very low memory usage
- Fast creation
- Built-in scheduler
- Easy concurrency
- Channels simplify communication
- Excellent scalability

---

# When Are Goroutines Useful?

Ideal for

- HTTP servers
- API servers
- Background jobs
- Worker pools
- Distributed systems
- Concurrent services
- Microservices

---

# LEC 17H - Choosing the Right Concurrency Model

# Which Model Should You Choose?

There is **no universal best model**.

Choose based on your workload.

---

## Threads

Choose when

- Heavy CPU computation
- Need true parallel execution
- Limited number of long-running tasks

Examples

- Video encoding
- AI inference
- Image processing

---

## Event Loop

Choose when

- Mostly waiting for I/O
- REST APIs
- CRUD applications
- Authentication
- WebSockets
- Chat servers
- Database-driven systems

This is why Node.js is popular for backend development.

---

## Goroutines

Choose when

- Building highly concurrent servers
- Thousands of background tasks
- Network services
- High-performance APIs
- Cloud-native applications

Go combines

- Lightweight execution
- Easy concurrency
- Excellent scalability

---

# Backend Reality

Most backend requests spend

```text
Receive Request

↓

Validate

↓

Database

↓

Redis

↓

External API

↓

Response
```

Notice

Almost all the time is spent **waiting**.

That is why

**Concurrency is usually more important than Parallelism** in backend engineering.

---

# Production Best Practices

- Identify whether your workload is I/O-bound or CPU-bound.
- Never block an Event Loop with CPU-heavy work.
- Don't create one OS thread per request.
- Use thread pools instead of unlimited threads.
- Use Worker Threads or separate services for CPU-intensive tasks.
- Use async programming for I/O-heavy applications.
- Keep critical sections protected when sharing memory.
- Measure performance before optimizing.
- Choose the simplest concurrency model that solves your problem.

---

# Interview Revision (Must Know)

## Concurrency

Managing multiple tasks efficiently by making progress on each, even if only one runs at a time.

---

## Parallelism

Executing multiple tasks simultaneously using multiple CPU cores.

---

## Thread

Smallest unit of execution managed by the OS.

---

## Process

Independent program containing one or more threads.

---

## Event Loop

A loop that keeps processing completed I/O events without blocking on waiting.

---

## Blocking

The current execution cannot continue until an operation finishes.

---

## Non-Blocking

Start the operation and continue doing other work while waiting.

---

## I/O-bound

Most time spent waiting.

Examples

- SQL
- Redis
- HTTP APIs
- Files

---

## CPU-bound

Most time spent computing.

Examples

- Encryption
- Compression
- Image Processing
- AI

---

## Goroutine

Lightweight thread managed by the Go runtime.

---

## Channel

Safe communication mechanism between goroutines.

---

# Complete Mental Model

```text
Client
        │
        ▼
Backend Server
        │
        ▼
Need Database?
        │
        ├──────────────┐
        ▼              │
Start Query            │
        │              │
        ▼              │
Don't Wait             │
        │              │
        ▼              │
Handle Other Requests  │
        │              │
        ▼              │
Database Responds ◄────┘
        │
        ▼
Resume Execution
        │
        ▼
Send Response
```

---

# Final Golden Rules

> **Most backend systems are I/O-bound, not CPU-bound.**

> **Concurrency keeps the CPU busy while requests wait; Parallelism speeds up heavy computation.**

> **Threads provide concurrency but become expensive at scale due to memory and context-switching costs.**

> **Event Loops achieve massive scalability by avoiding blocking and efficiently handling I/O.**

> **Goroutines combine lightweight execution with an efficient runtime scheduler, making highly concurrent systems easier to build.**

---
