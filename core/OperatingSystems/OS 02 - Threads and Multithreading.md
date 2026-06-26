# OS 02 - Threads and Multithreading

## Navigation

### Part 1 - Thread Fundamentals
- [1. Why Were Threads Introduced?](#1-why-were-threads-introduced)
- [2. What Is A Thread?](#2-what-is-a-thread)
- [3. Thread Components](#3-thread-components)
  - [Program Counter](#program-counter)
  - [Registers](#registers)
  - [Stack](#stack)
  - [Thread State](#thread-state)
- [4. Thread Control Block (TCB)](#4-thread-control-block-tcb)
- [5. What Is Shared Between Threads?](#5-what-is-shared-between-threads)

### Part 2 - Multithreading and Thread Models
- [6. What Is Multithreading?](#6-what-is-multithreading)
- [Concurrency vs Parallelism](#concurrency-vs-parallelism)
- [7. Thread Lifecycle](#7-thread-lifecycle)
- [8. User-Level Threads (ULT)](#8-user-level-threads-ult)
- [9. Kernel-Level Threads (KLT)](#9-kernel-level-threads-klt)
- [10. Thread Models](#10-thread-models)
  - [Many-To-One](#many-to-one)
  - [One-To-One](#one-to-one)
  - [Many-To-Many](#many-to-many)
- [11. Thread Context Switching](#11-thread-context-switching)
- [12. Benefits of Threads](#12-benefits-of-threads)
- [13. Problems With Threads](#13-problems-with-threads)
- [14. Common Interview Questions](#14-common-interview-questions)

### Part 3 - Advanced Concepts and Interview FAQs
- [15. Thread Creation](#15-thread-creation)
- [16. Thread Termination](#16-thread-termination)
- [17. What Happens If Main Thread Exits?](#17-what-happens-if-main-thread-exits)
- [18. Can One Thread Crash Entire Process?](#18-can-one-thread-crash-entire-process)
- [19. Multicore Execution](#19-multicore-execution)
- [20. Thread Scheduling Basics](#20-thread-scheduling-basics)
- [21. Thread Pool](#21-thread-pool)
- [22. Hyperthreading](#22-hyperthreading)
- [23. Common Thread Misconceptions](#23-common-thread-misconceptions)
- [Quick Revision](#quick-revision)
- [Final Interview Summary](#final-interview-summary)


# Part 1 Thread Fundamentals

---

# 1. Why Were Threads Introduced?

To understand threads, first understand the problem.

Suppose we have:

```text
Chrome Browser
```

Chrome must:

```text
Render UI
Download Images
Play Audio
Run JavaScript
Handle Mouse Clicks
Handle Keyboard Input
```

---

## Solution 1: Multiple Processes

We could create:

```text
Chrome UI Process
Chrome Download Process
Chrome Audio Process
Chrome JS Process
```

Each process has:

```text
Code
Data
Heap
Stack
PCB
```

---

## Problem

Every process has its own:

```text
Address Space
Heap
Globals
Resources
```

Therefore:

```text
High Memory Usage
Expensive Creation
Expensive Context Switching
Expensive Communication
```

---

## Example

Suppose:

```text
Process A downloads image
```

Now:

```text
Process B wants image
```

Since memory is separate:

```text
Process B cannot directly access
Process A's memory
```

OS must provide:

```text
Pipes
Sockets
Shared Memory
Message Passing
```

Communication becomes expensive.

---

# Solution 2: Threads

Instead of:

```text
Many Processes
```

Use:

```text
One Process
Many Threads
```

Example:

```text
Chrome Process
│
├── Thread 1 → UI
├── Thread 2 → Download
├── Thread 3 → Audio
└── Thread 4 → JavaScript
```

Now all threads share:

```text
Code
Data
Heap
Files
```

Result:

```text
Less Memory
Faster Creation
Faster Communication
Faster Switching
```

---

# Main Idea

A process provides:

```text
Resources
```

A thread provides:

```text
Execution
```

Remember:

```text
Process = Resource Container

Thread = Execution Unit
```

---

# 2. What Is A Thread?

Definition:

```text
A thread is the smallest unit of CPU execution
inside a process.
```

---

## Simple Meaning

Suppose process contains:

```text
Code
```

Thread tells:

```text
Which instruction to execute next
```

and

```text
Where execution currently is
```

---

## Thread Analogy

Think:

```text
Process = House
```

Contains:

```text
Kitchen
TV
Bedroom
WiFi
```

Now:

```text
Family Members
```

use those resources.

Family Members are:

```text
Threads
```

Shared:

```text
Kitchen
TV
WiFi
```

Private:

```text
Phone
Wallet
Thoughts
```

Similarly:

Shared:

```text
Code
Heap
Data
Files
```

Private:

```text
PC
Registers
Stack
```

---

# 3. Thread Components

Every thread needs its own execution state.

Therefore every thread owns:

```text
Program Counter
Registers
Stack
Thread State
```

---

# Program Counter

Stores:

```text
Address of next instruction
```

Example:

```c
void task()
{
    int a=10;
    int b=20;
    int c=a+b;
}
```

Suppose thread completed:

```text
a=10
b=20
```

Then:

```text
PC points to:

c=a+b
```

---

# Why Separate PC?

Suppose:

```text
Thread 1 → executing line 50

Thread 2 → executing line 120

Thread 3 → executing line 300
```

Each thread needs:

```text
Own PC
```

Otherwise OS cannot know where each thread should continue.

---

# Registers

Every thread gets:

```text
AX
BX
CX
DX
SP
etc.
```

because each thread may be performing different calculations.

---

# Example

Thread 1:

```text
5 + 10
```

Thread 2:

```text
500 + 900
```

Their intermediate results differ.

Therefore:

```text
Separate Registers
```

are needed.

---

# Stack

Every thread gets its own stack.

Stores:

```text
Function Calls
Parameters
Local Variables
Return Addresses
```

---

# Example

Thread 1:

```c
download();
```

Thread 2:

```c
playAudio();
```

Stack of Thread 1:

```text
download()
```

Stack of Thread 2:

```text
playAudio()
```

They must remain separate.

---

# Why Separate Stack?

Suppose:

```c
void func()
{
    int x;
}
```

If all threads shared one stack:

```text
Thread 1 writes x

Thread 2 writes x

Thread 3 writes x
```

Chaos.

Therefore:

```text
Each Thread
      ↓
Own Stack
```

---

# Thread State

Like processes:

```text
Ready
Running
Waiting
Terminated
```

Each thread can have its own state.

---

# 4. Thread Control Block (TCB)

Thread equivalent of PCB.

Stores:

```text
Thread ID
Thread State
Program Counter
Registers
Stack Pointer
Scheduling Info
```

---

# Relationship

Process:

```text
PCB
```

Thread:

```text
TCB
```

---

# Example

```text
Process
│
├── PCB
│
├── Thread 1
│    └── TCB
│
├── Thread 2
│    └── TCB
│
└── Thread 3
     └── TCB
```

---

# 5. What Is Shared Between Threads?

Most important interview question.

All threads share:

```text
Code Segment
Data Segment
Heap
Global Variables
Open Files
Sockets
Process Resources
```

---

# Why Share Code?

All threads belong to same process.

No need for duplicate copies.

Example:

```text
Chrome.exe
```

One code copy.

All threads execute it.

---

# Why Share Heap?

Suppose:

```c
int *p = malloc(100);
```

allocated by:

```text
Thread 1
```

Now:

```text
Thread 2
```

can use same memory.

Easy communication.

---

# Why Share Globals?

Example:

```c
int count = 0;
```

Thread 1:

```c
count++;
```

Thread 2:

```c
count++;
```

Both access same variable.

---

# Why Share Files?

Suppose:

```text
File Opened By Thread 1
```

Thread 2 should also use it.

Therefore file table belongs to process.

Not thread.

---

# Quick Summary

Shared:

```text
Code
Data
Heap
Globals
Files
Sockets
```

Private:

```text
PC
Registers
Stack
Thread State
```

---

# Interview Question

What is the biggest advantage of threads?

Answer:

```text
Resource sharing with independent execution.
```


# Part 2 Multithreading and Thread Models

---

# 6. What is Multithreading?

Definition:

```text
Multiple threads executing within the same process.
```

---

## Single Threaded Process

Example:

```text
Process
│
└── Thread 1
```

Only one execution path exists.

Example:

```c
readFile();
processData();
displayResult();
```

Execution:

```text
readFile
   ↓
processData
   ↓
displayResult
```

Everything happens sequentially.

---

## Problem

Suppose:

```text
readFile() takes 5 seconds
```

Entire application waits.

User sees:

```text
Application Not Responding
```

---

## Multithreaded Process

```text
Process
│
├── Thread 1
├── Thread 2
└── Thread 3
```

Example:

```text
Thread 1 → Read File
Thread 2 → UI
Thread 3 → Background Tasks
```

Now:

```text
UI remains responsive
```

while file loading continues.

---

# Real Example: Chrome

Chrome process may have:

```text
Thread 1 → Render UI
Thread 2 → Download Images
Thread 3 → JavaScript Engine
Thread 4 → Audio
Thread 5 → Network
```

All working simultaneously.

---

# Real Example: WhatsApp

```text
Thread 1 → Receive Messages
Thread 2 → UI
Thread 3 → Notifications
Thread 4 → Media Download
```

---

# Real Example: Game

```text
Thread 1 → Rendering
Thread 2 → Physics
Thread 3 → Sound
Thread 4 → Input Handling
```

---

# Important

Multithreading does NOT mean:

```text
Every thread runs at the exact same instant
```

on a single-core CPU.

Instead:

```text
CPU rapidly switches between threads
```

creating an illusion of parallel execution.

---

# Single Core Example

```text
Thread A
Thread B
Thread C
```

Execution:

```text
A
B
C
A
B
A
C
```

Very fast switching.

Appears simultaneous.

---

# Multi-Core Example

Suppose:

```text
4 CPU Cores
```

Then:

```text
Core 1 → Thread A
Core 2 → Thread B
Core 3 → Thread C
Core 4 → Thread D
```

Actual parallel execution occurs.

---

# Concurrency vs Parallelism

Very common interview question.

---

## Concurrency

```text
Many tasks in progress
```

Example:

```text
Thread A
Thread B
```

Single CPU:

```text
A
B
A
B
A
B
```

Tasks make progress together.

---

## Parallelism

```text
Many tasks executing simultaneously
```

Example:

```text
Core 1 → Thread A
Core 2 → Thread B
```

Same instant.

---

## Easy Memory Trick

```text
Concurrency
=
Taking turns

Parallelism
=
Working together
```

## 1. Quick Summary
This is one of the most common tech interview questions. The difference comes down to management versus execution:
* **Concurrency:** Dealing with many tasks at once (managing them).
* **Parallelism:** Doing many tasks at once (executing them).

---

## 2. Concurrency: Taking Turns

### The Kitchen Analogy
Imagine you are a chef working **alone** in a kitchen. You need to make soup and bake bread. 
1. You chop an onion for the soup.
2. You pause the soup to knead the bread dough.
3. You put the bread in the oven, then go back to stirring the soup.

You are not doing both at the exact same fraction of a second. You are switching back and forth so fast that both tasks make progress together.

### How a Computer Sees It
A computer with only a **single CPU core** uses concurrency to run multiple programs.

```text
Single CPU Core:
[ Thread A ] -> [ Thread B ] -> [ Thread A ] -> [ Thread B ]
```
The CPU switches between Thread A and Thread B in milliseconds. It creates the illusion that both apps are running simultaneously.

---

## 3. Parallelism: Working Together

### The Kitchen Analogy
Now, imagine you hire a **second chef** to help you.
* Chef 1 stirs the soup.
* Chef 2 kneads the bread.

Both tasks are happening at the **exact same instant** in time. 

### How a Computer Sees It
A computer with **multiple CPU cores** (like a modern Core i7 or M1 chip) can achieve true parallelism.

```text
Core 1 ──> [ Thread A ] (Happening at the same instant)
Core 2 ──> [ Thread B ] (Happening at the same instant)
```

---

## 4. Why Is This So Important?

### 1. Hardware Limits
Computer chips hit a physical limit around 2004—they got too hot to make a single core run any faster. Instead of making one worker faster, chip makers added more cores. Software must use parallelism to take advantage of modern hardware.

### 2. Cost and Scale
Concurrency allows a single server to handle thousands of users at once. If a server had to give every single user their own dedicated CPU core, running websites like Netflix or Amazon would be impossibly expensive.

### 3. Smooth Apps
If an app freezes while downloading a file, it lacks proper concurrency. Good design uses one thread to keep the buttons clickable (UI) and another thread to download data in the background.

---


# Architectural Decision: Concurrency vs. Parallelism

As a software engineer, your first job when tackling a performance problem is to analyze the nature of the task and choose the right approach. If you use the wrong model, your code will either run incredibly slowly or waste massive amounts of expensive hardware resources.

Here is the straightforward framework to help you decide which one to use based on the problem type.

---

## 1. The "I/O Bound" Problem → Choose Concurrency

### The Problem
Your code spends most of its time **waiting** for something outside the CPU to finish.

### Examples
* Downloading 100 images from the internet.
* Fetching data from a database.
* Reading a massive text file from a hard drive.

### Why Concurrency Fits
The CPU is mostly idle during these tasks. If you use Concurrency, while Thread 1 is waiting for Image A to download, the CPU instantly switches to Thread 2 to start downloading Image B. You do not need multiple expensive CPU cores for this; one core can manage thousands of waiting tasks perfectly.

---

## 2. The "CPU Bound" Problem → Choose Parallelism

### The Problem
Your code spends all of its time doing heavy math, calculations, and processing inside the chip. The CPU is running at 100% utilization.

### Examples
* Editing or rendering a 4K video.
* Training a Machine Learning model.
* Compressing a massive ZIP file.

### Why Parallelism Fits
Taking turns on a single CPU core will not make math finish any faster. You need raw muscle. You must split the data into chunks and feed them into Core 1, Core 2, and Core 3 at the exact same instant to cut the processing time down.


---

# 7. Thread Lifecycle

A thread also goes through states.

---

## New

Thread created.

Example:

```c
pthread_create(...)
```

State:

```text
NEW
```

---

## Ready

Thread prepared to run.

Waiting for CPU.

```text
READY
```

---

## Running

CPU currently executing thread.

```text
RUNNING
```

---

## Waiting

Thread waiting for:

```text
I/O
Lock
Semaphore
Network
User Input
```

State:

```text
WAITING
```

---

## Terminated

Thread finished execution.

```text
TERMINATED
```

---

# Lifecycle Diagram

```text
NEW
 ↓
READY
 ↓
RUNNING
 ↙     ↘
WAIT    READY
 ↘     ↙
 TERMINATED
```

---
# 8. User-Level Threads (ULT)

Important interview topic.

---

# What Are User-Level Threads?

User-Level Threads are threads managed completely in user space.

The Operating System kernel does NOT know these threads exist.

Instead, a thread library manages them.

Example:

```text
Java Green Threads (old)
Some User Thread Libraries
```

---

# Internal Working

Suppose we create:

```text
Thread 1
Thread 2
Thread 3
Thread 4
```

inside a process.

To us:

```text
Process
│
├── Thread 1
├── Thread 2
├── Thread 3
└── Thread 4
```

But the OS sees:

```text
One Process
```

only.

Kernel does NOT know:

```text
Thread 1
Thread 2
Thread 3
Thread 4
```

exist.

---

# Who Schedules Threads?

Not the OS.

A user-space library decides:

```text
Run Thread 1
Pause Thread 1

Run Thread 2
Pause Thread 2
```

All management happens without entering kernel mode.

---

# Why Is Creation Fast?

Suppose a new thread is created.

User thread library only creates:

```text
TCB
Stack
Registers
PC
```

inside user space.

Kernel is never involved.

No system call.

No kernel data structures.

Therefore:

```text
Very Fast Creation
```

---

# Why Is Switching Fast?

Switching between:

```text
Thread 1
Thread 2
```

is handled by the library.

No OS call.

No kernel mode switch.

Only:

```text
Save Registers
Save PC
Load Registers
Load PC
```

inside user memory.

Therefore:

```text
Very Fast Switching
```

---

# Why Low Overhead?

Because:

```text
Kernel does nothing
```

Most work happens inside application memory.

Less CPU overhead.

Less OS involvement.

---

# Biggest Problem

Kernel does not know individual threads exist.

This causes blocking issues.

---

# Example

Suppose:

```text
Thread 1
```

performs:

```c
read(file);
```

Disk access required.

Thread enters waiting state.

---

# What Kernel Sees

Kernel sees:

```text
One Process
```

waiting.

It does NOT know:

```text
Thread 2
Thread 3
Thread 4
```

are still ready.

Therefore kernel blocks:

```text
Entire Process
```

---

# Result

Even though:

```text
Thread 2 Ready
Thread 3 Ready
Thread 4 Ready
```

they cannot run.

Bad utilization.

---

# Another Problem

Multi-core CPUs.

Suppose:

```text
8 CPU Cores
```

Kernel sees:

```text
One Process
```

only.

Therefore:

```text
One Core Used
```

Other cores may remain unused.

True parallelism is difficult.

---

# Summary

Advantages:

```text
Fast Creation
Fast Switching
Low Overhead
Simple Implementation
```

Because:

```text
No Kernel Involvement
```

---

Disadvantages:

```text
One Blocking Thread Can Block Entire Process

Poor Multi-Core Utilization

Kernel Cannot Schedule Threads Individually
```

Because:

```text
Kernel Does Not Know Threads Exist
```

---

# Interview One-Liner

User-Level Threads are managed entirely by user-space libraries. They are very fast because the kernel is not involved, but a blocking operation can stop the entire process since the kernel cannot see individual threads.

---

# 9. Kernel-Level Threads (KLT)

Modern operating systems use this approach.

Linux:

```text
Uses Kernel-Level Threads
```

Windows:

```text
Uses Kernel-Level Threads
```

---

# What Are Kernel-Level Threads?

Kernel knows every thread individually.

Example:

```text
Process
│
├── Thread 1
├── Thread 2
├── Thread 3
└── Thread 4
```

Kernel also sees:

```text
Thread 1
Thread 2
Thread 3
Thread 4
```

individually.

---

# Internal Working

Kernel maintains:

```text
TCB 1
TCB 2
TCB 3
TCB 4
```

for every thread.

Scheduler can choose:

```text
Thread 1
or
Thread 2
or
Thread 3
or
Thread 4
```

directly.

---

# Why Is Creation Slower?

Creating a thread now requires kernel involvement.

OS must:

```text
Create Kernel TCB
Allocate Kernel Structures
Update Scheduler Data
```

System calls required.

Kernel mode entered.

Therefore:

```text
Creation Cost Higher
```

---

# What Is Creation Cost?

Simply:

```text
Extra Work Done By OS
```

More work:

```text
More CPU Time
```

Therefore:

```text
Higher Cost
```

---

# Why Is Switching Slower?

Switching:

```text
Thread 1
→
Thread 2
```

requires:

```text
Kernel Scheduler
Kernel Data Structures
Kernel Mode Execution
```

More work than user-space switching.

Therefore:

```text
Switching Cost Higher
```

---

# What Is Switching Cost?

Time spent:

```text
Saving Context
Loading Context
Updating Scheduler Structures
```

before actual execution starts.

---

# Biggest Advantage

Blocking issue solved.

---

# Example

Thread 1:

```c
read(file);
```

Waiting for disk.

Kernel sees:

```text
Thread 1 Waiting
```

But kernel also sees:

```text
Thread 2 Ready
Thread 3 Ready
Thread 4 Ready
```

Therefore:

```text
Run Thread 2
```

No problem.

---

# Better CPU Utilization

Suppose:

```text
4 CPU Cores
```

Kernel can schedule:

```text
Core 1 → Thread 1
Core 2 → Thread 2
Core 3 → Thread 3
Core 4 → Thread 4
```

True parallel execution.

---

# Summary

Advantages:

```text
Blocking Thread Doesn't Stop Others

True Multi-Core Execution

Kernel Can Schedule Threads Individually

Better CPU Utilization
```

Because:

```text
Kernel Knows Every Thread
```

---

Disadvantages:

```text
Thread Creation Slower

Thread Switching Slower

More Kernel Overhead
```

Because:

```text
Kernel Involvement Required
```

---

# Easy Interview Table

| Feature            | User-Level Threads    | Kernel-Level Threads    |
| ------------------ | --------------------- | ----------------------- |
| Managed By         | User Library          | OS Kernel               |
| Kernel Aware?      | No                    | Yes                     |
| Creation Speed     | Fast                  | Slower                  |
| Switching Speed    | Fast                  | Slower                  |
| Blocking Call      | Blocks Entire Process | Only Blocks That Thread |
| Multi-Core Support | Poor                  | Excellent               |
| Modern OS Usage    | Rare                  | Common                  |

---

# Ultimate Memory Trick

```text
User Threads
=
Fast
But Kernel Is Blind

Kernel Threads
=
Slightly Slower
But Kernel Is Smart
```


# 10. Thread Models (The Foundation)

Interview favorite.

---

## 🚀 The Core Concept (What is this basically?)

An application cannot talk directly to the hardware CPU. Only the Operating System (Kernel) can do that. Therefore, your application's tasks cannot run unless the OS gives them a real OS highway to drive on.

*   **User Threads:** Tasks created by your application code. The CPU hardware has no idea they exist.
*   **Kernel Threads:** Physical execution vehicles managed by the Operating System. They run directly on the physical CPU.

---

## 🔄 How Execution Works (Step-by-Step)

For your code to actually run, the OS follows this process:

```text
1. [User Thread] Created by your application code.
         ↓
2. [OS Kernel] Checks the user thread and creates a matching Kernel Thread.
         ↓
3. [Loading] The User Thread code is loaded into that Kernel Thread vehicle.
         ↓
4. [Scheduling] The OS scheduler assigns the Kernel Thread directly to a CPU Core.
         ↓
5. [Execution] The CPU hardware executes the task.
```

---

## 🚦 The Three Thread Models

The **Thread Model** is simply the rulebook that decides: *"How do we load these User Threads into the Kernel Thread vehicles so they can reach the CPU?"*

### 1. Many-To-One
*   **Concept:** Shoving all your application tasks into just *one* OS vehicle.
*   **Problem:** If one task pauses to wait for data (like reading a file), the OS stops the entire vehicle. Everything freezes.

### 2. One-To-One (Most Common: Linux & Windows)
*   **Concept:** Giving *every single* application task its own private OS vehicle.
*   **Advantage:** True parallelism. If one task blocks, the others keep speeding down their own CPU core highways.

### 3. Many-To-Many
*   **Concept:** Sharing a small fleet of OS vehicles among a large crowd of application tasks.
*   **Advantage:** You can create thousands of application threads without overloading the operating system.




## 10. Thread Models

Interview favorite.

---

## The Core Concept (Before You Start)
*   **User Threads:** Tasks created by your application code. The OS cannot see them.
*   **Kernel Threads:** Tasks managed by the Operating System. They run directly on the physical CPU.
*   **The Goal:** Thread models define how your invisible application tasks map onto the real OS tasks.

---

# Many-To-One

```text
Many User Threads (App)
        ↓  (Mapped to)
One Kernel Thread (OS)
```

---

## Diagram

```text
User Threads (App level)
│
├── T1 ──┐
├── T2 ──┼─→ [ Single Kernel Thread ] ─→ CPU Core
├── T3 ──┤
└── T4 ──┘
```

---

## Problem: The Bottleneck

One blocking thread blocks everything:
*   The OS only sees **one** single execution path.
*   If User Thread `T1` pauses to wait for data (like reading a file), the OS pauses the entire Kernel Thread.
*   Result: `T2`, `T3`, and `T4` instantly freeze, even if they are ready to run.

---

# One-To-One

Most common model used today.

```text
One User Thread
        ↓  (Maps to)
One Kernel Thread
```

---

## Diagram

```text
T1 (User) ──→ KT1 (Kernel) ──→ CPU Core 1
T2 (User) ──→ KT2 (Kernel) ──→ CPU Core 2
T3 (User) ──→ KT3 (Kernel) ──→ CPU Core 3
```

Linux & Windows:
```text
Use this model natively
```

---

## Advantage

True parallelism:
*   Every single application task gets its own dedicated highway to the CPU.
*   If `T1` blocks or crashes, `T2` and `T3` keep running on other CPU cores without pausing.

---

# Many-To-Many

```text
Many User Threads
        ↓  (Multiplexed across)
Many Kernel Threads
```

Combination approach. Less common today.

---

## Diagram

```text
User Threads      OS Kernel Pool      Hardware
 ├── T1 ──┐        ┌── KT1 ──┐      ┌── CPU 1
 ├── T2 ──┼───────→├── KT2 ──┼─────→├── CPU 2
 ├── T3 ──┼───────→└── KT3 ──┘      └── CPU 3
 └── T4 ──┘
```

## Why it exists

*   **Best of both worlds:** You can create 10,000 application threads without crashing your OS, because it automatically schedules them across a fixed, smaller pool of real OS threads.

---

## 🍽️ The Restaurant Analogy

*   **User Threads = Customer Orders:** A table orders 4 dishes (4 User Threads).
*   **Kernel Threads = Kitchen Chefs:** The restaurant has 2 chefs (2 Kernel Threads).
*   **Thread Model = Management:** The strategy the manager uses to assign those 4 orders to the 2 chefs so the food gets cooked efficiently.


# 11. Thread Context Switching

You learned:

```text
Process Context Switching
```

in OS 01.

Now thread switching.

---

# Example

```text
Thread A Running
```

Timer expires.

OS saves:

```text
PC
Registers
SP
```

into:

```text
TCB_A
```

---

Then loads:

```text
PC
Registers
SP
```

from:

```text
TCB_B
```

CPU continues:

```text
Thread B
```

---

# Why Faster Than Process Switch?

Because:

```text
Heap Shared
Code Shared
Files Shared
```

No address space change required.

Only execution state changes.

---

# Process Switch

Must change:

```text
Page Tables
Memory Context
Execution Context
```

More expensive.

---

# Thread Switch

Must change:

```text
PC
Registers
Stack
```

Less expensive.

---

## ⚠️ The Context Switching Trap (Interview Pro-Tip)

Threads are faster than processes because threads of the *same* process share memory, keeping the CPU Cache "hot." However:

*   **Intra-Process Switch (Thread A1 → Thread A2):** Fast. Memory maps stay the same. CPU Cache stays valid.
*   **Inter-Process Switch (Thread A5 → Thread B1):** Slow. The OS must swap the entire memory space. The CPU Cache is invalidated (Cache Misses) and must reload from RAM.

**Conclusion:** If the OS scheduler frequently jumps between threads of *different* processes, the performance degrades and behaves exactly like the heavy process-switching of old operating systems.

---

### 🛡️ How the OS Fixes This: Locality Awareness

Operating systems prevent this cache disaster using a smart scheduling strategy:

1.  **OS Schedulers are "Locality Aware":** The OS scheduler does not pick threads completely at random. 
2.  **Batching Threads:** If it sees that a single core is currently running a thread from Process A, it will aggressively try to execute all of Process A's threads back-to-back before finally switching to Process B. 
3.  **Keeping Caches Hot:** This intentional scheduling keeps the CPU cache filled with relevant data for as long as possible, avoiding costly trips to the main RAM.



# 12. Benefits of Threads

---

## Responsiveness

Example:

```text
Download continues
UI remains active
```

---

## Resource Sharing

Threads share:

```text
Heap
Globals
Files
```

Easy communication.

---

## Lower Memory Usage

One process.

Many threads.

No duplicate resources.

---

## Faster Creation

Creating thread:

```text
Cheaper
```

than process.

---

## Faster Context Switching

Less state to save.

---

## Better CPU Utilization

Idle thread doesn't stop others.

---

# 13. Problems With Threads

Threads are powerful.

But dangerous.

---

# Race Condition

Example:

```c
count++;
```

Thread 1:

```text
count = 5
```

Thread 2:

```text
count = 5
```

Both update.

Expected:

```text
7
```

Actual:

```text
6
```

Possible.

---

# Why?

Because both access shared memory.

---

# Deadlock

Thread A waits:

```text
Resource 1
```

Thread B waits:

```text
Resource 2
```

Neither proceeds.

---

# Starvation

Some thread never gets CPU.

---

# Synchronization Needed

Therefore we need:

```text
Mutex
Semaphore
Monitor
```

These are covered in:

```text
OS 04 - Synchronization
```

---

# 14. Common Interview Questions

---

## Process vs Thread

Process:

```text
Heavyweight
Own Address Space
Expensive
```

Thread:

```text
Lightweight
Shared Address Space
Cheap
```

---

## What Is Shared?

```text
Code
Data
Heap
Files
Sockets
```

---

## What Is Private?

```text
PC
Registers
Stack
Thread State
```

---

## Why Are Threads Faster?

```text
Shared Resources
Less Context To Switch
```

---

## Why Separate Stack?

```text
Each Thread Has Separate
Function Calls
Local Variables
Return Addresses
```

---

## Why Separate PC?

```text
Each Thread Executes
Different Instructions
```

---

## Why Separate Registers?

```text
Each Thread Maintains
Its Own Calculations
```

---

# Final Intuition

Think:

```text
Process = Resource Container
```

Contains:

```text
Code
Data
Heap
Files
```

Thread:

```text
Execution Unit
```

Contains:

```text
PC
Registers
Stack
```

A process can exist with:

```text
One Thread
```

or

```text
Many Threads
```

Threads share resources but execute independently.

This sharing gives:

```text
Speed
Efficiency
Low Memory Usage
```

but introduces:

```text
Race Conditions
Synchronization Problems
Deadlocks
```

which are solved in the next chapter:

```text
OS 04 - Process Synchronization
```


# Part 3 Advanced Concepts and Interview FAQs

Add this after Part 2.

---

# 15. Thread Creation

A process does NOT start with many threads.

Initially:

```text
Process Created
      ↓
Main Thread Created
```

Example:

```text
Chrome Process
      │
      └── Main Thread
```

---

# Why Main Thread?

Execution must start somewhere.

When program begins:

```c
int main()
{
}
```

the OS creates:

```text
Main Thread
```

and starts executing:

```text
main()
```

---

# Creating Additional Threads

Main thread may create more threads.

Example:

```text
Main Thread
│
├── Thread A
├── Thread B
└── Thread C
```

---

# Real Example

Chrome starts:

```text
Main Thread
```

Then creates:

```text
Render Thread
Network Thread
Audio Thread
JavaScript Thread
```

---

# Internal Working

Process:

```text
PCB
Code
Heap
Globals
```

already exists.

Creating a thread usually means:

```text
Create TCB
Create Stack
Assign Thread ID
Initialize Registers
Initialize PC
```

Much cheaper than creating a process.

---

# Why Thread Creation Is Cheap?

Because OS does NOT create:

```text
New Address Space
New Heap
New Code Segment
```

Those already exist.

Thread simply reuses them.

---

# Process Creation vs Thread Creation

Process Creation:

```text
PCB
Address Space
Heap
Stack
Resources
```

Large cost.

---

Thread Creation:

```text
TCB
Stack
Registers
PC
```

Small cost.

---

# 16. Thread Termination

A thread may finish execution.

Example:

```text
Thread A Finished
```

OS:

```text
Destroys TCB
Releases Stack
Removes Scheduling Info
```

---

# Important

Terminating one thread does NOT necessarily terminate other threads.

Example:

```text
Process
│
├── Thread A (Finished)
├── Thread B (Running)
└── Thread C (Running)
```

Process continues.

---

# When Does Entire Process Die?

Process terminates when:

```text
Main Program Ends
```

or

```text
Process Explicitly Terminated
```

Then:

```text
All Threads Die
```

---

# 17. What Happens If Main Thread Exits?

Interview favorite.

---

Example:

```text
Process
│
├── Main Thread
├── Thread A
└── Thread B
```

Suppose:

```text
Main Thread Ends
```

---

In most operating systems:

```text
Process Terminates
```

Therefore:

```text
Thread A Dies
Thread B Dies
```

too.

---

# Intuition

Remember:

```text
Threads belong to Process
```

No process:

```text
No threads
```

---

# 18. Can One Thread Crash Entire Process?

YES.

Very important.

---

Example:

```c
int *p = NULL;
*p = 10;
```

inside:

```text
Thread B
```

---

Result:

```text
Segmentation Fault
```

Process may crash.

---

Why?

Because threads share:

```text
Address Space
Heap
Globals
```

One bad thread can corrupt process memory.

---

# Example

```text
Thread A → Good

Thread B → Corrupts Heap

Thread C → Crashes Later
```

Even though Thread C did nothing wrong.

---

# Interview Answer

Can one thread crash entire process?

```text
Yes.

Because threads share the same address space and resources.
Memory corruption by one thread can affect all other threads.
```

---

# 19. Multicore Execution

Important modern concept.

---

# Single Core CPU

Suppose:

```text
Thread A
Thread B
Thread C
```

Only one executes at a time.

Example:

```text
A
B
C
A
B
C
```

Rapid switching.

---

# Multi-Core CPU

Suppose:

```text
4 CPU Cores
```

Now:

```text
Core 1 → Thread A
Core 2 → Thread B
Core 3 → Thread C
Core 4 → Thread D
```

Actual simultaneous execution.

---

# Why Multithreading Became Popular

Modern CPUs:

```text
2 Cores
4 Cores
8 Cores
16 Cores
```

Applications use threads to utilize all cores.

---

# Example

Video Rendering:

```text
Frame Chunk 1 → Thread A
Frame Chunk 2 → Thread B
Frame Chunk 3 → Thread C
Frame Chunk 4 → Thread D
```

Much faster.

---

# 20. Thread Scheduling Basics

Detailed scheduling comes in OS 03.

But understand this.

---

Suppose:

```text
Thread A
Thread B
Thread C
```

all are:

```text
READY
```

---

Scheduler decides:

```text
Who gets CPU
```

---

Possible execution:

```text
A
B
A
C
B
A
```

---

Important

Programmer CANNOT assume:

```text
A before B
```

unless synchronization is used.

---

# Example

Wrong Assumption

```text
Thread A sets value
Thread B uses value
```

Scheduler may run:

```text
Thread B first
```

Problem.

---

This directly leads to:

```text
Race Conditions
Synchronization
Mutex
Semaphore
```

covered later.

---

# 21. Thread Pool

Common interview question.

---

# Problem

Suppose web server receives:

```text
10000 Requests
```

Creating:

```text
10000 Threads
```

is expensive.

---

# Solution

Create fixed threads:

```text
Thread 1
Thread 2
Thread 3
Thread 4
```

and reuse them.

---

This group is called:

```text
Thread Pool
```

---

# Benefits

```text
Lower Creation Cost
Better Performance
Controlled Resource Usage
```

---

# Example

Java:

```text
ExecutorService
```

uses thread pools.

---

# 22. Hyperthreading

Interview favorite for hardware/OS questions.

---

## 💡 What is it basically?

Hyperthreading is a hardware trick where a single physical CPU core duplicates its architectural state so it can pretend to be **two virtual (logical) cores** to the Operating System.

```text
[ 1 Physical CPU Core ]
       │
       ├─→ Logical Core 1 (Thread A)
       └─→ Logical Core 2 (Thread B)
```

---

## 🛠️ Why is it necessary?

* **The Problem:** CPU cores are much faster than system RAM. When a thread requests data from RAM, the CPU core sits idle waiting for it, wasting processing time.
* **The Solution:** With hyperthreading, the moment Thread A pauses to wait for RAM, the physical core instantly switches to execute Thread B. This keeps the CPU hardware fully utilized.

---

## 🍽️ The Chef Analogy

* **Physical Core:** A single Chef.
* **Logical Cores:** Two different cutting boards in front of that Chef.
* **How it works:** The chef can only chop on one board at a time. But if Board 1 is waiting for a pot to boil, the chef instantly switches to Board 2. 

---

## ⚠️ Interview Warning

```text
Hyperthreading ≠ Real Additional Cores
```

* Hyperthreading does **not** give you two physical execution engines. 
* A 4-core CPU with hyperthreading (8 logical cores) will never perform as fast as a true, physical 8-core CPU. It simply eliminates wasted idle time on the 4 cores you do have.


# 23. Common Thread Misconceptions

---

# Misconception 1

```text
Every Thread Runs Simultaneously
```

False.

Single-core CPU:

```text
Threads take turns.
```

---

# Misconception 2

```text
Threads Have Separate Heap
```

False.

Heap belongs to process.

Shared by threads.

---

# Misconception 3

```text
Threads Have Separate Code
```

False.

Code segment is shared.

---

# Misconception 4

```text
Thread Switch = Process Switch
```

False.

Process switch is heavier.

---

# Misconception 5

```text
OS Understands Thread Dependencies
```

False.

OS schedules runnable threads.

Programmer must enforce ordering.

---

# Quick Revision

Thread Private:

```text
PC
Registers
Stack
Thread State
TCB
```

---

Thread Shared:

```text
Code
Data
Heap
Globals
Files
Sockets
```

---

Thread Creation:

```text
Cheap
```

because:

```text
Resources Reused
```

---

Thread Context Switch:

```text
Save Thread A Context
Load Thread B Context
```

---

Thread Advantages:

```text
Responsiveness
Fast Switching
Low Memory Usage
Resource Sharing
Parallelism
```

---

Thread Problems:

```text
Race Conditions
Deadlocks
Synchronization Issues
Memory Corruption
```

---

# Final Interview Summary

Process:

```text
Resource Container
```

Thread:

```text
Execution Unit
```

Process owns:

```text
Code
Data
Heap
Files
```

Thread owns:

```text
PC
Registers
Stack
```

Threads are lightweight because they share process resources while maintaining independent execution contexts.

# How Does A Program Actually Execute?

One of the biggest misconceptions is that the OS automatically creates multiple execution threads for every program.

It does **not**.

---

## Program Startup

When you run a program:

```text
Executable File
        ↓
OS Creates Process
        ↓
OS Creates One Main Thread
        ↓
Main Thread Starts At main()
```

Initially every normal process has:

```text
1 Process

1 Main Thread

1 Program Counter (PC)

1 Stack
```

---

## How Does The Program Execute?

Suppose:

```cpp
int main()
{
    int x = 5;
    int y = x + 2;
    cout << y;
}
```

Execution is simply:

```text
main()
   ↓
Line 1
   ↓
Line 2
   ↓
Line 3
   ↓
Program Ends
```

Only **one thread** executes these instructions.

There is **no parallel execution**.

---

## When Do Multiple Threads Exist?

Only if:

* The programmer creates them (`std::thread`, `pthread_create`)
* A library creates them
* A runtime/framework creates them (JVM, Browser, Game Engine, Thread Pool)

Otherwise:

```text
One Process
        ↓
One Main Thread
        ↓
Sequential Execution
```

---

## Do Threads Start In The Middle Of My Code?

**No.**

Threads never randomly begin executing at line 50 or line 100.

Each thread starts from its assigned **entry function**.

Example:

```cpp
void worker()
{
    ...
}

int main()
{
    std::thread t(worker);
}
```

Execution becomes:

```text
Main Thread
↓

main()

---------------------

Worker Thread
↓

worker()
```

Each thread has its own execution flow.

---

## Why Can Multiple Threads Execute The Same Function?

Example:

```cpp
void increment()
{
    counter++;
}

thread t1(increment);
thread t2(increment);
```

Execution:

```text
Thread 1
↓

increment()

------------------

Thread 2
↓

increment()
```

Both begin from the first line of `increment()`.

Each has its own:

```text
Program Counter

Registers

Stack
```

They only share:

```text
Heap

Global Variables

Code (.text Section)
```

---

# Interview Answer

```text
Every process starts with one main thread created by the OS.

The main thread begins execution at main().

Additional threads exist only if the program, a library, or the runtime explicitly creates them.
```

---

# Memory Trick

```text
Executable
        ↓
Process
        ↓
Main Thread
        ↓
main()
        ↓
Sequential Execution

Additional Threads?
↓

Only If Explicitly Created
```

```text
Application (User Space)
-------------------------
Main Thread
Worker Thread
Worker Thread

=========================

Kernel (OS)
-------------------------
Kernel Thread
Kernel Thread
Kernel Thread
```

