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
