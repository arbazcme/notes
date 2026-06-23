# Process States, PCB, Memory Layout, Stack, Heap, Suspend States (Detailed Notes)

---

# 1. What is a Process?

A Process is simply:

> A program that is currently executing.

Example:

```c
int main()
{
    printf("Hello");
}
```

When stored on disk:

```text
hello.exe
```

It is just a program.

When loaded into RAM and executed:

```text
Process
```

Now the OS must keep track of:

- Which instruction is running?
- Which memory belongs to it?
- What files are opened?
- Is it waiting for I/O?
- Is it ready to execute?

For this purpose OS creates:

```text
PCB (Process Control Block)
```

---

# 2. PCB (Process Control Block)

PCB is the "identity card" of a process.

Contains everything OS needs to manage a process.

```text
PCB
│
├── Process ID (PID)
├── Process State
├── Program Counter
├── CPU Registers
├── Scheduling Info
├── Memory Information
├── Open Files
├── I/O Status
└── Accounting Information
```

## Process Control Block (PCB) Storage

The **PCB** is stored in **kernel space** inside the physical RAM. 

### Physical RAM Layout

* **User Space:** The area of RAM where your everyday apps (like browsers or games) run.
* **Kernel Space:** The area of RAM reserved exclusively for the Operating System core.

> **Key Takeaway:** Because the PCB lives in kernel space, **only the OS can access it**! This keeps critical process data safe from user applications.

### When the OS Accesses the PCB

The OS switches to kernel mode and accesses the PCB during these critical operations:

* **Context Switching:** Saving old process data and loading the next process.
* **State Changes:** Moving a process between Running, Ready, or Waiting states.
* **CPU Scheduling:** Checking process priorities to choose what runs next.
* **Resource Management:** Tracking open files, memory limits, and I/O devices.

---

# 3. Process Number (PID)

Every process gets a unique number.

Example:

```text
Chrome      PID = 101
VS Code     PID = 205
Spotify     PID = 350
```

OS uses PID to identify processes.

---

# 4. Process State

Shows current condition of process.

Possible states:

```text
New
Ready
Running
Waiting/Blocked
Suspended Ready
Suspended Waiting
Terminated
```

We will discuss these later in detail.

---

# 5. Program Counter (PC)

Most important concept.

Stores:

```text
Address of next instruction to execute
```

Example:

```c
1  int a=5;
2  int b=10;
3  int c=a+b;
4  printf("%d",c);
```

Suppose CPU completed line 2.

Then:

```text
PC -> Address of line 3
```

Meaning:

```text
Next instruction = line 3
```

---

# Why Store PC?

Suppose process gets interrupted.

CPU starts executing another process.

Later process resumes.

How does CPU know where to continue?

Answer:

```text
Program Counter
```

OS saved it in PCB.

---

# 6. Registers in PCB

CPU contains registers:

```text
AX
BX
CX
DX
SP
PC
etc.
```

During context switch:

```text
CPU Registers
        ↓
Saved into PCB
```

Later:

```text
PCB
        ↓
Restored into CPU
```

Process continues exactly where it stopped.

The CPU registers of the currently executing entity are saved during a context switch. In a single-threaded process this context may be stored in the PCB, while in a multithreaded process the register values are typically stored in the corresponding TCB because each thread has its own execution state.

---

# 7. Memory Information in PCB

OS stores:

```text
Base Address
Limit Address
Page Table
Segment Table
```

Used to identify process memory.

---

# 8. Open File List

Example:

Chrome opens:

```text
image.jpg
video.mp4
cookies.db
```

PCB stores references to these files.

---

# 9. Process Memory Layout

When process enters RAM:

```text
High Address
────────────────
Stack
↓
↓
Free Space
↑
↑
Heap
────────────────
Data
────────────────
Text(Code)
────────────────
Low Address
```

---

# 10. Text Segment

Contains:

```text
Compiled machine instructions
```

Example:

```c
printf("Hello");
```

Compiler converts to machine code.

Stored in:

```text
Text Segment
```

Usually read-only.

---

# 11. Data Segment

Stores:

```c
int x = 10;
```

Global variables.

Static variables.

Example:

```c
int count = 5;

int main()
{
}
```

Stored in:

```text
Data Segment
```

---

# 12. Heap Segment

Used for:

```text
Dynamic Memory Allocation
```

Example:

```c
int *p = malloc(100);
```

Memory comes from:

```text
Heap
```

Heap grows upward.

```text
Heap
↑
↑
↑
```

---

# Why Heap?

Size not known beforehand.

Example:

```c
Enter number of students:
```

Could be:

```text
100
1000
10000
```

Need memory at runtime.

Use heap.

---

# Heap Characteristics

```text
Allocated manually
Freed manually
Large memory
Slower than stack
```

Example:

```c
free(p);
```

Must free memory.

Otherwise:

```text
Memory Leak
```

### Why Stack is Fast 🚀
- Allocation/deallocation only moves the Stack Pointer (SP).
- No searching for free memory blocks is required.
- Data is usually contiguous, giving good CPU cache locality.
- Very little memory-management overhead.

### Why Heap is Slow 🐢
- Allocator must search for a suitable free block.
- Metadata and free-space bookkeeping must be maintained.
- Fragmentation can occur over time.
- Heap objects may be scattered in memory, causing more cache misses.

---

# 13. Stack Segment

Stores:

```text
Function calls
Local variables
Return addresses
Parameters
```

Stack grows downward.

```text
Stack
↓
↓
↓
```

---

# Example

```c
int foo(int a,int b)
{
    int x;
    int y;
}

int main()
{
    foo(10,20);
}
```

---

# Before Calling foo()

Stack:

```text
main()
```

---

# During foo()

Stack:

```text
foo()
main()
```

---

# Inside foo()

Stored:

```text
a
b
x
y
Return Address
```

---

# After foo() Returns

Stack frame removed.

```text
main()
```

Only main remains.

---

# Return Address

Very important.

When function call occurs:

```c
foo();
```

CPU stores:

```text
Where to return after foo()
```

inside stack.

Example:

```c
1 foo();
2 printf("Hello");
```

Return address points to:

```text
Line 2
```

After foo completes:

```text
Execution resumes at line 2
```

---

# Stack Pointer (SP)

Points to current top of stack.

Example:

```text
Stack
│
│
│ <- SP
```

Whenever push/pop occurs:

```text
SP changes
```

---

# Heap vs Stack

| Stack | Heap |
|---------|---------|
| Automatic | Manual |
| Fast | Slower |
| Small | Large |
| Function Calls | Dynamic Memory |
| Managed by OS/Compiler | Managed by Programmer |


So stack memory is used for storing thread execution-related data i.e.

```text
Local Variables
Function Calls
Parameters
Return Addresses
```

(PC and Registers are also part of thread execution context, but are not stored inside the stack.)

---

There is execution context private to threads, right?

That is:

```text
PC
Registers
Stack
```

Shared memory is:

```text
Heap
Globals
Code
```

Stack memory is mainly for execution of program:

```text
Variables
Functions
Function Calls
```


## Stack vs Heap (Simple Understanding)

### Stack

Stack is a region of RAM that is private to each thread.

A thread's execution context consists of:

```text
PC
Registers
Stack
```

The stack stores execution-related state such as:

```text
Local Variables
Function Calls
Parameters
Return Addresses
```

Since stack memory is directly used by the thread and allocation/deallocation is just moving the stack pointer, it is very fast.

Since every thread gets its own stack, it is usually small.

---

### Heap

Heap is another region of RAM that belongs to the process and is shared by all its threads.

It is mainly used for storing program data such as:

```text
Arrays
Vectors
Objects
Dynamic Memory
```

Since memory can be allocated and freed at arbitrary locations, the allocator must manage free and used blocks.

This extra management makes heap slower than stack.

Heap is generally much larger than stack.

---

### Quick Summary

```text
Thread Private:
PC + Registers + Stack

Process Shared:
Heap + Globals + Code

Stack:
Stores execution state
Fast
Small

Heap:
Stores program data
Managed allocations
Large
Shared
```

---

# 14. Process State Diagram

![Cute Puppy](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQDnv1mSnhGLvFjPvikD_k8ymGcJ9MLFJ_fJnt036GQYXMJE1H0N0IrXjI&s=10)

States:

```text
New
Ready
Running
Waiting
Terminated
Suspended Ready
Suspended Waiting
```

---

# 15. NEW State

Process just created.

Example:

```text
Double-click Chrome
```

OS:

```text
Creates PCB
Allocates resources
```

State:

```text
NEW
```

---

# 16. READY State

Process is ready to run.

Everything loaded.

Only waiting for CPU.

```text
Ready Queue
```

Example:

```text
Chrome
VS Code
Spotify
```

all waiting.

CPU can run only one.

Others remain:

```text
READY
```

---

# 17. RUNNING State

CPU currently executing process.

```text
Running
```

Only one process per CPU core.

Example:

```text
CPU → Chrome
```

Chrome is:

```text
RUNNING
```

---

# 18. READY → RUNNING

Scheduler selects process.

```text
Ready Queue
      ↓
CPU
```

State changes:

```text
READY
      ↓
RUNNING
```

---

# 19. RUNNING → READY

Time quantum expires.

Example:

```text
Chrome got 10ms
```

10ms completed.

OS performs:

```text
Context Switch
```

State:

```text
RUNNING
      ↓
READY
```

Another process gets CPU.

---

# 20. WAIT/BLOCK State

Process needs something.

Usually:

```text
Disk I/O
Keyboard Input
Network
Mouse Input
```

Example:

```c
scanf("%d",&x);
```

Program waiting for user.

State:

```text
WAIT/BLOCK
```

CPU cannot help.

So OS gives CPU to another process.

---

# 21. RUNNING → WAIT

Example:

```c
read(file);
```

Disk access required.

State:

```text
RUNNING
      ↓
WAIT
```

---

# 22. WAIT → READY

I/O completed.

Example:

```text
File finished loading
```

State:

```text
WAIT
      ↓
READY
```

Now process can execute again.

---

# 23. TERMINATED

Process finished.

Example:

```c
return 0;
```

OS:

```text
Releases memory
Deletes PCB
Closes files
```

State:

```text
TERMINATED
```

---

# 24. Why Suspend States Exist?

RAM is limited.

Suppose:

```text
RAM = 8 GB
Running processes require = 12 GB
```

OS must move some processes out.

Process memory copied to disk.

Called:

```text
Swapping
```

---

# 25. Suspended Ready State

Definition:

```text
Ready to run
BUT
not present in RAM
```

Stored on disk.

---

# Internal Working

Before suspension:

```text
Process in RAM
Ready Queue
```

OS decides:

```text
Need RAM
```

OS performs:

```text
RAM → Disk
```

State becomes:

```text
SUSPENDED READY
```

---

# Important

Process is NOT waiting for I/O.

It is ready.

Only problem:

```text
Not in RAM
```

---

# Suspended Ready Example

You open:

```text
20 Chrome tabs
VS Code
Photoshop
Game
```

RAM becomes full.

OS moves inactive Chrome tab process to disk.

State:

```text
SUSPENDED READY
```

---

# Transition

```text
READY
   ↓
SUSPENDED READY
```

when swapped out.

---

# Returning

When RAM available:

```text
Disk → RAM
```

State:

```text
SUSPENDED READY
       ↓
READY
```

---

# 26. Suspended Wait State

Definition:

```text
Waiting for event
AND
not in RAM
```

Two conditions:

```text
Waiting
+
Swapped Out
```

---

# Example

Process waiting for:

```text
Disk Read
```

Current state:

```text
WAIT
```

RAM becomes scarce.

OS swaps process to disk.

State becomes:

```text
SUSPENDED WAIT
```

---

# Internal Working

```text
WAIT
 ↓
Swapped Out
 ↓
SUSPENDED WAIT
```

---

# Example Scenario

Process requests:

```c
read(bigfile);
```

Disk takes long time.

OS notices process won't run soon.

Moves it out of RAM.

Result:

```text
SUSPENDED WAIT
```

---

# What Happens If I/O Finishes?

Interesting case.

Suppose:

```text
SUSPENDED WAIT
```

Disk operation completes.

Now process is no longer waiting.

But still on disk.

Therefore:

```text
SUSPENDED WAIT
        ↓
SUSPENDED READY
```

because:

```text
I/O done
Still swapped out
```

---

# Complete Suspend Flow

```text
READY
  ↓
SUSPENDED READY
  ↓
READY

WAIT
 ↓
SUSPENDED WAIT
 ↓
SUSPENDED READY
 ↓
READY
 ↓
RUNNING
```

---

# 27. Context Switching

Suppose:

```text
Chrome Running
```

Timer expires.

OS saves:

```text
PC
Registers
SP
```

into PCB.

Then loads:

```text
PC
Registers
SP
```

from another PCB.

CPU continues new process.

This is:

```text
Context Switch
```

---

# 28. Relation Between PCB and States

Every time state changes:

```text
Process State field in PCB changes
```

Example:

```text
PCB
│
├── PID = 100
├── State = WAIT
├── PC = 0xA342
└── Registers
```

Later:

```text
PCB
│
├── PID = 100
├── State = READY
├── PC = 0xA342
└── Registers
```

Only state changed.

---

# Final Intuition

Think of a process as a package:

```text
Process
│
├── Text (Code)
├── Data
├── Heap
├── Stack
└── PCB
```

PCB is the process's identity card.

Stack stores function execution history.

Heap stores dynamic memory.

Program Counter stores next instruction.

Ready = waiting for CPU.

Running = using CPU.

Wait = waiting for event.

Suspended Ready = ready but swapped to disk.

Suspended Wait = waiting and swapped to disk.

Terminated = finished.


# OS 01 Appendix - Common Interview Doubts and Clarifications

---

# Q1. Does the Program Counter belong to PCB or TCB?

Short Answer:

```text
Single-threaded Process:
    PCB can store PC.

Multithreaded Process:
    Each Thread has its own PC.
```

Modern operating systems generally associate:

```text
Program Counter
CPU Registers
Stack Pointer
Thread State
```

with the:

```text
TCB (Thread Control Block)
```

because execution is performed by threads.

---

# Why?

Suppose a process contains:

```text
Process
│
├── Thread 1
├── Thread 2
└── Thread 3
```

Each thread may be executing different instructions.

Example:

```text
Thread 1 -> Line 50
Thread 2 -> Line 120
Thread 3 -> Line 80
```

A single Program Counter for the whole process would be impossible.

Therefore:

```text
Each Thread
      ↓
Own PC
```

---

# Interview Answer

"What does a Program Counter store?"

Answer:

```text
The memory address of the next instruction to execute.
In multithreaded systems, each thread maintains its own Program Counter.
```

---

# Q2. Does Context Switching happen between Processes or Threads?

Answer:

```text
Both.
```

---

# Process Context Switch

Heap is for dedicated/dynamic storing:

```text
Arrays
Objects
Dynamic Memory
```

Everything remains in RAM.

---

When threads of Process A execute,

its shared memory (Heap, Globals, etc.) may be loaded into:

```text
CPU Cache
(L1, L2, L3)
```

for faster access.

---

When Process B's Thread comes,

PCB gets updated with the current execution state of Process A:

```text
PC
Registers
Process State
Scheduling Information
```

The shared memory of Process A is NOT copied into PCB.

It remains in RAM.

---

Then Process B starts executing.

As Process B accesses its Heap/Globals,

that data naturally gets loaded into CPU Cache.

Old cache entries from Process A may be removed/replaced automatically if space is needed.

---

So the correct understanding is:

```text
Process A Heap
    ↓
Remains in RAM

PCB
    ↓
Updated with execution state

Process B Heap
    ↓
Naturally loaded into CPU Cache as Process B executes
```

No Heap copying.

No Global copying.

No RAM copying.

Only:

```text
PCB updated
CPU switches execution
Cache naturally updates based on memory access
```


Q3. When a Process is Suspended, Where is it Stored?
```

Answer:

```text
Secondary Storage (Disk / Swap Area)
```

OS copies the process memory image from RAM to disk.

This operation is called:

```text
Swapping
```

---

# What is Swap Space?

Special disk area reserved by OS.

Example:

```text
RAM Full
     ↓
Move Process Memory
     ↓
Swap Area
```

---

# Q4. Is the Suspended Process Converted Back Into a Program?

Answer:

```text
No.
```

Very important interview point.

A program is:

```text
Static
Stored on Disk
Not Executing
```

A suspended process is:

```text
Still a process
Still has state
Still has PCB
Execution can resume
```

The OS has simply moved its memory image to disk.

---

# Program vs Suspended Process

Program:

```text
No PCB
No State
No CPU Context
```

Suspended Process:

```text
Has PCB
Has State
Has Saved Context
Can Resume Execution
```

---

# Q5. What Exactly Gets Moved to Disk?

Usually:

```text
Code Segment
Data Segment
Heap
Stack
```

are moved.

These form the process memory image.

---

# What Usually Stays in RAM?

The OS typically keeps:

```text
PCB
Scheduling Information
Kernel Metadata
```

in kernel memory.

Because OS must still track the process.

Think:

```text
Memory Contents -> Disk

Process Identity -> PCB remains known to OS
```

---

# Q6. If Process is on Disk, How Can OS Find It Again?

Because PCB still exists.

PCB stores information like:

```text
PID
Process State
Location of Swapped Data
Scheduling Info
```

When RAM becomes available:

```text
Disk
   ↓
RAM
```

Process memory is restored.

State changes:

```text
Suspended Ready
        ↓
Ready
```

---

# Example

Process:

```text
Process
│
├── Thread 1 Stack
├── Thread 2 Stack
└── Thread 3 Stack
```

Shared:

```text
Code
Heap
Global Variables
```

Private:

```text
PC
Registers
Stack
```

---

# Interview Question

What is shared between threads?

Answer:

```text
Code Segment
Data Segment
Heap
Open Files
```

What is private?

```text
Program Counter
Registers
Stack
Thread State
```

---

# Quick Revision

Process owns:

```text
Code
Data
Heap
Open Files
```

Thread owns:

```text
Program Counter
Registers
Stack
Thread State
```

PCB stores:

```text
Process Information
Scheduling Information
Memory Information
```

TCB stores:

```text
PC
Registers
Stack Information
Thread State
```

Suspended Process:

```text
Still a Process
Not a Program
Memory moved to disk
PCB remains tracked by OS
```


# Q7. Where are Threads Stored, Thread Data Storage (TCB, Registers, PC, and Stack)

Just like the PCB, thread control data is stored in **kernel space** to protect it from unauthorized modification by user applications.

## Component Breakdown

* **Thread Control Block (TCB):** Stored in **Kernel Space**. This is the thread equivalent of a PCB, used by the OS scheduler to manage thread execution.
* **Program Counter (PC) & CPU Registers:** 
  * **When Running:** Stored directly inside the **Physical CPU hardware**.
  * **When Stopped:** Saved into the **TCB (Kernel Space)** so the thread can resume exactly where it left off.
* **Stack Pointer (SP):** The pointer *address* is stored in the **TCB (Kernel Space)**. This address points to the thread's actual stacks:
  * **User Stack:** Located in **User Space** to handle standard application function calls.
  * **Kernel Stack:** Located in **Kernel Space** to handle system calls and OS tasks.

> **Key Takeaway:** The OS maintains strict control over thread management by keeping all TCBs, saved registers, and execution states safely isolated inside **kernel space RAM**.
