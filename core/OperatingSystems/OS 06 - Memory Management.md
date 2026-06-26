# OS 06 - Memory Management

## Navigation

- [Part 1 - Memory Management Fundamentals](#part-1---memory-management-fundamentals)
  - [1. Why Memory Management?](#1-why-memory-management)
  - [2. Address Binding](#2-address-binding)
  - [3. Logical vs Physical Address](#3-logical-vs-physical-address)
  - [4. Memory Management Unit (MMU)](#4-memory-management-unit-mmu)
  - [5. Dynamic Loading](#5-dynamic-loading)
  - [6. Dynamic Linking](#6-dynamic-linking)
  - [7. Swapping](#7-swapping)
  - [8. Interview FAQs](#8-interview-faqs)

- [Part 2 - Contiguous Memory Allocation](#part-2---contiguous-memory-allocation)
  - [1. Contiguous Allocation](#1-contiguous-allocation)
  - [2. Fixed Partitioning](#2-fixed-partitioning)
  - [3. Variable Partitioning](#3-variable-partitioning)
  - [4. First Fit](#4-first-fit)
  - [5. Best Fit](#5-best-fit)
  - [6. Worst Fit](#6-worst-fit)
  - [7. Internal Fragmentation](#7-internal-fragmentation)
  - [8. External Fragmentation](#8-external-fragmentation)
  - [9. Compaction](#9-compaction)
  - [10. Interview FAQs](#10-interview-faqs)

- [Part 3 - Interview Questions & Revision](#part-3---interview-questions--revision)

# 1. Why Memory Management?

Memory is one of the most important resources managed by an Operating System.

Every running program requires memory to store:

```text
Instructions

Variables

Functions

Stack

Heap
```

Without memory,

no program can execute.

---

## The Problem

Suppose the computer has:

```text
8 GB RAM
```

Now three programs start:

```text
Chrome

VS Code

Spotify
```

Questions immediately arise:

```text
Who Gets Memory?

How Much Memory?

Where Is It Stored?

Can One Program Access Another's Memory?

What Happens When A Program Ends?
```

The Operating System answers all of these questions.

This responsibility is called:

```text
Memory Management
```

---

## Goals Of Memory Management

The Operating System tries to:

```text
Allocate Memory

Protect Memory

Share Memory

Free Memory

Reuse Memory

Utilize RAM Efficiently
```

---

## Responsibilities

Whenever a program starts:

```text
Program Starts
        ↓
Requests Memory
        ↓
OS Allocates Memory
        ↓
Program Executes
        ↓
Program Ends
        ↓
Memory Released
```

Meanwhile,

the OS continuously keeps track of:

```text
Which Memory Is Free

Which Memory Is Allocated

Which Process Owns Which Memory
```

---

## Why Is Memory Management Necessary?

Imagine there is no Operating System.

Program A could write into:

```text
Program B's Variables

Passwords

Browser Data

Operating System Memory
```

Result:

```text
Data Corruption

Security Problems

Program Crashes

System Crash
```

Therefore,

every process receives its own protected memory space.

---

## Example

Suppose RAM contains:

```text
---------------------------------

Chrome

---------------------------------

VS Code

---------------------------------

Spotify

---------------------------------
```

Chrome should never access:

```text
VS Code's Memory
```

Similarly,

Spotify should never modify:

```text
Chrome's Memory
```

The Operating System enforces this protection.

---

## Memory Sharing

Sometimes processes intentionally communicate.

Instead of copying large amounts of data,

the OS may provide:

```text
Shared Memory
```

Both processes access the same memory safely.

This is much faster than repeatedly copying data.

---

## Efficient RAM Usage

RAM is limited.

Example:

```text
RAM = 8 GB
```

Processes request:

```text
Chrome
4 GB

VS Code
2 GB

Game
6 GB
```

Total:

```text
12 GB
```

Impossible.

The OS decides:

```text
Which Process Gets Memory

Which Must Wait

Which Can Be Swapped Out
```

Memory Management is therefore about using RAM as efficiently as possible.

---

# 2. Address Binding

Whenever a program is written,

it contains many variables.

Example:

```cpp
int x = 10;
```

The compiler knows that

a variable named

```text
x
```

exists,

but it usually does **not** know where in RAM

that variable will finally be stored.

This process of assigning addresses to instructions and variables is called:

```text
Address Binding
```

---

## Why Can't The Compiler Decide The Address?

Suppose you compile:

```cpp
int main()
{
    int x;
}
```

Today your program may start at:

```text
1000
```

Tomorrow,

because other programs are already using memory,

it may start at:

```text
8500
```

The compiler cannot predict

where the program will eventually be loaded.

Therefore,

addresses are bound later.

---

## Three Types Of Address Binding

### 1. Compile-Time Binding

Memory location is known during compilation.

Example:

```text
Program Always Starts

At Address 1000
```

Compiler directly generates:

```text
Physical Addresses
```

Very fast,

but almost never used in modern operating systems.

---

### 2. Load-Time Binding

Compiler generates:

```text
Relocatable Addresses
```

When the program is loaded,

the loader decides:

```text
Actual Memory Location
```

Example:

```text
Program

↓

Loaded At 5000
```

Tomorrow it might load at:

```text
8000
```

No recompilation is needed.

---

### 3. Execution-Time Binding

Modern operating systems mostly use this approach.

The program executes using:

```text
Logical Addresses
```

The hardware continuously converts them into:

```text
Physical Addresses
```

during execution.

This allows:

```text
Programs To Move

During Execution

(Virtual Memory)
```

---

## Memory Trick

```text
Compile Time

↓

Before Program Runs

-----------------------

Load Time

↓

When Program Loads

-----------------------

Execution Time

↓

While Program Is Running
```

---

# 3. Logical Address vs Physical Address

This is one of the most important interview questions.

Students often confuse these two.

---

## Logical Address

Also called:

```text
Virtual Address
```

It is the address generated by:

```text
CPU
```

Example:

```text
Logical Address

250
```

The program believes

its variable exists at address:

```text
250
```

But this is **not** the real RAM address.

---

## Physical Address

Physical Address is the

actual location inside RAM.

Example:

```text
Logical

250

↓

MMU

↓

Physical

7250
```

Only the hardware knows

the real address.

Programs never directly access physical memory.

---

## Why Use Logical Addresses?

Suppose two programs are running.

Both believe:

```text
Variable x

Exists At

Address 100
```

Can both be correct?

Yes.

Because:

```text
Logical Address

100
```

may become:

```text
Process A

↓

Physical 5100
```

while

```text
Process B

↓

Physical 14200
```

Both processes think

they own address:

```text
100
```

The Operating System maps them

to different RAM locations.

This provides:

```text
Protection

Isolation

Flexibility
```
# 4. Memory Management Unit (MMU)

The **Memory Management Unit (MMU)** is a hardware component present inside the CPU.

Its job is to convert:

```text
Logical Address

↓

Physical Address
```

Every memory access made by a running program passes through the MMU.

---

## Why Is MMU Needed?

Programs generate:

```text
Logical Addresses
```

RAM understands only:

```text
Physical Addresses
```

The MMU performs the translation automatically.

Without the MMU,

programs could not safely execute using virtual addresses.

---

## Example

Suppose the CPU generates:

```text
Logical Address

250
```

The MMU converts it to:

```text
Physical Address

8250
```

The program still believes it accessed address:

```text
250
```

but the RAM actually accesses:

```text
8250
```

---

## Benefits Of MMU

```text
Memory Protection

Address Translation

Process Isolation

Supports Virtual Memory
```

---

## Memory Flow

```text
CPU

↓

Logical Address

↓

MMU

↓

Physical Address

↓

RAM
```

---

# 5. Dynamic Loading

Normally,

an entire program is loaded into memory before execution.

Sometimes,

large parts of the program may never execute.

Loading everything wastes RAM.

---

## Idea

Load a function

only when it is actually needed.

This is called:

```text
Dynamic Loading
```

---

## Example

Suppose a calculator program contains:

```text
Addition

Subtraction

Multiplication

Scientific Functions
```

A user only performs addition.

There is no need to load:

```text
Scientific Functions
```

They are loaded only when required.

---

## Advantages

```text
Saves RAM

Reduces Startup Memory

Useful For Large Programs
```

---

## Disadvantage

```text
Slight Delay

When Loading
```

for the first time.

---

# 6. Dynamic Linking

Loading and Linking are different.

Loading:

```text
Load Code
Into Memory
```

Linking:

```text
Connect Program

To Required Libraries
```

---

## Static Linking

Library code becomes part of the executable.

Example:

```text
Executable

+

printf()

+

Other Library Code
```

Large executable.

---

## Dynamic Linking

Executable stores only a reference.

Actual library loads at runtime.

Example:

```text
Executable

↓

Shared Library (.dll/.so)
```

Many programs share

the same library.

---

## Example

Suppose:

```text
Chrome

VS Code

Spotify
```

all use:

```text
printf()
```

With static linking:

```text
Three Copies
```

exist.

With dynamic linking:

```text
One Shared Copy
```

exists.

RAM usage decreases.

---

## Advantages

```text
Smaller Executables

Less RAM Usage

Easy Library Updates

Library Shared By Many Programs
```

---

## Memory Trick

```text
Dynamic Loading

↓

Load Code Later

--------------------

Dynamic Linking

↓

Link Libraries Later
```

---

# 7. Swapping

RAM is limited.

Sometimes,

there is not enough memory

to keep every process in RAM.

The OS temporarily moves

inactive processes

to disk.

This is called:

```text
Swapping
```

---

## Process

```text
RAM Full

↓

Move Process

To Disk

↓

Free RAM

↓

Run Another Process
```

Later,

when required,

the process is brought back into RAM.

---

## Swap In

```text
Disk

↓

RAM
```

---

## Swap Out

```text
RAM

↓

Disk
```

---

## Example

Suppose:

```text
Chrome

VS Code

Game
```

RAM becomes full.

You minimize:

```text
Game
```

The OS may move it

to disk.

When you reopen the game,

it is loaded back into RAM.

---

## Advantage

```text
Allows More Processes

Than RAM Can Hold
```

---

## Disadvantage

Disk is much slower than RAM.

Frequent swapping reduces performance.

---

# Dynamic Loading vs Dynamic Linking

```text
Dynamic Loading

↓

Loads Functions

Only When Needed

----------------------

Dynamic Linking

↓

Loads Libraries

At Runtime
```

---

# Loading vs Swapping

```text
Loading

↓

Program

Disk → RAM

First Time

----------------------

Swapping

↓

Running Process

RAM ↔ Disk

Many Times
```

---

# Interview FAQs

### What Is Address Binding?

```text
Assigning Memory Addresses

To Program Instructions

And Variables.
```

---

### Which Address Binding Is Used Today?

```text
Execution-Time Binding
```

---

### Difference Between Logical And Physical Address?

```text
Logical

Generated By CPU

---------------------

Physical

Actual RAM Address
```

---

### What Does MMU Do?

```text
Converts

Logical Address

To

Physical Address.
```

---

### Dynamic Loading?

```text
Load Code

Only When Needed.
```

---

### Dynamic Linking?

```text
Load Libraries

At Runtime.
```

---

### Swapping?

```text
Move Process

Between

RAM And Disk.
```

---

# Part 1 Revision

```text
Memory Management

↓

Address Binding

↓

Logical Address

↓

MMU

↓

Physical Address

↓

Dynamic Loading

↓

Dynamic Linking

↓

Swapping
```
