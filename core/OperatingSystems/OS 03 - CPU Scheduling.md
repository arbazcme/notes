# OS 03 - CPU Scheduling

## Navigation

### Part 1 - Scheduling Fundamentals
- [1. Why CPU Scheduling Exists](#1-why-cpu-scheduling-exists)
- [2. CPU Burst and I/O Burst](#2-cpu-burst-and-io-burst)
- [3. Process Execution Cycle](#3-process-execution-cycle)
- [4. Scheduler Types](#4-scheduler-types)
  - [Long-Term Scheduler](#long-term-scheduler)
  - [Short-Term Scheduler](#short-term-scheduler)
  - [Medium-Term Scheduler](#medium-term-scheduler)
- [5. Scheduling Queues](#5-scheduling-queues)
  - [Job Queue](#job-queue)
  - [Ready Queue](#ready-queue)
  - [Device Queue](#device-queue)
- [6. Scheduling Criteria](#6-scheduling-criteria)
  - [CPU Utilization](#cpu-utilization)
  - [Throughput](#throughput)
  - [Turnaround Time](#turnaround-time)
  - [Waiting Time](#waiting-time)
  - [Response Time](#response-time)
  - [Fairness](#fairness)
- [7. Preemptive vs Non-Preemptive Scheduling](#7-preemptive-vs-non-preemptive-scheduling)

### Part 2 - Scheduling Algorithms
- [8. FCFS](#8-fcfs-first-come-first-served)
- [9. SJF](#9-sjf-shortest-job-first)
- [10. SRTF](#10-srtf-shortest-remaining-time-first)
- [11. Priority Scheduling](#11-priority-scheduling)
- [12. Round Robin](#12-round-robin)

### Part 3 - Advanced Scheduling Concepts
- [13. Starvation](#13-starvation)
- [14. Aging](#14-aging)
- [15. Multilevel Queue Scheduling](#15-multilevel-queue-scheduling)
- [16. Multilevel Feedback Queue Scheduling](#16-multilevel-feedback-queue-scheduling)
- [17. Dispatcher](#17-dispatcher)
- [18. Dispatcher Latency](#18-dispatcher-latency)
- [19. Real World Examples](#19-real-world-examples)
- [20. Common Interview Questions](#20-common-interview-questions)
- [21. Quick Revision](#21-quick-revision)

---

# Part 1 - Scheduling Fundamentals

---

# 1. Why CPU Scheduling Exists?

To understand CPU Scheduling, first understand the problem.

Suppose we have:

```text
Chrome
VS Code
Spotify
WhatsApp
```

all running simultaneously.

However:

```text
1 CPU Core
```

can execute:

```text
Only One Process
```

at a time.

---

# The Problem

Suppose all processes are ready:

```text
Ready Queue

Chrome
VS Code
Spotify
WhatsApp
```

CPU must decide:

```text
Who gets CPU next?
```

This decision is called:

```text
CPU Scheduling
```

---

# Definition

CPU Scheduling is the process of selecting which ready process or thread gets the CPU next.

---

# Why Is Scheduling Needed?

Without scheduling:

```text
Some processes may never run.

CPU may remain idle.

System may become unresponsive.
```

Scheduling helps:

```text
Maximize CPU Usage

Improve Performance

Improve Responsiveness

Ensure Fairness
```

---

# Real Example

Suppose:

```text
Chrome
```

is downloading a file.

At the same time:

```text
VS Code
```

is compiling code.

And:

```text
Spotify
```

is playing music.

CPU Scheduling decides:

```text
Which process runs now?

Which process waits?

When should a process be stopped?
```

---

# Main Goal

Keep CPU busy as much as possible.

Ideal:

```text
CPU Utilization = 100%
```

Practical:

```text
High Utilization
```

---

# 2. CPU Burst and I/O Burst

Very Important Concept.

Interview Favorite.

---

# Observation

Programs do not continuously use CPU.

Example:

```c
read(file);
processData();
write(file);
```

Execution pattern:

```text
Read File
↓
Wait For Disk

Process Data
↓
Use CPU

Write File
↓
Wait For Disk
```

---

# CPU Burst

Time spent actually executing instructions on CPU.

Example:

```text
Calculations
Sorting
Searching
Loops
```

CPU actively working.

---

# I/O Burst

Time spent waiting for:

```text
Disk

Keyboard

Network

Mouse

Printer
```

CPU is not doing useful work for that process.

---

# Example

```text
CPU Burst
   ↓
I/O Burst
   ↓
CPU Burst
   ↓
I/O Burst
```

Most processes alternate between these two.

---

# Real Example

Chrome downloading webpage:

```text
Request Sent
↓
Waiting For Network
(I/O Burst)

Page Processing
(CPU Burst)

Download Images
(I/O Burst)

Render Page
(CPU Burst)
```

---

# Why Is This Important?

Suppose process enters:

```text
I/O Burst
```

CPU should not wait.

OS schedules another process.

This improves:

```text
CPU Utilization
```

---

# 3. Process Execution Cycle

Typical execution:

```text
READY
 ↓
RUNNING
 ↓
WAIT
 ↓
READY
 ↓
RUNNING
 ↓
TERMINATED
```

---

# Example

Process starts:

```text
READY
```

Scheduler chooses it:

```text
RUNNING
```

Needs disk:

```text
WAIT
```

Disk completes:

```text
READY
```

Gets CPU again:

```text
RUNNING
```

Finishes:

```text
TERMINATED
```

---

# Why Scheduler Is Needed Continuously?

Because processes constantly move between:

```text
READY

RUNNING

WAIT
```

OS must repeatedly decide:

```text
Who gets CPU next?
```

---

# 4. Scheduler Types

Operating Systems use multiple schedulers.

---

# Why Multiple Schedulers?

Different decisions occur at different times.

Example:

```text
Should process enter memory?

Should process get CPU?

Should process be swapped out?
```

Different schedulers handle these.

---

# Long-Term Scheduler

Also called:

```text
Job Scheduler
```

---

# Responsibility

Decides:

```text
Which jobs enter memory?
```

---

# Example

Suppose:

```text
100 Processes Submitted
```

RAM cannot hold all.

Long-term scheduler chooses:

```text
20 Processes
```

to enter memory.

---

# Goal

Control:

```text
Degree of Multiprogramming
```

Meaning:

```text
How many processes stay in memory.
```

---

# Short-Term Scheduler

Most important scheduler.

Also called:

```text
CPU Scheduler
```

---

# Responsibility

Chooses:

```text
Which READY process gets CPU?
```

---

# Example

Ready Queue:

```text
Chrome
VS Code
Spotify
```

Short-term scheduler selects one.

---

# Frequency

Runs extremely often.

Sometimes every:

```text
Few Milliseconds
```

---

# Medium-Term Scheduler

Related to swapping.

---

# Responsibility

Moves processes between:

```text
RAM
```

and

```text
Disk
```

---

# Example

RAM Full.

OS moves process:

```text
READY
```

↓

```text
SUSPENDED READY
```

using medium-term scheduler.

---

# Quick Memory Trick

```text
Long-Term
=
Admit Process

Short-Term
=
Give CPU

Medium-Term
=
Swap Process
```

---

# 5. Scheduling Queues

Processes move through various queues during their lifetime.

Think of queues as waiting lines managed by the OS.

---

# Why Queues Exist?

Suppose:

```text
100 Processes
```

want CPU.

CPU can run:

```text
Only One Process Per Core
```

Therefore processes must wait somewhere.

OS stores them in queues.

---

# Main Scheduling Queues

```text
Job Queue
Ready Queue
Device Queue
```

---

# Job Queue

Contains:

```text
All Processes In System
```

including:

```text
New
Ready
Waiting
Running
```

Think:

```text
Every Process Known To OS
```

belongs to Job Queue.

---

# Example

```text
Chrome
VS Code
Spotify
Game
Notepad
```

All belong to:

```text
Job Queue
```

---

# Ready Queue

Most important queue.

Contains:

```text
Processes Ready To Execute
```

Requirements:

```text
Present In RAM
Not Waiting For I/O
Need CPU
```

---

# Example

```text
Ready Queue

Chrome
VS Code
Spotify
```

Scheduler selects one.

---

# Device Queue

Contains:

```text
Processes Waiting For I/O
```

Example:

```text
Disk Queue
Printer Queue
Network Queue
```

---

# Example

Chrome requests:

```text
Read File
```

Process moves:

```text
Running
      ↓
Device Queue
```

until I/O completes.

---

# Queue Flow

```text
Job Queue
     ↓
Ready Queue
     ↓
Running
     ↓
Device Queue
     ↓
Ready Queue
     ↓
Running
```

---

# Real Example

```text
Open Chrome
      ↓
Job Queue

Loaded Into RAM
      ↓
Ready Queue

Gets CPU
      ↓
Running

Needs Internet Data
      ↓
Device Queue

Data Arrives
      ↓
Ready Queue

Gets CPU Again
      ↓
Running
```

---

# 6. Scheduling Criteria

Question:

```text
How Do We Decide
Whether A Scheduling Algorithm Is Good?
```

Answer:

Using scheduling criteria.

---

# Main Criteria

```text
CPU Utilization
Throughput
Turnaround Time
Waiting Time
Response Time
Fairness
```

---

# CPU Utilization

Definition:

```text
Percentage Of Time
CPU Is Busy
```

---

# Formula

```text
CPU Utilization

=
CPU Busy Time
---------------
Total Time
× 100
```

---

# Example

Suppose:

```text
CPU Busy = 90 Seconds

Total Time = 100 Seconds
```

Then:

```text
90%
```

utilization.

---

# Goal

Keep CPU busy as much as possible.

Higher:

```text
Better
```

---

# Throughput

Definition:

```text
Number Of Processes
Completed Per Unit Time
```

---

# Example

Suppose:

```text
10 Processes
```

finish in:

```text
1 Minute
```

Throughput:

```text
10 Processes/Minute
```

---

# Goal

Higher Throughput

```text
Better
```

---

# Turnaround Time

Interview Favorite.

Definition:

```text
Total Time Taken
From Submission
To Completion
```

---

# Formula

```text
Turnaround Time

=
Completion Time
-
Arrival Time
```

---

# Example

Process arrives:

```text
10:00
```

Finishes:

```text
10:20
```

Turnaround Time:

```text
20 Minutes
```

---

# Includes Everything

```text
Waiting Time

+
CPU Time

+
I/O Time
```

---

# Waiting Time

Definition:

```text
Time Spent Waiting
In Ready Queue
```

---

# Formula

```text
Waiting Time

=
Turnaround Time
-
CPU Burst Time
```

---

# Example

Process:

```text
Needs CPU For 5 Seconds
```

Waited:

```text
15 Seconds
```

before execution.

Waiting Time:

```text
15 Seconds
```

---

# Goal

Lower Waiting Time

```text
Better
```

---

# Response Time

Important for interactive systems.

Definition:

```text
Time Between Request
And First Response
```

---

# Formula

```text
Response Time

=
First CPU Allocation
-
Arrival Time
```

---

# Example

User clicks:

```text
Chrome
```

Application starts responding after:

```text
1 Second
```

Response Time:

```text
1 Second
```

---

# Difference

Turnaround Time:

```text
Start
↓
Finish
```

Response Time:

```text
Start
↓
First Response
```

---

# Fairness

Definition:

```text
Every Process Gets
Reasonable CPU Time
```

---

# Bad Scheduler

```text
Chrome Always Runs

VS Code Never Runs
```

Not fair.

---

# Good Scheduler

CPU distributed reasonably.

---

# Ideal Scheduler Wants

```text
High CPU Utilization

High Throughput

Low Waiting Time

Low Turnaround Time

Low Response Time

Good Fairness
```

---

# 7. Preemptive vs Non-Preemptive Scheduling

Most Important Foundation Before Algorithms.

---

# Non-Preemptive Scheduling

Definition:

```text
Once CPU Is Given

Process Keeps CPU

Until It Finishes
Or Waits For I/O
```

OS cannot forcibly take CPU away.
Process can take a cpu without giving control back to os ! (Not good)

---

# Example

```text
Process A Starts
```

Needs:

```text
10 Seconds
```

CPU.

Even if:

```text
Process B Arrives
```

B must wait.

---

# Execution

```text
AAAAAAAAAA
BBBB
CCCC
```

---

# Advantages

```text
Simple

Low Context Switching

Easy To Implement
```

---

# Disadvantages

```text
Poor Responsiveness

Long Waiting Times
```

---

# Example

Suppose:

```text
Video Rendering
```

starts first.

Needs:

```text
30 Seconds
```

Chrome opened after.

Chrome waits:

```text
30 Seconds
```

Bad user experience.

---

# Preemptive Scheduling

Definition:

```text
OS Can Take CPU Away
From Running Process
```

---

# Example

```text
Process A Running
```

Timer expires.

OS interrupts:

```text
Process A
```

and runs:

```text
Process B
```

---

# Execution

```text
AAA
BBB
AA
CC
BB
```

---

# Advantages

```text
Better Responsiveness

Better Fairness

Interactive Systems Work Well
```

---

# Disadvantages

```text
More Context Switching

More Overhead

More Complex
```

---

# Why Modern OS Use Preemptive Scheduling?


Imagine:

```text
Chrome
VS Code
Spotify
Game
```

Without preemption:

```text
One Process Could Monopolize CPU
```

System feels frozen.

Preemption prevents this.

---

# Examples

Non-Preemptive:

```text
FCFS
Non-Preemptive SJF
```

Preemptive:

```text
Round Robin

SRTF

Preemptive Priority
```

---

# Quick Comparison

| Feature              | Non-Preemptive | Preemptive |
| -------------------- | -------------- | ---------- |
| CPU Taken Away By OS | No             | Yes        |
| Responsiveness       | Poor           | Better     |
| Context Switching    | Less           | More       |
| Complexity           | Simple         | Complex    |
| Fairness             | Lower          | Higher     |

---

# Scheduling Formula Cheat Sheet

| Metric                | Formula                                    |
| --------------------- | ------------------------------------------ |
| Turnaround Time (TAT) | Completion Time - Arrival Time             |
| Waiting Time (WT)     | Turnaround Time - CPU Burst Time           |
| Response Time (RT)    | First CPU Allocation Time - Arrival Time   |
| Throughput            | Number of Completed Processes / Total Time |
| CPU Utilization       | (CPU Busy Time / Total Time) × 100         |

---


Conceptually OS textbooks discuss process scheduling.

Modern operating systems generally schedule threads because threads are the actual execution units.

# 8. FCFS (First Come First Served)

Simplest scheduling algorithm.

Processes execute in order of arrival.

First process to arrive gets CPU first.

---

## Real Life Analogy

```text
Bank Queue

First Person Arrives
        ↓
First Person Served
```

---

## Example

| Process | Arrival | Burst |
| ------- | ------- | ----- |
| P1      | 0       | 5     |
| P2      | 1       | 3     |
| P3      | 2       | 2     |

---

## Gantt Chart

```text
0      5      8      10
| P1 | P2 | P3 |
```

---

## Waiting Time

P1:

```text
0
```

P2:

```text
5 - 1 = 4
```

P3:

```text
8 - 2 = 6
```

Average Waiting Time:

```text
(0 + 4 + 6) / 3

= 3.33
```

---

## Advantages

```text
Simple

Easy To Implement

No Starvation
```

---

## Disadvantages

```text
Poor Response Time

Large Jobs Delay Small Jobs
```

---

## Convoy Effect

Very Important Interview Topic.

Suppose:

| Process | Burst |
| ------- | ----- |
| P1      | 100   |
| P2      | 2     |
| P3      | 1     |

Execution:

```text
P1
 ↓
P2
 ↓
P3
```

Small processes wait behind huge process.

Called:

```text
Convoy Effect
```

---

# 9. SJF (Shortest Job First)

Idea:

```text
Shortest Burst Time First
```

---

## Example

| Process | Burst |
| ------- | ----- |
| P1      | 8     |
| P2      | 4     |
| P3      | 2     |

Execution:

```text
P3
 ↓
P2
 ↓
P1
```

---

## Why Does It Work?

Small jobs finish quickly.

Average waiting time reduces.

---

## Important Fact

```text
SJF Produces Minimum Average Waiting Time
```

among non-preemptive algorithms.

Very popular interview question.

---

## Problem

OS must know:

```text
Future CPU Burst Time
```

which is usually unknown.

Therefore perfect SJF is difficult.

---

## Advantages

```text
Excellent Waiting Time

Excellent Turnaround Time
```

---

## Disadvantages

```text
Future Burst Time Required

Possible Starvation
```

---

# 10. SRTF (Shortest Remaining Time First)

Preemptive version of SJF.

---

## Core Idea

Always execute process with:

```text
Shortest Remaining Burst Time
```

SJF Produces Minimum Average Waiting Time
among non-preemptive scheduling algorithms.

Its preemptive version, SRTF, can further reduce
average waiting time by preempting long jobs when
shorter jobs arrive.

---


## Example

P1 arrives:

```text
Burst = 10
```

Runs for:

```text
2 Seconds
```

Remaining:

```text
8
```

Now P2 arrives:

```text
Burst = 2
```

---

## Scheduler Decision

Compare:

```text
P1 Remaining = 8

P2 Remaining = 2
```

Run:

```text
P2
```

instead of P1.

---

## Why?

P2 can finish earlier.

Improves average waiting time.

---

## Advantages

```text
Best Waiting Time

Best Turnaround Time
```

---

## Disadvantages

```text
Many Context Switches

More Overhead

Complex Implementation
```

---

# 11. Priority Scheduling

Each process receives a priority value.

Higher priority process executes first.

---

## Example

| Process | Priority |
| ------- | -------- |
| P1      | 3        |
| P2      | 1        |
| P3      | 2        |

Assume:

```text
Smaller Number
=
Higher Priority
```

Execution:

```text
P2
 ↓
P3
 ↓
P1
```

---

## Types

### Non-Preemptive

Running process keeps CPU.

---

### Preemptive

Higher priority process can interrupt current process.

---

## Starvation Problem

Very important.

Suppose:

```text
P1 Priority = 100
```

Low priority.

New high-priority jobs keep arriving.

P1 may never execute.

---

## Advantages

```text
Important Jobs Execute First
```

---

## Disadvantages

```text
Starvation Possible
```

---

# 12. Round Robin

Most important practical scheduling algorithm.

Used in:

```text
Time Sharing Systems
```

---

## Core Idea

Each process gets:

```text
Time Quantum
```

Example:

```text
2 ms
```

---

## Example

| Process | Burst |
| ------- | ----- |
| P1      | 5     |
| P2      | 4     |
| P3      | 3     |

Quantum:

```text
2
```

---

## Execution

```text
P1
 ↓
P2
 ↓
P3
 ↓
P1
 ↓
P2
 ↓
P3
 ↓
P1
```

---

## Why Is It Fair?

Every process gets CPU regularly.

No process monopolizes CPU.

---

## Quantum Too Small

```text
Many Context Switches
```

High overhead.

---

## Quantum Too Large

Behaves like:

```text
FCFS
```

---

## Advantages

```text
Fair

Responsive

Good For Interactive Systems
```

---

## Disadvantages

```text
Context Switching Overhead
```

---

# Formula Cheat Sheet

| Metric          | Formula                             |
| --------------- | ----------------------------------- |
| Turnaround Time | Completion Time - Arrival Time      |
| Waiting Time    | Turnaround Time - Burst Time        |
| Response Time   | First CPU Allocation - Arrival Time |
| Throughput      | Completed Processes / Total Time    |
| CPU Utilization | Busy Time / Total Time × 100        |

---

# Gantt Chart Basics

A Gantt Chart shows:

```text
Which Process
Runs At Which Time
```

Example:

```text
0      5      8
| P1 | P2 |
```

---

# Solved Example

| Process | Arrival | Burst |
| ------- | ------- | ----- |
| P1      | 0       | 5     |
| P2      | 2       | 3     |

FCFS:

```text
0      5      8
| P1 | P2 |
```

---

P1:

```text
CT = 5

TAT = 5 - 0 = 5

WT = 5 - 5 = 0

RT = 0
```

---

P2:

```text
CT = 8

TAT = 8 - 2 = 6

WT = 6 - 3 = 3

RT = 5 - 2 = 3
```

---

# Comparison Table

| Algorithm   | Preemptive | Starvation | Response Time |
| ----------- | ---------- | ---------- | ------------- |
| FCFS        | No         | No         | Poor          |
| SJF         | No         | Possible   | Good          |
| SRTF        | Yes        | Possible   | Better        |
| Priority    | Both       | Possible   | Depends       |
| Round Robin | Yes        | No         | Excellent     |

---

# Advantages and Disadvantages Summary

```text
FCFS
=
Simple
But Convoy Effect

SJF
=
Best Waiting Time
But Future Burst Needed

SRTF
=
Best Average Performance
But Many Context Switches

Priority
=
Important Jobs First
But Starvation Possible

Round Robin
=
Fair And Responsive
But Quantum Selection Matters
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



