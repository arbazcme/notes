# OS 07 - Paging & Virtual Memory

## Navigation

### Part 1 - Why Paging Was Invented

- [1. Why Contiguous Memory Allocation Failed](#1-why-contiguous-memory-allocation-failed)
- [2. The Big Question Engineers Asked](#2-the-big-question-engineers-asked)
- [3. The Core Idea Behind Paging](#3-the-core-idea-behind-paging)
- [4. Pages And Frames](#4-pages-and-frames)
- [5. How A Process Is Divided](#5-how-a-process-is-divided)
- [6. How RAM Is Divided](#6-how-ram-is-divided)
- [7. Page Allocation Example](#7-page-allocation-example)
- [8. Why Paging Eliminates External Fragmentation](#8-why-paging-eliminates-external-fragmentation)
- [9. Does Paging Create Any New Problem?](#9-does-paging-create-any-new-problem)
- [10. Interview Summary](#10-interview-summary)

### Part 2 - Address Translation

- [1. The New Problem After Paging](#1-the-new-problem-after-paging)
- [2. What Is A Page Table?](#2-what-is-a-page-table)
- [3. Who Creates The Page Table?](#3-who-creates-the-page-table)
- [4. Who Uses The Page Table?](#4-who-uses-the-page-table)
- [5. Virtual Address Format](#5-virtual-address-format)
  - [What Is The Page Number?](#what-is-the-page-number)
  - [What Is The Offset?](#what-is-the-offset)
- [6. Complete Address Translation](#6-complete-address-translation)
- [7. Why Is The Offset Never Changed?](#7-why-is-the-offset-never-changed)
- [8. Why Is Paging Better Than Contiguous Allocation?](#8-why-is-paging-better-than-contiguous-allocation)
- [9. New Problem Created By Paging](#9-new-problem-created-by-paging)
- [10. Interview Questions](#10-interview-questions)
- [11. Final Mental Model](#11-final-mental-model)


### Part 3 - Translation Lookaside Buffer (TLB)

- [1. The Problem With Page Tables](#1-the-problem-with-page-tables)
- [2. Why Was TLB Invented?](#2-why-was-tlb-invented)
- [3. What Is A TLB?](#3-what-is-a-tlb)
- [4. Where Is The TLB Located?](#4-where-is-the-tlb-located)
- [5. How Address Translation Works With A TLB](#5-how-address-translation-works-with-a-tlb)
- [6. TLB Hit](#6-tlb-hit)
- [7. TLB Miss](#7-tlb-miss)
- [8. Why Is A TLB So Small?](#8-why-is-a-tlb-so-small)
- [9. Why Does A Small TLB Still Work Well?](#9-why-does-a-small-tlb-still-work-well)
- [10. TLB Vs Page Table](#10-tlb-vs-page-table)
- [11. Interview Questions](#11-interview-questions)
- [12. Final Mental Model](#12-final-mental-model)

### Part 4 - Virtual Memory & Demand Paging

- [1. The Problem With RAM](#1-the-problem-with-ram)
- [2. Why Was Virtual Memory Invented?](#2-why-was-virtual-memory-invented)
- [3. What Is Virtual Memory?](#3-what-is-virtual-memory)
- [4. Secondary Storage As An Extension Of RAM](#4-secondary-storage-as-an-extension-of-ram)
- [5. Why Not Load The Entire Program?](#5-why-not-load-the-entire-program)
- [6. Principle Of Locality](#6-principle-of-locality)
- [7. Demand Paging](#7-demand-paging)
- [8. Page Fault](#8-page-fault)
- [9. Handling A Page Fault](#9-handling-a-page-fault)
- [10. Valid And Invalid Page Faults](#10-valid-and-invalid-page-faults)
- [11. Interview Questions](#11-interview-questions)
- [12. Final Mental Model](#12-final-mental-model)

### Swapping vs Virtual Memory

- [Swapping (Older Concept)](#swapping-older-concept)
- [Virtual Memory / Demand Paging (Modern Concept)](#virtual-memory--demand-paging-modern-concept)
- [Mental Model](#mental-model)
- [Key Difference](#key-difference)


### Part 5 - Page Replacement Algorithms

- [1. Why Is Page Replacement Needed?](#1-why-is-page-replacement-needed)
- [2. What Happens When RAM Becomes Full?](#2-what-happens-when-ram-becomes-full)
- [3. What Is Page Replacement?](#3-what-is-page-replacement)
- [4. FIFO Page Replacement](#4-fifo-page-replacement)
- [5. Belady's Anomaly](#5-beladys-anomaly)
- [6. Interview Questions](#6-interview-questions)
- [7. Optimal Page Replacement](#7-optimal-page-replacement)
- [8. LRU (Least Recently Used)](#8-lru-least-recently-used)
- [9. FIFO vs Optimal vs LRU](#9-fifo-vs-optimal-vs-lru)
- [10. Interview Questions](#10-interview-questions)
- [11. Memory Trick](#11-memory-trick)
- [12. LFU (Least Frequently Used)](#12-lfu-least-frequently-used)
- [13. Second Chance (Clock) Algorithm](#13-second-chance-clock-algorithm)
- [14. Frame Allocation](#14-frame-allocation)
  - [Equal Allocation](#equal-allocation)
  - [Proportional Allocation](#proportional-allocation)
  - [Global Replacement](#global-replacement)
  - [Local Replacement](#local-replacement)
- [15. Thrashing](#15-thrashing)
- [16. Complete Algorithm Comparison](#16-complete-algorithm-comparison)
- [17. Final Interview Summary](#17-final-interview-summary)




# Part 1 - Why Paging Was Invented

---

# 1. Why Contiguous Memory Allocation Failed

At the end of OS06,

we reached this point:

```text
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

Still Not Good Enough
```

Question:

```text
Why?
```

Because every process still required:

```text
One Continuous Block
```

Example:

```text
RAM

--------------------------------

Free 20 MB

--------------------------------

Process

--------------------------------

Free 30 MB

--------------------------------

Process

--------------------------------
```

Suppose a new process needs:

```text
40 MB
```

Question:

Can it fit?

```text
No.
```

Even though:

```text
20 MB + 30 MB = 50 MB
```

There is no single

continuous block.

This was the biggest limitation of contiguous allocation.

---

# 2. The Big Question Engineers Asked

Instead of asking:

```text
Where Can We Find

One Large Block?
```

Engineers asked something much smarter.

They asked:

> **Why should a process occupy one continuous block at all?**

That single question changed memory management forever.

---

# 3. The Core Idea Behind Paging

Imagine a book.

Does every chapter need to be printed

on one huge sheet?

No.

The book is divided into:

```text
Pages
```

Each page is independent.

The same idea was applied to memory.

Instead of storing a process

as one huge block,

the Operating System divides it into

small fixed-size pieces.

These pieces are called:

```text
Pages
```

---

# 4. Pages And Frames

Two new terms appear.

## Page

A fixed-size block

of a process.

Example:

```text
Process

↓

Page 0

Page 1

Page 2

Page 3
```

---

## Frame

A fixed-size block

of physical RAM.

Example:

```text
RAM

↓

Frame 0

Frame 1

Frame 2

Frame 3

Frame 4

Frame 5
```

Very important:

```text
Page

=

Process Piece

--------------------

Frame

=

RAM Piece
```

---

# 5. How A Process Is Divided

Suppose a process size is:

```text
16 KB
```

Page size:

```text
4 KB
```

Number of pages:

```text
16 ÷ 4

=

4 Pages
```

Process becomes:

```text
Page 0

4 KB

---------------

Page 1

4 KB

---------------

Page 2

4 KB

---------------

Page 3

4 KB
```

Notice:

The process no longer needs

one continuous block.

---

# 6. How RAM Is Divided

Suppose RAM is:

```text
32 KB
```

Frame size:

```text
4 KB
```

RAM becomes:

```text
Frame 0

Frame 1

Frame 2

Frame 3

Frame 4

Frame 5

Frame 6

Frame 7
```

Notice something.

Page size

and

Frame size

are always identical.

Example:

```text
Page

4 KB

↓

Frame

4 KB
```

Otherwise,

a page could never fit

inside a frame.

---

# 7. Page Allocation Example

Suppose the process has:

```text
4 Pages
```

RAM has free frames:

```text
Frame 1

Frame 4

Frame 7

Frame 9
```

The Operating System simply stores:

```text
Page 0

↓

Frame 7

-------------------

Page 1

↓

Frame 1

-------------------

Page 2

↓

Frame 9

-------------------

Page 3

↓

Frame 4
```

Notice carefully.

The pages are

completely scattered.

Yet,

the process runs perfectly.

This is the biggest achievement of paging.

---

# 8. Why Paging Eliminates External Fragmentation

Remember OS06.

We needed:

```text
One Continuous Block
```

Now we don't.

Every page only needs:

```text
One Free Frame
```

Example:

```text
Frame 2

Free

--------------

Frame 6

Free

--------------

Frame 12

Free

--------------

Frame 18

Free
```

Perfect.

The OS simply places

different pages

into different frames.

Therefore,

external fragmentation

is practically eliminated.

---

# 9. Does Paging Create Any New Problem?

Yes.

Now pages are

scattered everywhere.

Question:

How does the CPU know

where

Page 2

actually exists?

Example:

```text
Page 2

↓

Frame 9
```

Some mapping

must exist.

That mapping is called:

```text
Page Table
```

## Page Table Mapping

Instead, it stores the mapping:

```text
Virtual Page Number  →  Physical Frame Number
```

## MMU Translation

The MMU uses this mapping to translate a complete virtual address.

**Example:**

```text
Virtual Address

Page Number = 2
Offset      = 100
```

## Page Table Lookup

The page table says:

```text
Page 2 → Frame 3
```

## Physical Address

So the MMU produces:

```text
Physical Address

Frame 3 + Offset 100
```

This becomes

the next topic.

---

# 10. Interview Summary

## Why Was Paging Invented?

```text
To Remove

The Requirement

That A Process

Must Occupy

One Continuous Block

Of Memory.
```

---

## Difference

```text
Contiguous Allocation

↓

One Large Block

----------------------

Paging

↓

Many Small Blocks
```

---

## Memory Trick

```text
Need Continuous Memory?

↓

No

↓

Split Process

Into Pages

↓

Store Pages

Anywhere

↓

Use Page Table

To Remember

Their Locations
```

---

# Evolution

```text
Contiguous Allocation

↓

External Fragmentation

↓

Compaction

↓

Still Expensive

↓

Paging

↓

Pages

↓

Frames

↓

Page Table

↓

Modern Operating Systems
```

# Part 2 - Address Translation

---

# 1. The New Problem After Paging

In Part 1 we learned:

```text
Process

↓

Pages

↓

Stored In

Different Frames
```

Example:

```text
Page 0

↓

Frame 7

------------------

Page 1

↓

Frame 2

------------------

Page 2

↓

Frame 10

------------------

Page 3

↓

Frame 5
```

Question:

How does the CPU know

that

```text
Page 2

↓

Frame 10
```

Answer:

The Operating System creates

a data structure called:

```text
Page Table
```

---

# 2. What Is A Page Table?

A Page Table is simply

a mapping between

Virtual Pages

and

Physical Frames.

Example:

```text
Virtual Page

↓

Physical Frame

-------------------

0

↓

7

-------------------

1

↓

2

-------------------

2

↓

10

-------------------

3

↓

5
```

Nothing more.

It is simply a lookup table.

---

# 3. Who Creates The Page Table?

Remember OS06.

The Operating System

creates

all address mappings.

Exactly the same happens here.

Flow:

```text
Program Starts

↓

OS Creates Process

↓

OS Divides Process

Into Pages

↓

OS Finds Free Frames

↓

OS Creates Page Table
```

The CPU never creates

the page table.

---

# 4. Who Uses The Page Table?

Question:

If the OS created it,

who actually uses it?

Answer:

```text
MMU
```
---

# 5. Virtual Address Format

The CPU no longer generates

one single address.

Instead,

every virtual address

contains two parts.

```text
Virtual Address

↓

Page Number

+

Offset
```

---

## What Is The Page Number?

The page number answers:

```text
Which Page?
```

Example:

```text
Page 5
```

---

## What Is The Offset?

The offset answers:

```text
Which Byte

Inside That Page?
```

Example:

```text
Offset

250
```

---

# Real Life Analogy

Imagine a book.

Question:

Where is a word?

Answer:

```text
Page

↓

Line

↓

Word
```

Similarly,

memory says:

```text
Page

↓

Offset
```

The page identifies

the page.

The offset identifies

the exact byte

inside that page.

---

# 6. Complete Address Translation

Suppose:

Virtual Address:

```text
Page = 2

Offset = 100
```

MMU checks:

```text
Page Table

↓

Page 2

↓

Frame 10
```

The MMU now knows:

```text
Frame = 10

Offset = 100
```

Finally:

```text
Physical Address

↓

Start Of Frame 10

+

Offset 100
```

RAM returns the data.

---

# 7. Why Is The Offset Never Changed?

Very important interview question.

Suppose:

```text
Page Size

4 KB
```

Frame Size:

```text
4 KB
```

Every page

fits perfectly

inside one frame.

Therefore,

if the CPU asks for

```text
Byte 300
```

inside a page,

the MMU simply asks for

```text
Byte 300
```

inside the frame.

Nothing changes.

Only

the page number

changes.

---

Example:

```text
Virtual

Page 2

Offset 300

↓

Physical

Frame 9

Offset 300
```

Notice:

```text
300

Stayed

300
```

Only:

```text
Page

↓

Frame
```

changed.

---


# 8. Why Is Paging Better Than Contiguous Allocation?

Contiguous Allocation:

```text
Need

One Large Block
```

Paging:

```text
Need

One Free Frame

For Each Page
```

Huge difference.

---

# 9. New Problem Created By Paging

Every memory access now requires:

```text
CPU

↓

MMU

↓

Page Table

↓

RAM
```

Question:

Doesn't this become slower?

Yes.

Every access now requires

another lookup.

Engineers solved this by inventing:

```text
TLB

(Translation Lookaside Buffer)
```

That is our next topic.

---

# Interview Questions

## What Does The Page Table Store?

```text
Page Number

↓

Frame Number
```

---

## Who Creates The Page Table?

```text
Operating System
```

---

## Who Uses The Page Table?

```text
MMU
```

---

## Which Part Changes During Translation?

```text
Page Number
```

---

## Which Part Never Changes?

```text
Offset
```

---

# Final Mental Model

```text
CPU

↓

Virtual Address

↓

Page Number

+

Offset

↓

MMU

↓

Page Table

↓

Frame Number

↓

Frame Start Address

↓

Add Offset

↓

Physical Address

↓

RAM
```

# Part 3 - Translation Lookaside Buffer (TLB)

---

# 1. The Problem With Page Tables

In Part 2 we learned:

```text
CPU

↓

Virtual Address

↓

MMU

↓

Page Table

↓

Frame Number

↓

RAM
```

Looks perfect.

But there is one problem.

Question:

Where is the Page Table stored?

Answer:

```text
RAM
```

Now imagine the CPU wants

one value from memory.

Example:

```cpp
x = y + z;
```

The CPU first needs to find

where

```text
y
```

is stored.

To do that,

it first accesses:

```text
Page Table
```

which itself is stored in RAM.

Then,

after obtaining the frame number,

it accesses RAM again

to read the actual data.

One memory access has become:

```text
RAM Access

↓

Page Table

↓

RAM Access

↓

Actual Data
```

Memory access has effectively doubled.

---

# 2. Why Was TLB Invented?

Engineers asked:

> Why should we search RAM for the page table every single time?

Most programs repeatedly access

the same pages.

Example:

```cpp
for(int i=0;i<1000;i++)
    sum += arr[i];
```

The CPU repeatedly accesses

the same few pages.

Searching the page table

again and again

is unnecessary.

The solution was:

```text
Keep Frequently Used

Page Table Entries

Inside The CPU.
```

This small cache is called:

```text
Translation Lookaside Buffer

(TLB)
```

---

# 3. What Is A TLB?

A TLB is simply

a very small,

very fast cache

that stores

recently used

Page Table entries.

Example:

```text
Page

↓

Frame

--------------

2

↓

15

--------------

5

↓

8

--------------

7

↓

21
```

Notice.

This looks exactly like

a page table.

The only difference is:

```text
Very Small

Very Fast
```

---

# 4. Where Is The TLB Located?

The Page Table is stored in:

```text
RAM
```

The TLB is stored:

```text
Inside

The CPU/MMU
```

```text
Refer ->

CPU Chip
│
├── ALU
├── Registers
├── Cache
├── MMU
│    └── TLB
└── Control Unit
```

Therefore,

accessing the TLB

is much faster

than accessing RAM.

---

# 5. How Address Translation Works With A TLB

Every memory access now follows:

```text
CPU

↓

Virtual Address

↓

TLB

↓

Found?

↓

Yes

↓

Frame Number

↓

RAM

↓

Done
```

If the page is not found,

another path is followed.

---

# 6. TLB Hit

Suppose:

Virtual Address:

```text
Page 5
```

The TLB already contains:

```text
Page 5

↓

Frame 18
```

Excellent.

No page table lookup is needed.

Flow:

```text
CPU

↓

TLB

↓

Frame Number

↓

RAM
```

Very fast.

This is called:

```text
TLB Hit
```

---

# 7. TLB Miss

Suppose:

The CPU requests:

```text
Page 12
```

The TLB does not contain it.

Flow becomes:

```text
CPU

↓

TLB

↓

Not Found

↓

Page Table

↓

Frame Number

↓

Update TLB

↓

RAM
```

This is called:

```text
TLB Miss
```

Notice something important.

After the miss,

the new mapping is inserted

into the TLB.

Future accesses

become hits.

---

# 8. Why Is A TLB So Small?

Question:

Why not store

the entire Page Table

inside the CPU?

Because page tables

can contain

millions of entries.

CPU chips have

very limited space.

Instead,

the TLB stores only

the most recently

or frequently used entries.

Typical size:

```text
32

64

128

256 Entries
```

---

# 9. Why Does A Small TLB Still Work Well?

Programs usually exhibit:

```text
Locality Of Reference
```

Meaning:

Programs repeatedly access

the same pages

for short periods of time.

Example:

```cpp
for(int i=0;i<1000;i++)
```

The same pages

are accessed

again and again.

Therefore,

even a tiny TLB

achieves

a very high hit rate.

---

# 10. TLB Vs Page Table

| TLB | Page Table |
|-----|------------|
| Very Small | Very Large |
| Inside CPU/MMU | Stored In RAM |
| Very Fast | Slower |
| Cache | Complete Mapping |
| Stores Recent Entries | Stores Every Mapping |

---

# 11. Interview Questions

### Why Was TLB Invented?

```text
To Avoid

Repeated Page Table

Lookups

In RAM.
```

---

### What Does TLB Store?

```text
Recent

Page

↓

Frame

Mappings.
```

---

### Difference Between TLB And Page Table?

```text
TLB

↓

Small Cache

----------------

Page Table

↓

Complete Mapping
```

---

### What Is A TLB Hit?

```text
Required Mapping

Found

Inside TLB.
```

---

### What Is A TLB Miss?

```text
Mapping Not Found

TLB

↓

Search Page Table

↓

Update TLB
```

---

# 12. Final Mental Model

```text
CPU

↓

Virtual Address

↓

TLB

↓

Hit?

↓

Yes

↓

Frame

↓

RAM

------------------------

No

↓

Page Table

↓

Update TLB

↓

Frame

↓

RAM
```

---

# Evolution

```text
Contiguous Allocation

↓

Paging

↓

Page Table

↓

Too Many RAM Lookups

↓

TLB

↓

Fast Address Translation
```

# Part 4 - Virtual Memory & Demand Paging

---

# 1. The Problem With RAM

So far we assumed

every page

of every process

fits inside RAM.

But imagine:

```text
RAM

8 GB
```

Now suppose the user opens:

```text
Chrome

VS Code

Spotify

Discord

Photoshop

Game
```

Together they require:

```text
18 GB
```

Question:

```text
Can Everything Fit
Inside 8 GB RAM?
```

Answer:

```text
No.
```

Without a better solution,

the OS would have to say:

```text
Cannot Run
```

This was unacceptable.

---

# 2. Why Was Virtual Memory Invented?

Engineers asked:

> Do we really need the **entire program** in RAM?

Think about Chrome.

Suppose Chrome contains:

```text
100 MB
```

of code.

While you're browsing,

does the CPU execute

all 100 MB?

No.

Most of the program

isn't being used

at that moment.

Only a small portion

is active.

This observation

led to Virtual Memory.

---

# 3. What Is Virtual Memory?

Virtual Memory is the idea that

a process

does **not**

need to be completely loaded

into RAM.

Instead:

```text
Frequently Used Pages

↓

RAM

--------------------

Unused Pages

↓

Disk
```

The disk acts as

an extension of RAM.

---

# 4. Secondary Storage As An Extension Of RAM

Remember:

```text
RAM

↓

Fast

Small

Expensive
```

Disk (SSD/HDD):

```text
Large

Cheap

Slow
```

Virtual Memory combines

both.

Think of it like:

```text
RAM

=

Study Table

------------------

Disk

=

Bookshelf
```

You keep

only the books

you're currently reading

on the table.

The remaining books

stay on the shelf.

---

# 5. Why Not Load The Entire Program?

Suppose:

```text
Microsoft Word

250 MB
```

You open it

only to type

one paragraph.

Question:

Should all

250 MB

be loaded?

No.

Most features

like templates,

mail merge,

advanced tools,

etc.

are not being used.

Loading everything

would waste RAM.

---

# 6. Principle Of Locality

Programs usually behave

in a predictable way.

They repeatedly execute

the same nearby code.

Example:

```cpp
for(int i=0;i<1000;i++)
{
    sum += arr[i];
}
```

The CPU repeatedly accesses

the same few pages.

This property is called:

```text
Locality Of Reference
```

Because of locality,

keeping only

a few pages

inside RAM

works surprisingly well.

---

# 7. Demand Paging

Question:

When should a page

be loaded

into RAM?

Answer:

```text
Only When Needed.
```

This technique is called:

```text
Demand Paging
```

Instead of loading

all pages,

the OS loads

only the pages

currently required.

---

# Example

Program contains:

```text
Page 0

Page 1

Page 2

Page 3

Page 4

Page 5
```

Initially RAM contains:

```text
Page 0

Page 1
```

Later,

the CPU requests:

```text
Page 4
```

The OS loads:

```text
Page 4
```

only then.

---

# 8. Page Fault

Suppose the CPU requests:

```text
Page 6
```

Question:

Is Page 6

currently in RAM?

If:

```text
No
```

the hardware raises:

```text
Page Fault
```

Don't panic.

A page fault

is **not an error**.

It simply means:

```text
The Required Page

Is Not In RAM.
```

---

# 9. Handling A Page Fault

Complete flow:

```text
CPU

↓

Requests Page

↓

Page Not In RAM

↓

Page Fault

↓

OS Interrupts

↓

Loads Page

From Disk

↓

Updates Page Table

↓

Restarts Instruction

↓

Program Continues
```

The program

never knows

this happened.

---

# 10. Valid And Invalid Page Faults

Not every page fault

is acceptable.

## Valid Page Fault

Example:

The page belongs

to the process

but is currently

stored on disk.

The OS simply loads it.

---

## Invalid Page Fault

Suppose the program

tries to access:

```cpp
int *p = nullptr;
*p = 5;
```

or accesses memory

it doesn't own.

The page

doesn't exist.

The OS cannot load it.

Result:

```text
Segmentation Fault

(Process Terminated)
```

---

# 11. Interview Questions

### Why Was Virtual Memory Invented?

```text
To Execute Programs

Larger Than RAM

And Improve

RAM Utilization.
```

---

### Why Doesn't The OS Load The Entire Program?

```text
Because Most Pages

Are Not Used

Immediately.
```

---

### What Is Demand Paging?

```text
Load A Page

Only When

It Is Needed.
```

---

### What Is A Page Fault?

```text
The Requested Page

Is Not Present

In RAM.
```

---

### Is Every Page Fault An Error?

```text
No.

Most Page Faults

Are Normal.
```

---

# 12. Final Mental Model

```text
Program

↓

Pages

↓

Frequently Used

↓

RAM

--------------------

Unused

↓

Disk

--------------------

CPU Requests Page

↓

Present?

↓

Yes

↓

Continue

--------------------

No

↓

Page Fault

↓

OS Loads Page

↓

Continue
```

---

# Evolution

```text
Paging

↓

Page Table

↓

TLB

↓

RAM Still Limited

↓

Virtual Memory

↓

Demand Paging

↓

Page Fault

↓

Modern Operating Systems
```

# Swapping vs Virtual Memory

The **swap area (backing store)** that you learned in **OS06** is essentially the same concept used by **Virtual Memory**.

The difference is **what is being moved**.

## Swapping (Older Concept)

* Moves the **entire process** between RAM and disk.
* Flow:

```text
RAM
↕
Swap Area (Whole Process)
```

---

## Virtual Memory / Demand Paging (Modern Concept)

* Moves **individual pages** instead of the whole process.
* Flow:

```text
RAM
↕
Swap Area / Backing Store (Only Required Pages)
```

---

## Mental Model

### Older Operating Systems

```text
Whole Process
      ↕
Swap Area (Disk)
```

---

### Modern Operating Systems

```text
Individual Pages
      ↕
Swap Area / Backing Store (Disk)
```

---

## Key Difference

Modern operating systems prefer **paging** over **swapping** because moving a few required pages is much faster and more efficient than moving an entire process.

So, **Virtual Memory** can be viewed as an evolution of **Swapping**, where the Operating System transfers only the required pages instead of the whole process.

# Part 5 - Page Replacement Algorithms

---

# 1. Why Is Page Replacement Needed?

In Part 4 we learned:

```text
Program

↓

Pages

↓

Only Required Pages

Loaded Into RAM
```

Everything works perfectly.

But a new question appears.

Suppose RAM is already full.

Now the CPU requests another page.

Question:

```text
Where Should The New Page Go?
```

RAM has no free frame.

The OS cannot simply ignore the request.

It must somehow create space.

---

# 2. What Happens When RAM Becomes Full?

Suppose RAM contains:

```text
Frame 0

↓

Page A

------------------

Frame 1

↓

Page B

------------------

Frame 2

↓

Page C

------------------

Frame 3

↓

Page D
```

All frames are occupied.

Now the CPU requests:

```text
Page E
```

Question:

Can the OS place Page E anywhere?

```text
No.
```

Because every frame is already occupied.

The Operating System must first remove one existing page.

Only then can it load the new page.

---

# 3. What Is Page Replacement?

The Operating System chooses

one page already present in RAM,

removes it,

and loads the required page into that frame.

This process is called:

```text
Page Replacement
```

Flow:

```text
RAM Full

↓

CPU Requests New Page

↓

Choose Victim Page

↓

Remove Victim

↓

Load Required Page

↓

Update Page Table

↓

Continue Execution
```

---

# Important Question

Question:

How does the OS decide

which page to remove?

This is exactly why

Page Replacement Algorithms

were invented.

Their only job is:

```text
Choose

The Best Victim Page.
```

---

# 4. FIFO Page Replacement

FIFO means:

```text
First In

First Out
```

The oldest page

is removed first.

Exactly like a queue.

---

## Real Life Analogy

Imagine people standing in a queue.

```text
A

↓

B

↓

C

↓

D
```

If another person arrives,

who leaves first?

```text
A
```

The first person who entered.

FIFO works exactly the same way.

---

# Example

RAM has 3 frames.

Initially:

```text
Empty

Empty

Empty
```

Reference String:

```text
1 2 3 4
```

---

Request:

```text
1
```

RAM:

```text
1

-

-
```

---

Request:

```text
2
```

RAM:

```text
1

2

-
```

---

Request:

```text
3
```

RAM:

```text
1

2

3
```

RAM is now full.

---

Request:

```text
4
```

FIFO removes:

```text
1
```

because it entered first.

RAM becomes:

```text
4

2

3
```

---

# Advantages

```text
Very Simple

Easy To Implement

Uses Queue
```

---

# Disadvantages

FIFO does not care

whether a page is

still heavily used.

It only cares

which page entered first.

Sometimes,

it removes

the most useful page.

---

# 5. Belady's Anomaly

Normally, we expect:

```text
More RAM Frames

↓

More Pages Can Stay In RAM

↓

Fewer Page Faults
```

This is what happens in **most** page replacement algorithms.

---

## The Strange Case

With **FIFO**, something unexpected can happen.

```text
More Frames

↓

More Page Faults
```

This surprising behavior is called:

```text
Belady's Anomaly
```

---

## Why Does It Happen?

FIFO has one simple rule:

```text
Remove

The Oldest Page.
```

It never asks:

```text
Is This Page

Still Being Used?
```

or

```text
Will This Page

Be Needed Again Soon?
```

It only remembers:

```text
Who Entered First.
```

---

## Simple Intuition

Suppose RAM has:

```text
3 Frames
```

A useful page becomes the **oldest** page.

FIFO removes it.

Later,

the CPU needs that page again.

Result:

```text
Page Fault
```

Now suppose RAM has:

```text
4 Frames
```

You might think this will always help.

But adding one extra frame changes the **order** in which pages become the oldest.

Because FIFO only follows arrival order,

it may now remove a different page—

one that the CPU needs very soon.

This can create **even more page faults** than before.

---

## Important Point

Belady's Anomaly does **not** happen because having more memory is bad.

It happens because **FIFO makes poor replacement decisions**.

FIFO only considers:

```text
Arrival Order
```

It ignores:

```text
Recent Usage

Future Usage
```

---

## Interview Questions

### What Is Belady's Anomaly?

```text
A Situation Where

Increasing The Number Of Frames

Results In

More Page Faults.
```

---

### Which Algorithm Suffers From Belady's Anomaly?

```text
FIFO
```


# 6. Interview Questions

### Why Is Page Replacement Needed?

```text
Because RAM

Has Limited Frames.
```

---

### What Is A Victim Page?

```text
The Page

Chosen To Be Removed

From RAM.
```

---

### FIFO Stands For?

```text
First In

First Out
```

---

### Which Page Is Removed?

```text
The Oldest Page.
```

---

### Which Algorithm Shows Belady's Anomaly?

```text
FIFO
```

---

# 7. Optimal Page Replacement

FIFO was simple.

But engineers asked:

> Can we design the **best possible** page replacement algorithm?

The answer is:

```text
Yes
```

It is called:

```text
Optimal Page Replacement

(OPT)
```

---

## What Is Optimal Page Replacement?

Whenever RAM is full,

the Operating System removes:

```text
The Page

That Will Not Be Used

For The Longest Time

In The Future.
```

Notice the important word:

```text
Future
```

Unlike FIFO,

Optimal actually knows

future memory accesses.

---

## Example

Suppose RAM contains:

```text
1

2

3
```

Future page requests are:

```text
2

1

4

2

1

3
```

Question:

Which page should be removed?

Look ahead.

```text
Page 1

↓

Used Soon

------------------

Page 2

↓

Used Soon

------------------

Page 3

↓

Used Much Later
```

Therefore,

remove:

```text
Page 3
```

This causes the

fewest possible

page faults.

---

## Why Isn't It Used?

Question:

Can an Operating System

know the future?

```text
No.
```

The OS cannot predict

exactly which page

the CPU will request next.

Therefore,

Optimal Page Replacement

cannot be implemented

in a real operating system.

It is mainly used:

```text
For Comparison

And Examination Problems.
```

---

## Why Study It?

Because it gives

the minimum possible

number of page faults.

Every other algorithm

is compared against it.

---

## Advantages

```text
Minimum Page Faults

Best Possible Performance
```

---

## Disadvantages

```text
Needs Future Knowledge

Impossible To Implement
```

---

# 8. LRU (Least Recently Used)

Since the future

cannot be predicted,

engineers thought:

> What if we use the **past** instead?

This idea led to:

```text
Least Recently Used

(LRU)
```

---

## Core Idea

Pages that

have not been used

for a long time

are less likely

to be needed soon.

Therefore,

remove:

```text
The Least Recently Used Page.
```

---

## Example

RAM contains:

```text
1

2

3
```

Recent accesses:

```text
1

3

1

2
```

Question:

Which page

was used least recently?

```text
Page 3
```

Remove:

```text
Page 3
```

---

## Why Does LRU Work Well?

Most programs show:

```text
Locality Of Reference
```

Meaning:

If a page

was used recently,

there is a high chance

it will be used again soon.

LRU uses this observation.

---

## Real-Life Analogy

Imagine reading a book.

The page you read

one minute ago

is more likely

to be opened again

than the page

you read yesterday.

Exactly the same idea.

---

## Advantages

```text
Much Better Than FIFO

Uses Recent Usage

No Belady's Anomaly
```

---

## Disadvantages

The OS must remember

when every page

was last used.

Maintaining this information

creates extra overhead.

---

# 9. FIFO vs Optimal vs LRU

| Algorithm | Removes | Practical? | Belady's Anomaly |
|-----------|---------|------------|------------------|
| FIFO | Oldest Page | Yes | Yes |
| Optimal | Page Used Farthest In Future | No | No |
| LRU | Least Recently Used Page | Yes | No |

---

# Interview Questions

### Which Algorithm Gives The Minimum Page Faults?

```text
Optimal
```

---

### Why Isn't Optimal Used?

```text
Future Memory Accesses

Cannot Be Known.
```

---

### Which Algorithm Is Most Common In Theory?

```text
LRU
```

---

### Which Algorithm Uses Past Behaviour?

```text
LRU
```

---

### Which Algorithm Suffers From Belady's Anomaly?

```text
FIFO
```

---

# Memory Trick

```text
FIFO

↓

Oldest Page

------------------

Optimal

↓

Future

------------------

LRU

↓

Past
```

# 12. LFU (Least Frequently Used)

FIFO looked at:

```text
Arrival Order
```

LRU looked at:

```text
Recent Usage
```

Engineers then asked:

> What if we remove the page that is used the least?

This idea became:

```text
Least Frequently Used

(LFU)
```

---

## Core Idea

Every page keeps a counter.

Whenever the CPU accesses a page,

its counter increases.

Example:

```text
Page

1 → Used 10 Times

2 → Used 3 Times

3 → Used 1 Time
```

When RAM becomes full,

remove:

```text
Page 3
```

because it has been used the least.

---

## Why Does It Work?

The idea is simple.

Pages used many times

are likely to be important.

Pages rarely used

are better candidates

for replacement.

---

## Problem

Suppose:

```text
Page A

Used 100 Times
```

long ago.

Now it is never used again.

LFU still believes

it is important

because its counter

is very high.

So LFU may keep

an old useless page.

---

## Advantages

```text
Uses Access Frequency

Good For Frequently Used Data
```

---

## Disadvantages

```text
Needs Counters

Extra Memory

Old Pages May Stay Forever
```

---

# 13. Second Chance (Clock) Algorithm

LRU performs well,

but remembering

the exact last access time

for every page

is expensive.

Engineers wanted

something simpler.

This led to:

```text
Second Chance

(Clock Algorithm)
```

---

## Core Idea

Every page gets

one extra bit:

```text
Reference Bit

(0 or 1)
```

Initially:

```text
0
```

Whenever the page

is accessed,

hardware sets it to:

```text
1
```

---

## What Happens During Replacement?

Suppose a page

is selected.

Question:

Is its reference bit:

```text
0 ?
```

If yes,

remove it.

---

If the bit is:

```text
1
```

don't remove it.

Instead:

```text
Give It

One More Chance
```

The OS changes:

```text
1

↓

0
```

and checks

the next page.

---

## Why Is It Called Clock?

Imagine all pages

arranged in a circle.

A pointer moves

like the hand

of a clock.

```text
Page 1

↓

Page 2

↓

Page 3

↓

Page 4

↓

Back To Page 1
```

Whenever replacement

is needed,

the pointer rotates

until it finds

a page

whose reference bit

is:

```text
0
```

That page

is replaced.

---

## Why Is It Better Than LRU?

Clock approximates LRU,

but does not need

to store exact timestamps.

Therefore,

it is simpler

and faster.

---

## Advantages

```text
Simple

Efficient

Approximation Of LRU
```

---

## Disadvantages

```text
Not As Accurate

As True LRU
```

---

# 14. Frame Allocation

Question:

Suppose RAM has:

```text
100 Frames
```

Three processes start.

Question:

How many frames

should each process receive?

This decision is called:

```text
Frame Allocation
```

---

## Equal Allocation

Every process receives

the same number

of frames.

Example:

```text
90 Frames

3 Processes

↓

30 Frames Each
```

Simple.

Fair.

---

## Proportional Allocation

Larger processes

receive

more frames.

Example:

```text
Process A

100 Pages

↓

60 Frames

------------------

Process B

50 Pages

↓

30 Frames
```

Allocation depends

on process size.

---

## Global Replacement

All frames

belong to

everyone.

If Process A

needs one frame,

it may replace

a page belonging

to Process B.

---

## Local Replacement

Each process

uses only

its own frames.

If Process A

needs a page,

it can replace

only one of

its own pages.

It cannot

replace

Process B's pages.

---

# 15. Thrashing

Suppose a process

has very few frames.

It continuously executes:

```text
Page Fault

↓

Load Page

↓

Replace Page

↓

Page Fault Again

↓

Replace Again
```

Almost every instruction

causes

another page fault.

The CPU spends

most of its time

waiting for disk I/O

instead of

executing instructions.

This situation is called:

```text
Thrashing
```

---

## Why Does Thrashing Happen?

Main reason:

```text
Too Few Frames

For The Working Set
```

The process

cannot keep

its frequently used pages

inside RAM.

Pages keep

entering

and leaving.

---

## Solution

```text
Give More Frames

Or

Reduce Multiprogramming
```

---

# 16. Complete Algorithm Comparison

| Algorithm | Removes | Practical | Belady's Anomaly |
|-----------|---------|-----------|------------------|
| FIFO | Oldest Page | Yes | Yes |
| Optimal | Future Farthest Page | No | No |
| LRU | Least Recently Used | Yes | No |
| LFU | Least Frequently Used | Sometimes | No |
| Clock | Approximate LRU | Yes | No |

---

# 17. Final Interview Summary

## Evolution

```text
Contiguous Allocation

↓

Paging

↓

Page Table

↓

TLB

↓

Virtual Memory

↓

Demand Paging

↓

Page Fault

↓

Page Replacement

↓

FIFO

↓

Optimal

↓

LRU

↓

LFU

↓

Clock

↓

Frame Allocation

↓

Thrashing
```

---

## Memory Tricks

```text
FIFO

↓

Oldest

------------------

Optimal

↓

Future

------------------

LRU

↓

Past

------------------

LFU

↓

Least Used

------------------

Clock

↓

Reference Bit

------------------

Thrashing

↓

Too Many Page Faults
```

---

# OS07 Complete

Congratulations!

You have now covered:

```text
✓ Paging

✓ Pages & Frames

✓ Page Tables

✓ MMU Translation

✓ TLB

✓ Virtual Memory

✓ Demand Paging

✓ Page Faults

✓ Page Replacement

✓ FIFO

✓ Belady's Anomaly

✓ Optimal

✓ LRU

✓ LFU

✓ Clock Algorithm

✓ Frame Allocation

✓ Thrashing
```

This completes the **Memory Management** portion of Operating Systems and covers the standard interview syllabus from fundamentals through advanced concepts.
