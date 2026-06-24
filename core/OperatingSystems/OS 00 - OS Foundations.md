# OS 00 - OS Foundations


## Navigation

### OS Basics
- [1. What Is An Operating System?](#1-what-is-an-operating-system)
- [2. Why Do We Need An Operating System?](#2-why-do-we-need-an-operating-system)
- [3. Goals Of An Operating System](#3-goals-of-an-operating-system)

### OS Protection & Execution
- [4. User Mode vs Kernel Mode](#4-user-mode-vs-kernel-mode)
- [5. System Calls](#5-system-calls)

### Interrupts
- [6. What Is An Interrupt?](#6-what-is-an-interrupt)
- [7. Hardware Interrupts](#7-hardware-interrupts)
- [8. Software Interrupts](#8-software-interrupts)
- [9. Exceptions And Traps](#9-exceptions-and-traps)
- [10. Interrupt Handling (ISR)](#10-interrupt-handling-isr)

### System Startup
- [11. Booting Process](#11-booting-process)

### OS Architecture
- [12. Monolithic Kernel](#12-monolithic-kernel)
- [13. Microkernel](#13-microkernel)

### OS Types
- [14. Types Of Operating Systems](#14-types-of-operating-systems)

### Revision
- [15. Common Interview Questions](#15-common-interview-questions)
- [16. Quick Revision Sheet](#16-quick-revision-sheet)

---
# 1. What Is An Operating System?

Operating System (OS) is system software that acts as an intermediary between:

```text
User
  ↕
Applications
  ↕
Operating System
  ↕
Hardware
```

---

# Without OS

Suppose a program wants:

```text
Read Keyboard

Display Pixels

Access Disk

Allocate Memory
```

It would need to directly control hardware.

Very difficult.

---

# With OS

Application simply requests:

```text
Open File

Read Input

Create Process

Allocate Memory
```

OS performs hardware operations.

---

# Definition

```text
Operating System is a resource manager and control program
that manages hardware resources and provides services
to applications.
```

---

# Examples

```text
Windows

Linux

macOS

Android

iOS
```

---

# 2. Why Do We Need An Operating System?

Without OS:

```text
Every Program
Must Control Hardware Directly
```

Impossible at large scale.

---

# OS Provides

```text
Process Management

Memory Management

File Management

Device Management

Security

Scheduling
```

---

# Example

Suppose:

```text
Chrome
VS Code
Spotify
```

are running.

Questions:

```text
Who Gets CPU?

Who Gets RAM?

Who Gets Disk?
```

OS manages everything.

---

# Real Analogy

Think:

```text
Operating System
=
Manager Of A Company
```

Resources:

```text
CPU
RAM
Disk
Printer
```

Employees:

```text
Processes
```

Manager allocates resources.

---

# 3. Goals Of An Operating System

Main goals:

---

# Convenience

Make computer easy to use.

Without OS:

```text
Program Hardware Directly
```

With OS:

```text
Use Applications
```

---

# Efficiency

Use hardware efficiently.

Example:

```text
Keep CPU Busy

Manage Memory Properly

Avoid Resource Waste
```

---

# Fairness

Every process should get reasonable resources.

Avoid:

```text
One Process
Using Everything
```

---

# Protection

Prevent programs from damaging:

```text
Other Programs

OS

Hardware
```

---

# 4. User Mode vs Kernel Mode

One of the most important OS concepts.

---

# Problem

Suppose application executes:

```c
while(1)
{
}
```

or

```c
deleteEntireDisk();
```

Should application have full hardware access?

```text
No
```

---

# Solution

CPU provides:

```text
User Mode

Kernel Mode
```

---

# User Mode

Applications run here.

Examples:

```text
Chrome

VS Code

Spotify

Games
```

Restrictions:

```text
Cannot Access Hardware Directly

Cannot Access Kernel Memory

Cannot Execute Privileged Instructions
```

---

# Kernel Mode

Operating System runs here.

Privileges:

```text
Full Hardware Access

Full Memory Access

Can Execute Privileged Instructions
```

---

# Diagram

```text
User Application
       ↓
System Call
       ↓
Kernel Mode
       ↓
OS Performs Work
       ↓
Return To User Mode
```

---

# Why Is This Important?

Protection.

If application crashes:

```text
Application Dies
```

not:

```text
Entire Operating System
```

---

# 5. System Calls

Applications cannot directly access hardware.

Therefore they ask OS for help.

This request is called:

```text
System Call
```

---

# Example

Application:

```c
fopen("data.txt");
```

Internally:

```text
System Call
```

occurs.

OS opens file.

---

# Examples Of System Calls

Process:

```text
fork()

exec()

wait()
```

File:

```text
open()

read()

write()

close()
```

Memory:

```text
malloc()

mmap()
```

---

# Flow

```text
Application
      ↓
System Call
      ↓
Kernel Mode
      ↓
OS Service
      ↓
Return Result
```

---

# 6. What Is An Interrupt?

Most important foundation before scheduling.

---

# Definition

An interrupt is a signal that temporarily stops current execution and transfers control to the operating system.

---

# Simple Meaning

CPU running:

```text
Process A
```

Suddenly:

```text
Keyboard Pressed
```

CPU must react.

Interrupt generated.

---

# Flow

```text
Running Process
      ↓
Interrupt Occurs
      ↓
Save Context
      ↓
Execute OS Handler
      ↓
Resume Execution
```

---

# Why Interrupts Exist?

Without interrupts:

```text
CPU Must Continuously Check

Keyboard
Disk
Network
Mouse
```

Wasteful.

Interrupts allow hardware to notify CPU.

---

# 7. Hardware Interrupts

Generated by hardware devices.

---

# Examples

```text
Keyboard Pressed

Mouse Clicked

Disk Finished Reading

Network Packet Arrived

Timer Expired
```

---

# Example

User presses:

```text
A
```

Keyboard sends interrupt.

OS receives control.

OS processes key press.

---

# Most Important Hardware Interrupt

```text
Timer Interrupt
```

Used for:

```text
CPU Scheduling
```

---

# Example

```text
P1 Running
```

Timer expires.

Interrupt occurs.

OS scheduler executes.

Possibly:

```text
P2 Runs
```

---

# 8. Software Interrupts

Generated by software.

---

# Example

Program wants:

```text
Open File
```

Cannot access disk directly.

Generates:

```text
Software Interrupt
```

to enter kernel mode.

---

# Purpose

Request operating system services.

---

# Easy Memory Trick

```text
Hardware Interrupt
=
Device Needs Attention

Software Interrupt
=
Program Needs OS Service
```

---

# 9. Exceptions And Traps

Generated by CPU itself.

---

# Example

Division By Zero

```c
10 / 0
```

---

# Example

Invalid Memory Access

```c
int *p = NULL;
*p = 5;
```

---

CPU detects error.

Generates exception.

OS gains control.

---

# Why Needed?

Prevent system corruption.

---

# Example

Bad program:

```text
Access Random Memory
```

OS:

```text
Terminates Program
```

instead.

---

# 10. Interrupt Handling (ISR)

ISR:

```text
Interrupt Service Routine
```

---

# What Is ISR?

Special OS function executed when interrupt occurs.

---

# Example

Keyboard Interrupt

```text
Interrupt Occurs
       ↓
Keyboard ISR Runs
       ↓
Stores Key
       ↓
Return
```

---

# Internal Flow

```text
Process Running
      ↓
Interrupt
      ↓
Save PC
Save Registers
      ↓
Run ISR
      ↓
Restore PC
Restore Registers
      ↓
Continue Execution
```

---

# Relation To Context Switching

Interrupts often trigger:

```text
Scheduling
```

Scheduling may trigger:

```text
Context Switching
```

---

# 11. Booting Process

How computer starts.

---

# Step 1

Power ON.

CPU starts executing firmware.

---

# Step 2

BIOS or UEFI starts.

Performs:

```text
Hardware Checks
```

called:

```text
POST
```

(Power-On Self-Test)

---

# Step 3

Bootloader loaded.

Examples:

```text
GRUB
Windows Boot Manager
```

---

# Step 4

Bootloader loads:

```text
Operating System Kernel
```

into memory.

---

# Step 5

Kernel initializes:

```text
Memory

Devices

Drivers

Schedulers
```

---

# Step 6

System becomes usable.

---

# Diagram

```text
Power On
    ↓
BIOS / UEFI
    ↓
Bootloader
    ↓
Kernel
    ↓
OS Running
```

---

# 12. Monolithic Kernel

All OS services run inside kernel.

---

# Diagram

```text
Kernel
│
├── Memory Manager
├── Scheduler
├── File System
├── Drivers
└── Networking
```

Everything together.

---

# Advantages

```text
Fast

Efficient
```

---

# Disadvantages

Bug in one component may affect entire kernel.

---

# Example

```text
Linux
```

---

# 13. Microkernel

Keep kernel small.

Move services outside kernel.

---

# Diagram

```text
Kernel
│
├── Scheduling
├── IPC
└── Basic Memory

User Space
│
├── Drivers
├── File System
└── Networking
```

---

# Advantages

```text
Safer

More Modular

More Reliable
```

---

# Disadvantages

```text
More Communication Overhead
```

---

# Example

```text
MINIX
QNX
```

---

# Easy Memory Trick

```text
Monolithic
=
Everything Inside

Microkernel
=
Minimal Kernel
```

---

# 14. Types Of Operating Systems

---

# 1. Batch Operating System

Oldest type of operating system.

Users submit jobs to the system.

OS collects multiple jobs into a batch and executes them one after another.

---

## Example

```text
Job 1 → Salary Calculation
Job 2 → Electricity Bill Generation
Job 3 → Report Generation
```

OS processes all jobs sequentially.

---

## Characteristics

```text
No User Interaction

Jobs Executed In Batches

High Turnaround Time
```

---

## Advantages

```text
Simple

Good For Repetitive Tasks

Can Handle Large Number Of Jobs
```

---

## Disadvantages

```text
Long Waiting Time

No Immediate Response

Difficult To Debug
```

---

# 2. Multiprogramming Operating System

Multiple processes are kept in memory at the same time.

When one process waits for I/O, CPU executes another process.

---

## Example

```text
Process A → Waiting For Disk

CPU Executes

Process B
```

instead of remaining idle.

---

## Goal

```text
Keep CPU Busy
```

---

## Characteristics

```text
Multiple Processes In RAM

CPU Rarely Idle

Better Resource Utilization
```

---

## Advantages

```text
Higher CPU Utilization

Better Throughput

Less CPU Wastage
```

---

## Disadvantages

```text
More Complex

Requires Scheduling

Requires Memory Management
```

---

# 3. Multitasking Operating System

Allows multiple applications to appear running simultaneously.

CPU rapidly switches between processes.

---

## Example

```text
Chrome

VS Code

Spotify

WhatsApp
```

running together.

---

## Internal Working

```text
Chrome
 ↓
VS Code
 ↓
Spotify
 ↓
Chrome
```

Very fast context switching.

---

## Goal

```text
Provide Responsive User Experience
```

---

## Characteristics

```text
Time Sharing

Fast Context Switching

Interactive System
```

---

## Advantages

```text
Responsive

User Can Run Multiple Apps

Efficient Resource Sharing
```

---

## Disadvantages

```text
Context Switching Overhead

More Complex Scheduling
```

---

# 4. Real-Time Operating System (RTOS)

Designed for systems where timing is critical.

Responses must occur within guaranteed deadlines.

---

## Example

```text
Aircraft Control System

Medical Equipment

Industrial Robots
```

---

## Goal

```text
Meet Deadlines
```

not maximize throughput.

---

## Characteristics

```text
Predictable Execution

Deterministic Behavior

Fast Response
```

---

## Advantages

```text
Reliable

Accurate Timing

Suitable For Critical Systems
```

---

## Disadvantages

```text
Complex Design

Expensive Development
```

---

# 5. Distributed Operating System

Multiple computers work together and appear as one system.

Resources are shared across machines.

---

## Example

```text
Computer A

Computer B

Computer C
```

working together.

User sees:

```text
One System
```

---

## Goal

```text
Resource Sharing

Scalability

Fault Tolerance
```

---

## Advantages

```text
High Performance

Resource Sharing

System Can Grow Easily
```

---

## Disadvantages

```text
Complex Management

Network Dependency
```

---

# 6. Network Operating System

Provides services over a network.

Focuses on communication and resource sharing.

---

## Example

```text
File Server

Print Server

Authentication Server
```

---

## Characteristics

```text
Network Communication

Remote Access

Shared Resources
```

---

## Advantages

```text
Easy Resource Sharing

Centralized Management
```

---

## Disadvantages

```text
Network Failure Affects Services

Security Challenges
```

#

# 15. Common Interview Questions

---

# Why User Mode And Kernel Mode?

```text
Protection And Security
```

---

# Why Are Interrupts Needed?

```text
Allow Hardware And Software
To Notify CPU Efficiently
```

---

# Difference Between Interrupt And System Call?

Interrupt:

```text
Can Be Hardware Generated
```

System Call:

```text
Explicitly Requested By Program
```

---

# Why Is Timer Interrupt Important?

```text
Enables Preemptive Scheduling
```

---

# What Is ISR?

```text
OS Routine That Handles Interrupts
```

---

# 16. Quick Revision Sheet

```text
OS
=
Resource Manager

User Mode
=
Restricted

Kernel Mode
=
Full Privileges

System Call
=
Program Requests OS Service

Interrupt
=
Stop Current Execution
And Run OS Handler

Hardware Interrupt
=
Device Generated

Software Interrupt
=
Program Generated

Exception
=
CPU Detected Error

ISR
=
Interrupt Handler

Timer Interrupt
=
Scheduling

Boot Process
=
BIOS → Bootloader → Kernel

Monolithic
=
Everything Inside Kernel

Microkernel
=
Minimal Kernel
```


# 13. Starvation

Important interview topic.

---

## What Is Starvation?

A process waits indefinitely because other processes continuously receive CPU.

---

## Example

Priority Scheduling:

```text
P1 Priority = 100
```

Low priority.

Continuously arriving:

```text
P2 Priority = 1
P3 Priority = 1
P4 Priority = 1
```

Scheduler always chooses:

```text
Higher Priority Process
```

P1 may never execute.

---

## Why Does It Happen?

Because scheduler keeps favoring some processes.

Other processes keep waiting.

---

## Algorithms Where Starvation Can Occur

```text
Priority Scheduling

SJF

SRTF
```

---

## Algorithms Where Starvation Is Rare

```text
Round Robin
```

because everyone gets CPU eventually.

---

## Interview Definition

```text
Starvation is a condition where a process waits
indefinitely because CPU keeps getting allocated
to other processes.
```

---

# 14. Aging

Solution to starvation.

---

## Idea

Increase priority of waiting processes over time.

Longer a process waits:

```text
Higher Its Priority Becomes
```

---

## Example

Initially:

```text
P1 Priority = 100
```

After waiting:

```text
Priority = 90
```

Later:

```text
Priority = 80
```

Eventually:

```text
Priority = 1
```

and process gets CPU.

---

## Why Is Aging Needed?

Without aging:

```text
Low Priority Process
May Never Execute
```

With aging:

```text
Every Process
Eventually Gets CPU
```

---

## Interview Definition

```text
Aging gradually increases the priority of waiting
processes to prevent starvation.
```

---

# 15. Multilevel Queue Scheduling

Processes are divided into multiple queues.

Each queue contains a different category of process.

---

## Example

```text
System Processes
        ↓
Interactive Processes
        ↓
Batch Processes
```

---

## Diagram

```text
Queue 1 → System
Queue 2 → Interactive
Queue 3 → Batch
```

Each queue can use its own scheduling algorithm.

---

## Example

```text
System Queue
=
Round Robin

Batch Queue
=
FCFS
```

---

## Between Queues

Queues themselves may have priorities.

Example:

```text
System Queue
Higher Priority

Batch Queue
Lower Priority
```

---

## Problem

Lower queues may starve.

---

## Advantages

```text
Simple Classification

Different Policies For Different Jobs
```

---

## Disadvantages

```text
Possible Starvation

Rigid Structure
```

---

# 16. Multilevel Feedback Queue Scheduling

One of the most advanced scheduling algorithms.

Frequently asked conceptually.

---

## Problem With Multilevel Queue

Processes remain permanently in one queue.

Bad.

---

## Solution

Allow movement between queues.

---

## Example

```text
Queue 1
High Priority

Queue 2
Medium Priority

Queue 3
Low Priority
```

Processes can move.

---

## Example Working

New process enters:

```text
Queue 1
```

If it uses too much CPU:

```text
Move To Queue 2
```

Again uses too much CPU:

```text
Move To Queue 3
```

---

## Why?

Interactive jobs are usually short.

CPU-intensive jobs are long.

MLFQ automatically separates them.

---

## Advantages

```text
Flexible

Good Response Time

Adapts Automatically
```

---

## Disadvantages

```text
Complex

Difficult To Configure
```

---

## Interview Fact

```text
MLFQ Tries To Approximate SJF
Without Knowing Future Burst Time
```

Very important.

---

# 17. Dispatcher

Scheduler selects process.

Dispatcher actually runs it.

---

## Scheduler vs Dispatcher

Scheduler:

```text
Chooses Process
```

Dispatcher:

```text
Gives CPU To Process
```

---

## Dispatcher Responsibilities

```text
Context Switch

Switch To User Mode

Jump To Correct Instruction
```

---

## Example

Scheduler decides:

```text
Run P2
```

Dispatcher performs:

```text
Save P1 Context

Load P2 Context

Start P2 Execution
```

---

# 18. Dispatcher Latency

Important interview topic.

---

## Definition

Time taken by dispatcher to stop one process and start another.

---

## Includes

```text
Save Registers

Load Registers

Update PCB

Switch Address Space

Resume Execution
```

---

## Example

Suppose:

```text
Process Switch
```

takes:

```text
2 ms
```

That:

```text
2 ms
```

is dispatcher latency.

---

## Goal

```text
Lower Dispatcher Latency
```

because CPU does useful work only after switching finishes.

---

# 19. Real World Scheduling

Modern operating systems do not use pure:

```text
FCFS

SJF

Priority

Round Robin
```

alone.

---

## Linux

Uses:

```text
Completely Fair Scheduler (CFS)
```

---

## Windows

Uses:

```text
Priority Based Scheduling
+
Round Robin Concepts
```

---

## Why?

Real systems need:

```text
Fairness

Responsiveness

Efficiency
```

simultaneously.

---

# 20. Common Interview Questions

---

## Which Scheduling Algorithm Is Simplest?

```text
FCFS
```

---

## Which Scheduling Algorithm Gives Minimum Average Waiting Time?

```text
SJF
```

---

## Which Scheduling Algorithm Is Preemptive Version Of SJF?

```text
SRTF
```

---

## Which Scheduling Algorithm Is Most Common For Time Sharing Systems?

```text
Round Robin
```

---

## What Causes Starvation?

```text
Priority Scheduling

SJF

SRTF
```

---

## How Is Starvation Prevented?

```text
Aging
```

---

## Difference Between Scheduler And Dispatcher?

Scheduler:

```text
Selects Process
```

Dispatcher:

```text
Performs Switch
```

---

## Why Is Timer Interrupt Important?

```text
Enables Preemptive Scheduling
```

Without timer interrupt:

```text
OS Cannot Force CPU Away
```

---

# 21. Quick Revision

```text
FCFS
=
Arrival Order

Convoy Effect
=
Large Job Delays Small Jobs

SJF
=
Shortest Job First

SRTF
=
Shortest Remaining Time First

Priority
=
Highest Priority First

Round Robin
=
Fixed Time Quantum

Starvation
=
Process Waits Forever

Aging
=
Increase Priority Over Time

Multilevel Queue
=
Multiple Fixed Queues

MLFQ
=
Processes Can Move Between Queues

Scheduler
=
Chooses Process

Dispatcher
=
Runs Process

Dispatcher Latency
=
Time Needed To Switch Processes
```

---

# CPU Scheduling Complete

You should now understand:

```text
Why Scheduling Exists

Schedulers

Scheduling Queues

Scheduling Criteria

FCFS

SJF

SRTF

Priority Scheduling

Round Robin

Starvation

Aging

Multilevel Queue

MLFQ

Dispatcher

Dispatcher Latency
```

