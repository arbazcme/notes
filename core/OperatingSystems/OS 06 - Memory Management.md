# OS 06 - Memory Management

## Navigation

### Part 1 - Address Binding & Logical Memory

- [Why Memory Management Exists](#part-1---why-memory-management-exists)
- [How Does A Program Actually Reach RAM?](#1-how-does-a-program-actually-reach-ram)
- [Compile-Time Binding](#5-compile-time-binding)
- [Load-Time Binding](#6-load-time-binding)
- [Execution-Time Binding](#7-then-why-was-execution-time-binding-invented)
- [Logical Address](#8-who-generates-the-logical-address)
- [MMU & Address Translation](#11-how-does-the-mmu-use-address-mapping)
- [Shared Memory](#15-shared-memory-internals)
- [Load-Time Binding Limitations](#18-why-was-load-time-binding-not-enough)
- [Context Switch & MMU](#20-what-happens-during-a-context-switch)
- [Memory Management Evolution](#memory-management-evolution)

### Part 2 - Contiguous Memory Allocation

- [Fixed Partitioning](#fixed-partitioning)
- [Variable Partitioning](#variable-partitioning)
- [Compaction](#compaction)
- [Choosing A Free Block](#how-should-the-os-choose-a-free-block)
- [First Fit](#first-fit)
- [Best Fit](#best-fit)
- [Worst Fit](#worst-fit)
- [Comparison](#comparison)
- [Bridge To OS07](#bridge-to-os07)
- [Final Mental Model](#final-mental-model)
# Memory Management

# Part 1 - Why Memory Management Exists

---

# 1. How Does A Program Actually Reach RAM?

Suppose you write:

```cpp
#include<iostream>
using namespace std;

int main()
{
    int x = 10;
    cout << x;
}
```

Question:

Where is this program?

Answer:

Initially,

```text
Disk (SSD/HDD)
```

Not RAM.

After compilation:

```text
main.cpp
        ↓

Compiler

        ↓

main.exe
```

The executable is simply a file stored on disk.

Nothing is running yet.

---

Now you double-click:

```text
main.exe
```

What happens?

The Operating System begins execution.

Flow:

```text
Disk

↓

Executable (.exe)

↓

OS Creates Process

↓

OS Allocates RAM

↓

Executable Is Copied
Into RAM

↓

OS Creates Main Thread

↓

CPU Starts Executing main()
```

Notice something.

The CPU **never executes directly from disk.**

It always executes instructions that are already inside RAM.

Therefore,

RAM is mandatory.

---

# 2. What Exactly Gets Loaded Into RAM?

Many students think

"the program"

is copied.

Actually,

multiple sections are copied.

Example:

```cpp
int g = 100;

int main()
{
    int x = 5;

    new int;

    return 0;
}
```

Memory becomes:

```text
------------------------
Code (.text)

Machine Instructions
------------------------

Data

Global Variables

g = 100
------------------------

Heap

Dynamic Memory

new int
------------------------

Stack

Local Variables

x
------------------------
```

So when people say

"The program is loaded"

they actually mean

all these sections.

---

# 3. Then Why Doesn't The Compiler Give RAM Addresses?

Suppose today

Chrome is already using

this RAM:

```text
1000

↓

9000
```

Tomorrow,

Chrome isn't running.

The available RAM is completely different.

How can the compiler know

where your program

will finally be placed?

It cannot.

That's why

the compiler avoids

real RAM addresses.

---

# 4. Why Was Address Binding Invented?

Suppose compiler writes:

```text
Variable x

↓

RAM 5000
```

Tomorrow,

another program already occupies

5000.

Now your executable

cannot run.

You would need:

```text
Recompile Program
```

every single time.

Impossible.

Hence,

Address Binding

was invented.

Instead of fixing addresses,

we delay

the final address decision.

---

# 5. Compile-Time Binding

Old operating systems

sometimes knew

exactly where a program

would execute.

Example:

```text
Always

Address 1000
```

Compiler directly generated:

```text
Physical Address

1000

1001

1002

1003
```

Advantages:

```text
Very Fast
```

Disadvantages:

```text
Cannot Move Program

Cannot Relocate

Rarely Used Today
```

---

# 6. Load-Time Binding

This is much smarter.

Compiler says:

"I don't know

where RAM will place me."

Instead,

it creates

Relocatable Addresses.

Question:

What is a relocatable address?

Suppose compiler generates:

```text
Instruction A

Offset 0

Instruction B

Offset 4

Instruction C

Offset 8
```

Notice:

These are NOT RAM addresses.

They simply mean:

```text
Beginning

+

Offset
```

Example:

OS loads program

at:

```text
5000
```

Loader computes:

```text
5000 + 0

=

5000

--------------

5000 + 4

=

5004

--------------

5000 + 8

=

5008
```

Tomorrow,

program loads at:

```text
9000
```

Exactly same executable.

Loader computes:

```text
9000 + 0

=

9000

--------------

9000 + 4

=

9004

--------------

9000 + 8

=

9008
```

Nothing is recompiled.

This is why they are called

Relocatable Addresses.

They can be relocated

anywhere.

---

# 7. Then Why Was Execution-Time Binding Invented?

Excellent interview question.

Load-Time Binding

still has a limitation.

After program starts,

it is fixed.

Suppose:

```text
Program Loaded

At

5000
```

During execution,

OS wants to move it

to:

```text
12000
```

Can it?

No.

Because every address

already became:

```text
Physical Address
```

Program cannot move anymore.

Modern operating systems

wanted:

```text
Move Programs

While They Are Running.
```

Why?

Because later

Virtual Memory,

Paging,

Swapping,

Demand Paging

all require this.

Hence,

Execution-Time Binding

became the standard.

---

# Memory Trick

```text
Compile Time

↓

Known Before Execution

----------------------

Load Time

↓

Known While Loading

----------------------

Execution Time

↓

Known Every Time

CPU Accesses Memory
```
# 8. Who Generates The Logical Address?

This is one of the biggest interview questions.

Many students think:

```text
Operating System
```

generates addresses.

It does **not**.

The address is generated by the:

```text
CPU
```

---

Suppose:

```cpp
int x = 10;

cout << x;
```

When the CPU executes:

```cpp
cout << x;
```

it asks:

```text
"I need variable x."
```

The CPU generates:

```text
Logical Address

100
```

Notice:

The CPU has **no idea**

where x actually exists in RAM.

It only knows:

```text
Logical Address
```

Every memory access in the program works like this.

---

# 9. What Is A Logical Address Really?

A logical address is simply:

```text
The Address

Visible To The Program.
```

The process believes:

```text
x

↓

Address 100
```

This never changes.

Whether the process is loaded at:

```text
5000
```

or

```text
12000
```

the program always thinks:

```text
x

↓

100
```

This is why programs become portable.

---

# 10. How Does The CPU Access A Variable?

Suppose:

```cpp
int x = 10;

cout << x;
```

Execution:

```text
CPU Executes

↓

Needs x

↓

Generates Logical Address

100

↓

Sends 100 To MMU

↓

MMU Finds

Physical Address

5100

↓

RAM Returns Value

10

↓

CPU Prints 10
```

Notice carefully.

The CPU **never searches RAM.**

The CPU never says:

```text
Find Variable x
```

Instead it always says:

```text
Give Me

Logical Address

100
```

---

# 11. How Does The MMU Use Address Mapping?

Excellent question.

The MMU does **not** magically know.

It consults a mapping created by the Operating System.

Think of it like this:

```text
Logical

↓

Physical

Table
```

Example:

```text
Logical      Physical

0       →     5000

4       →     5004

8       →     5008

100     →     5100

104     →     5104
```

The MMU simply performs:

```text
Lookup

↓

Translation

↓

RAM Access
```

It does not decide the mapping.

It only **uses** it.

---

# 12. Who Creates This Mapping?

The Operating System.

When the program is loaded,

OS allocates RAM.

Example:

```text
Program

↓

Loaded At

5000
```

OS then creates:

```text
Logical

0

↓

Physical

5000
```

and similar entries for every address.

Later,

if the process moves:

```text
5000

↓

12000
```

OS updates:

```text
Mapping
```

The CPU never notices.

---

# 13. Where Is This Mapping Stored?

This mapping is stored in a structure called:

```text
Page Table
```

Don't worry about paging yet.

For now,

remember only:

```text
Operating System

↓

Creates Mapping

↓

Stores Mapping

↓

MMU Uses Mapping
```

Later,

in Paging,

we will study page tables in detail.

---

# 14. Why Two Processes Can Both Have Address 100?

Suppose:

Process A:

```cpp
int x;
```

Process B:

```cpp
int y;
```

Both CPUs generate:

```text
Logical

100
```

Can both exist?

Yes.

OS creates:

```text
Process A

100

↓

5100
```

Process B

```text
100

↓

14200
```

Both believe:

```text
Address 100
```

Both are correct.

The MMU translates differently

depending on

which process is currently running.

This is why processes are isolated.

---

# 15. Shared Memory Internals

Question:

If processes have different mappings,

how can they share memory?

Suppose:

```text
Process A

↓

Shared Memory

↓

Process B
```

OS allocates one physical block.

Example:

```text
Physical RAM

9000-9500
```

Then OS creates mappings:

```text
Process A

Logical 400

↓

Physical 9000
```

Process B

```text
Logical 700

↓

Physical 9000
```

Notice:

Different logical addresses.

Same physical memory.

Now:

```text
Process A Writes

↓

Physical 9000
```

Process B Reads:

```text
Physical 9000
```

Both communicate

through the same RAM.

Neither process knows

the other's logical address.

The Operating System creates this sharing.

---

# 16. Complete Memory Access Flow

```text
Executable

↓

Disk

↓

OS Creates Process

↓

OS Allocates RAM

↓

OS Creates Address Mapping

↓

CPU Executes Instruction

↓

CPU Generates Logical Address

↓

MMU Uses Mapping

↓

Physical Address

↓

RAM

↓

Value Returned

↓

CPU Continues
```

---

# 17. Complete Mental Model

```text
Program

Never Knows

Physical RAM

------------------------

CPU

Generates

Logical Address

------------------------

Operating System

Creates Mapping

------------------------

MMU

Uses Mapping

------------------------

RAM

Stores Actual Data

------------------------

Program Always Thinks

Address Never Changed

Even If

OS Moves Entire Process
```

---

# Interview Deep Questions

### Who Generates Logical Addresses?

```text
CPU
```

---

### Who Creates The Mapping?

```text
Operating System
```

---

### Who Stores The Mapping?

```text
Page Tables

(Managed By OS)
```

---

### Who Performs Translation?

```text
MMU
```

---

### Does The Program Ever Know Physical Addresses?

```text
No.
```

---

### Biggest Advantage Of Logical Addresses?

```text
OS Can Move

Processes Anywhere

Without Changing

The Program.
```

---

# One-Line Memory Trick

```text
CPU

Generates

↓

Logical Address

OS

Creates

↓

Mapping

MMU

Uses

↓

Mapping

RAM

Returns

↓

Data
```



# 18. Why Was Load-Time Binding Not Enough?

so simply rewriting the entire program, load time is simply advantage in taking offset and start from free mem but when we have to swap in and out it fails.

Suppose the loader places the program at:

```text
5000
```

It converts all offsets into **physical addresses**.

Example:

```text
Offset 0  → 5000

Offset 4  → 5004

Offset 8  → 5008
```

Now the CPU executes using these addresses:

```text
5000

5004

5008
```

Notice:

The program now directly depends on **actual RAM addresses**.

---

## Problem

Suppose later the OS needs that memory for another process.

It decides to move our program to:

```text
12000
```

The program is copied to:

```text
12000

12004

12008
```

But the CPU is still executing:

```text
5000

5004

5008
```

because those addresses were already written into the program when it was loaded.

Result:

```text
CPU Looks At

5000

↓

Program Is No Longer There

↓

Wrong Memory Is Accessed

↓

Program Crashes
```

---

## Why Can't The OS Simply Change The Addresses?

Imagine a program with:

```text
Millions Of Instructions

Thousands Of Variables

Thousands Of Pointers
```

The OS would have to find **every single physical address** and replace it.

Example:

```text
5000  → 12000

5004  → 12004

5008  → 12008

...
```

This would be extremely slow and impractical.

---

# Execution-Time Binding Solves This

Instead of generating physical addresses,

the CPU always generates:

```text
Logical

0

4

8
```

Initially:

```text
0

↓

5000
```

After moving the process:

```text
0

↓

12000
```

The CPU still generates:

```text
0

4

8
```

Nothing inside the program changes.

Only the **MMU mapping** changes.

---

# Biggest Advantage

With execution-time binding,

the OS can:

```text
Move Running Processes

Swap Processes

Implement Virtual Memory

Implement Paging
```

without modifying the program itself.

---

# Memory Trick

```text
Load-Time Binding

↓

Physical Addresses Fixed

↓

Program Cannot Move Easily

----------------------------

Execution-Time Binding

↓

Logical Addresses Fixed

↓

MMU Changes Mapping

↓

Program Can Move Anytime
```

# 18. MMU Address Translation (Complete Flow)

Until now we know:

```text
CPU

↓

Logical Address

↓

MMU

↓

Physical Address
```

But how?

Let's see every step.

---

Suppose the CPU executes:

```cpp
cout << x;
```

The CPU needs the value of:

```text
Variable x
```

The compiler has already assigned:

```text
Logical Address

100
```

So the CPU generates:

```text
100
```

The CPU does **not** know where RAM stores x.

It only knows:

```text
Logical Address

100
```

Now the CPU sends:

```text
100
```

to the MMU.

The MMU checks:

```text
"What Physical Address
Corresponds To
Logical 100?"
```

Suppose the mapping says:

```text
100

↓

12100
```

The MMU immediately returns:

```text
12100
```

RAM is accessed:

```text
RAM[12100]
```

Suppose:

```text
RAM[12100]

=

10
```

RAM returns:

```text
10
```

CPU receives:

```text
10
```

Finally:

```cpp
cout << 10;
```

Notice:

The CPU never knows

12100.

It only knows:

```text
100
```

---

# Complete Translation Flow

```text
CPU

↓

Logical Address

100

↓

MMU

↓

Looks Up Mapping

↓

Physical Address

12100

↓

RAM

↓

Returns Data

↓

CPU
```

---

# 19. Where Is The Mapping Stored?

Question:

Does the MMU remember

every address?

Answer:

```text
No.
```

The mapping is stored in a data structure called:

```text
Page Table
```

Think of it as:

```text
Address Dictionary
```

Example:

```text
Logical      Physical

0        →    5000

4        →    5004

8        →    5008

100      →    5100
```

The Operating System creates

and maintains

this table.

The MMU simply consults it.

---

## Important

Do **not** think:

```text
MMU Stores Everything
```

Correct idea:

```text
Operating System

↓

Creates Mapping

↓

Stores Page Table

↓

MMU Reads It
```

We'll study page tables in detail

during Paging.

For now,

remember only this relationship.

---

# 20. What Happens During A Context Switch?

Suppose:

```text
Process A
```

is currently running.

Its mapping is:

```text
Logical 100

↓

5100
```

Now the scheduler switches to:

```text
Process B
```

Its mapping is:

```text
Logical 100

↓

18200
```

Question:

How does the MMU know

which mapping to use?

Answer:

During every context switch,

the Operating System changes

the page table pointer.

Think of it as:

Before:

```text
MMU

↓

Page Table A
```

After context switch:

```text
MMU

↓

Page Table B
```

Nothing inside the program changes.

Only the mapping being used changes.

This is why:

```text
Both Processes

Can Own

Logical Address

100
```

without conflict.

---

# 21. Shared Memory + Synchronization

Earlier we learned:

```text
Two Processes

Can Share

One Physical Memory
```

Question:

Is shared memory alone enough?

Answer:

```text
No.
```

Suppose:

```text
Process A

Writes
```

while

```text
Process B

Reads
```

at exactly the same time.

Result:

```text
Race Condition
```

Therefore:

Shared Memory

is usually combined with:

```text
Mutex

Semaphore

Condition Variable
```

Shared Memory answers:

```text
Where Both Processes
Can Access Data
```

Synchronization answers:

```text
Who Can Access

When
```

Very important interview point.

---

# 22. Complete End-To-End Memory Access Flow

Everything we've learned

fits into one flow.

```text
Source Code

↓

Compiler

↓

Executable

↓

Disk

↓

User Runs Program

↓

Operating System

Creates Process

↓

OS Allocates RAM

↓

OS Creates Address Mapping

↓

CPU Executes Instruction

↓

CPU Generates Logical Address

↓

MMU Reads Mapping

↓

Physical Address Obtained

↓

RAM Accessed

↓

Data Returned

↓

CPU Continues Execution
```

This is exactly what happens

millions of times

every second.

---

# 23. Interview Deep Questions

### Who Generates Logical Addresses?

```text
CPU
```

---

### Who Allocates RAM?

```text
Operating System
```

---

### Who Creates Address Mapping?

```text
Operating System
```

---

### Who Uses The Mapping?

```text
MMU
```

---

### Who Stores Actual Data?

```text
RAM
```

---

### Does The Program Ever See Physical Addresses?

```text
No.
```

---

### Why Were Logical Addresses Invented?

```text
To Allow Programs

To Move In Memory

Without Modifying

The Program.
```

---

### Why Was Load-Time Binding Replaced?

```text
Because Programs

Could Not Be Moved

After Loading.
```

---

### Why Is Execution-Time Binding Used Today?

```text
Supports

Relocation

Paging

Virtual Memory

Swapping
```

---

# 24. Final Mental Model

Forget every complicated definition.

Remember this story.

```text
Executable

Lives On Disk

↓

Operating System

Loads It

Into RAM

↓

Operating System

Creates Address Mapping

↓

CPU Runs Instructions

↓

CPU Generates

Logical Address

↓

MMU Converts

Logical

↓

Physical

↓

RAM Returns Data

↓

CPU Continues

↓

If OS Moves Process

Only Mapping Changes

Program Never Knows
```

---

# Golden Memory Trick

```text
CPU

Generates

Logical Address

--------------------

Operating System

Creates Mapping

--------------------

MMU

Uses Mapping

--------------------

RAM

Stores Actual Data

--------------------

Program

Never Knows

Physical Address
```

## Interview Summary (30 Seconds)

```text
Programs never access RAM directly.

The CPU always generates logical addresses.

The Operating System creates a mapping from logical to physical addresses.

The MMU uses this mapping to translate every memory access.

Because the program only sees logical addresses, the Operating System can move the program anywhere in RAM by simply updating the mapping, without changing the program itself.
```
# Memory Management Evolution

Memory management evolved in this order:

```text
Compile-Time Binding

↓

Load-Time Binding

↓

Execution-Time Binding

↓

Paging

↓

Virtual Memory
```

Each technique solved the limitation of the previous one.

Compile-Time
↓

Program Fixed Forever.

--------------------

Load-Time
↓

Program Can Move Before Running.

--------------------

Execution-Time
↓

Program Can Move While Running.


Till here the address translation problem is solved, next is how to actually allocate ram!
"How should RAM actually be organized and allocated efficiently?"
--------------------

Paging
↓

Move Small Parts Instead Of Entire Program.

--------------------

Virtual Memory
↓

Program Can Be Larger Than RAM.

# Fixed Partitioning

After introducing contiguous memory allocation, early operating systems faced a new question.

Suppose RAM is:

```text
100 MB
```

Many programs may start during execution.

Question:

```text
How Should The OS Divide RAM?
```

The simplest solution was:

```text
Divide RAM Beforehand.
```

This technique is called:

```text
Fixed Partitioning
```

---


# What Is Fixed Partitioning?

Before any process starts,

the Operating System divides RAM into fixed-size partitions.

Example:

```text
100 MB RAM

↓

-------------------------
25 MB
-------------------------
25 MB
-------------------------
25 MB
-------------------------
25 MB
-------------------------
```

These partitions never change.

Every arriving process receives one partition.

---

## Example

Suppose:

```text
Process A = 18 MB

Process B = 20 MB

Process C = 24 MB
```

Allocation:

```text
-------------------------
A (18 MB)
7 MB Unused
-------------------------

B (20 MB)
5 MB Unused
-------------------------

C (24 MB)
1 MB Unused
-------------------------

Free Partition
-------------------------
```

Notice something.

Every process fits.

Yet memory is still wasted.

---

# Why?

Each partition has a fixed size.

Suppose:

```text
Partition

25 MB
```

Process requires:

```text
18 MB
```

The remaining:

```text
7 MB
```

cannot be given

to another process.

It remains unused.

This wasted memory is called:

```text
Internal Fragmentation
```

---

# Why "Internal"?

Because the waste exists:

```text
Inside

The Allocated Partition
```

Example:

```text
----------------------

25 MB Partition

----------------------

18 MB Used

7 MB Wasted

----------------------
```

The partition is allocated.

The unused space is trapped inside it.

---

# Real-Life Analogy

Imagine a hostel.

Every room has:

```text
4 Beds
```

One student arrives.

He receives:

```text
Entire Room
```

Three beds remain empty.

Can another student occupy those beds?

No.

The room is already allocated.

Those empty beds are exactly like:

```text
Internal Fragmentation
```

---

# Advantages Of Fixed Partitioning

```text
Very Simple

Fast Allocation

Easy To Implement

No External Fragmentation
```

---

# Disadvantages

```text
Internal Fragmentation

Limited Number Of Processes

Partition Size Never Changes

Poor RAM Utilization
```

---

# Biggest Limitation

Suppose every partition is:

```text
25 MB
```

A process requires:

```text
30 MB
```

Can it execute?

```text
No.
```

Even if total free RAM is:

```text
75 MB
```

it cannot fit inside any single partition.

The process must wait.

---

# Memory Trick

```text
Fixed Partitioning

↓

RAM Divided Beforehand

↓

One Process

One Partition

↓

Unused Space Inside

↓

Internal Fragmentation
```

---

# Interview Questions

### What Is Fixed Partitioning?

```text
RAM Is Divided Into
Fixed-Size Partitions
Before Execution.
```

---

### Biggest Advantage?

```text
Simple

Fast
```

---

### Biggest Disadvantage?

```text
Internal Fragmentation
```

---

# Evolution

```text
Contiguous Allocation

↓

Fixed Partitioning

↓

Simple

↓

Memory Wasted

↓

Internal Fragmentation

↓

Need Better Technique
```


# Variable Partitioning

Fixed Partitioning solved one problem,

but created another.

Question:

```text
Why Should Every Process
Receive The Same Partition Size?
```

Suppose:

```text
Partition Size

25 MB
```

Processes:

```text
18 MB

12 MB

9 MB
```

Most of every partition remains unused.

Memory is wasted.

The Operating System needed a better idea.

---

# The Idea

Instead of dividing RAM beforehand,

why not create a partition

only when a process arrives?

This is called:

```text
Variable Partitioning
```

---

# What Is Variable Partitioning?

The OS allocates

exactly the amount of memory

requested by the process.

Example:

Process A needs:

```text
20 MB
```

OS allocates:

```text
20 MB
```

Process B needs:

```text
35 MB
```

OS allocates:

```text
35 MB
```

No fixed partition sizes exist.

Every partition is created dynamically.

---

# Example

Initially:

```text
-------------------------
100 MB Free
-------------------------
```

Process A:

```text
20 MB
```

Memory:

```text
-------------------------
A (20 MB)
-------------------------
80 MB Free
-------------------------
```

Process B:

```text
30 MB
```

Memory:

```text
-------------------------
A (20 MB)
-------------------------
B (30 MB)
-------------------------
50 MB Free
-------------------------
```

Process C:

```text
10 MB
```

Memory:

```text
-------------------------
A (20 MB)
-------------------------
B (30 MB)
-------------------------
C (10 MB)
-------------------------
40 MB Free
-------------------------
```

Notice:

Almost no memory is wasted.

---

# Why Is It Better?

Unlike Fixed Partitioning,

the OS gives:

```text
Exactly

What The Process Needs.
```

Suppose:

```text
Process

18 MB
```

OS allocates:

```text
18 MB
```

Not:

```text
25 MB
```

Therefore,

Internal Fragmentation becomes very small (or practically absent in the basic model).

---

# New Problem

Suppose memory becomes:

```text
--------------------------------

A (20 MB)

--------------------------------

Free (30 MB)

--------------------------------

B (25 MB)

--------------------------------

Free (15 MB)

--------------------------------

C (20 MB)

--------------------------------
```

Now a process needs:

```text
40 MB
```

Question:

Can it fit?

Answer:

```text
NO
```

Even though total free memory is:

```text
45 MB
```

because the free memory is

split into different places.

This problem is called:

```text
External Fragmentation
```

---

# Why "External"?

Because the wasted space exists

outside

the allocated processes.

Example:

```text
------------------------

Process

------------------------

Free Hole

------------------------

Process

------------------------

Free Hole

------------------------
```

The holes are

outside the allocated memory.

Hence the name:

```text
External Fragmentation
```

---

# Internal vs External Fragmentation

## Internal Fragmentation

```text
Waste

Inside

Allocated Memory
```

Example:

```text
25 MB Partition

↓

18 MB Used

7 MB Wasted
```

---

## External Fragmentation

```text
Waste

Outside

Allocated Memory
```

Example:

```text
Process

↓

Free Hole

↓

Process

↓

Free Hole
```

Total free memory may be enough,

but it is scattered.

---

# Real-Life Analogy

Imagine a parking lot.

Cars leave at random.

Now the parking lot looks like:

```text
Car

Empty

Car

Empty

Car
```

A bus arrives.

Needs:

```text
Two Adjacent Spaces
```

Total empty spaces are available,

but they are separated.

The bus cannot park.

Exactly the same thing happens

in External Fragmentation.

---

# Why Doesn't The OS Simply Join The Holes?

Excellent interview question.

Suppose:

```text
Free

Process

Free
```

Can the OS join both free blocks?

```text
No.
```

Because:

```text
A Running Process

Exists Between Them.
```

The process blocks the holes

from becoming one large block.

---

# Memory Trick

```text
Fixed Partitioning

↓

Internal Fragmentation

--------------------------

Variable Partitioning

↓

External Fragmentation
```

---

# Interview Questions

### Why Was Variable Partitioning Introduced?

```text
To Reduce

Memory Wasted

By Fixed Partitions.
```

---

### Biggest Advantage?

```text
Allocates

Exactly

Required Memory.
```

---

### Biggest Disadvantage?

```text
External Fragmentation.
```

---

### Difference Between Fixed And Variable Partitioning?

```text
Fixed

↓

Partitions Created Beforehand

↓

Internal Fragmentation

----------------------------

Variable

↓

Partitions Created Dynamically

↓

External Fragmentation
```

---

# Evolution

```text
Fixed Partitioning

↓

Internal Fragmentation

↓

Variable Partitioning

↓

External Fragmentation

↓

Need Another Solution...
```

# Compaction

We learned that Variable Partitioning creates:

```text
External Fragmentation
```

Example:

```text
----------------------------------

Process A

----------------------------------

Free (20 MB)

----------------------------------

Process B

----------------------------------

Free (15 MB)

----------------------------------

Process C

----------------------------------
```

Total Free Memory:

```text
35 MB
```

A new process needs:

```text
30 MB
```

Question:

Can it execute?

```text
No.
```

Why?

Because there is **no single continuous block** of 30 MB.

---

# The Idea Behind Compaction

Instead of leaving free holes scattered,

why not move all running processes together?

Then all free memory becomes one large block.

This process is called:

```text
Compaction
```

---

# How Compaction Works

Initially:

```text
----------------------------------

Process A

----------------------------------

Free (20 MB)

----------------------------------

Process B

----------------------------------

Free (15 MB)

----------------------------------

Process C

----------------------------------
```

The OS moves the processes.

After moving:

```text
----------------------------------

Process A

----------------------------------

Process B

----------------------------------

Process C

----------------------------------

Free (35 MB)

----------------------------------
```

Now,

a process needing:

```text
30 MB
```

can be allocated.

---

# Why Doesn't The OS Always Perform Compaction?

Excellent interview question.

Moving one process means moving:

```text
Code

Data

Heap

Stack
```

Everything must be copied.

Then,

the OS must update:

```text
Address Mapping
```

for the moved process.

If there are hundreds of processes,

this becomes expensive.

---

# Why Is Compaction Slow?

Suppose:

```text
200 Processes
```

Each process has:

```text
100 MB
```

Total data moved:

```text
20 GB
```

Copying huge amounts of memory

takes time.

During compaction,

many processes may also need to pause.

Therefore,

compaction is **not performed frequently**.

---

# Memory Trick

```text
External Fragmentation

↓

Compaction

↓

Move Processes

↓

Create One Large Free Block
```

---

# Interview Question

### What Is Compaction?

```text
Moving Running Processes

To Combine

Scattered Free Memory

Into One Large Block.
```

---

### Why Is Compaction Expensive?

```text
Processes Must Be Moved

Memory Must Be Copied

Mappings Must Be Updated
```

---

# How Should The OS Choose A Free Block?

Suppose RAM looks like this:

```text
--------------------------------

Free 100 MB

--------------------------------

Process

--------------------------------

Free 20 MB

--------------------------------

Process

--------------------------------

Free 60 MB

--------------------------------

Process

--------------------------------

Free 40 MB

--------------------------------
```

A new process arrives.

Needs:

```text
18 MB
```

Question:

Which free block

should the OS choose?

Several algorithms were invented.

---

# First Fit

## Idea

Scan memory

from the beginning.

Choose

the **first block**

that is large enough.

---

Example:

```text
Free

100 MB

20 MB

60 MB

40 MB
```

Need:

```text
18 MB
```

OS checks:

```text
100 MB

↓

Enough

↓

Allocate Here
```

The search stops immediately.

Remaining:

```text
82 MB
```

---

## Advantages

```text
Very Fast

Simple

Less Searching
```

---

## Disadvantages

Over time,

many small holes appear

near the beginning of RAM.

---

# Memory Trick

```text
First Fit

↓

First Suitable Block
```

---

# Best Fit

## Idea

Search

every free block.

Choose

the **smallest block**

that can hold the process.

---

Example:

```text
Free Blocks

100 MB

20 MB

60 MB

40 MB
```

Need:

```text
18 MB
```

OS checks all blocks.

Best choice:

```text
20 MB
```

Only:

```text
2 MB
```

is wasted.

---

## Advantage

Initially,

less unused memory remains.

---

## Disadvantage

Must search

every free block.

Slower.

Also,

it creates

many tiny holes

that become unusable.

---

# Memory Trick

```text
Best Fit

↓

Smallest Suitable Block
```

---

# Worst Fit

## Idea

Choose

the largest free block.

---

Example:

```text
100 MB

20 MB

60 MB

40 MB
```

Need:

```text
18 MB
```

Choose:

```text
100 MB
```

Remaining:

```text
82 MB
```

The idea is:

```text
Leave

Large Useful Holes

Instead Of

Many Tiny Ones
```

---

## Advantage

Leaves

larger remaining holes.

---

## Disadvantage

Often wastes

large free blocks.

Rarely used.

---

# Memory Trick

```text
Worst Fit

↓

Largest Free Block
```

---

# Comparison

| Algorithm | Chooses | Speed | Memory Usage |
|-----------|---------|-------|--------------|
| First Fit | First suitable block | Fastest | Good |
| Best Fit | Smallest suitable block | Slow | Creates tiny holes |
| Worst Fit | Largest block | Slow | Preserves larger holes |

---

# Which One Is Used?

Interview answer:

```text
First Fit
```

Reason:

```text
Simple

Fast

Good Enough

Lower Searching Cost
```

Best Fit

looks better on paper,

but over time,

it creates

many tiny unusable holes.

Worst Fit

is rarely used.

---

# Memory Trick

```text
First Fit

↓

Fast

-------------------

Best Fit

↓

Least Immediate Waste

-------------------

Worst Fit

↓

Largest Hole
```

---

# Evolution

```text
Variable Partitioning

↓

External Fragmentation

↓

Compaction

↓

Allocation Algorithms

↓

Still Not Perfect

↓

Need Better Memory Management

↓

Paging (OS07)
```

---

# Bridge To OS07

Even after:

```text
Compaction

First Fit

Best Fit

Worst Fit
```

the Operating System still had one major problem:

```text
External Fragmentation
```

Compaction could reduce it,

but compaction itself was expensive.

Engineers asked:

> **Why should a process occupy one continuous block at all?**

That single question led to the invention of:

```text
Paging
```

which completely changed modern memory management.

OS07 begins from there.

---

# Final Mental Model

```text
Need Memory

↓

Contiguous Allocation

↓

Fixed Partitioning

↓

Internal Fragmentation

↓

Variable Partitioning

↓

External Fragmentation

↓

Compaction

↓

First / Best / Worst Fit

↓

Still Not Good Enough

↓

Paging
```
