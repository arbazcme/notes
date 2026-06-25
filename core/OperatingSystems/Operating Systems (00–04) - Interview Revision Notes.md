# Operating Systems (00–04) - Interview Revision Notes

---

# Q1. What happens when you run a program?

### Answer

1. The executable is stored on secondary storage (SSD/HDD).
2. The OS loader loads the executable into RAM.
3. The OS creates a **process**.
4. A **PCB (Process Control Block)** is created containing:

   * Process ID
   * Process State
   * Scheduling Information
   * Memory Information
   * Open Files, etc.
5. Memory layout is initialized:

   * **Text** → Executable code
   * **Data** → Global & Static variables
   * **Heap** → Dynamic memory
   * **Stack** → Function calls & Local variables
6. The OS creates the **main thread**.
7. The thread enters the **Ready Queue**.
8. The **Short-Term Scheduler** selects the thread.
9. The **Dispatcher** loads the thread's saved context (PC, SP, Registers) into the CPU.
10. The CPU starts executing the program.

---

# Q2. Explain Context Switching.

### Answer

A context switch is the process of switching the CPU from one thread to another.

### Steps

1. Interrupt or scheduler gains CPU control.
2. Save Thread A's execution context into its **TCB**:

   * Program Counter
   * Stack Pointer
   * CPU Registers
3. Scheduler selects Thread B.
4. Dispatcher restores Thread B's saved context from its TCB.
5. CPU resumes execution exactly where Thread B previously stopped.

### Important

* Stack is **not copied**.
* Heap is **not copied**.
* Code is **not copied**.
* Only the execution state is saved/restored.

---

# Q3. What is a Race Condition?

### Answer

A race condition occurs when multiple threads access shared data simultaneously and the final output depends on the order of execution.

Example:

```cpp
x++;
```

is **not atomic**.

Internally it performs:

```
Load x
Increment
Store x
```

Possible execution:

```
Thread A : Load x (0)

Context Switch

Thread B : Load x (0)
Thread B : Increment
Thread B : Store x = 1

Context Switch

Thread A : Increment
Thread A : Store x = 1
```

Expected:

```
2
```

Actual:

```
1
```

---

# How to prevent Race Conditions?

Use synchronization primitives such as:

* Mutex
* Semaphore
* Condition Variable (with Mutex)

The entire critical section should execute atomically.

---

# Conditions for a Correct Synchronization Solution

1. **Mutual Exclusion**

   * Only one thread enters the critical section.

2. **Progress**

   * If no thread is inside, one waiting thread should eventually enter.

3. **Bounded Waiting**

   * Every waiting thread should eventually get a chance.

---

# Q4. Explain Condition Variables.

### Code

```cpp
unique_lock<mutex> lock(mtx);

while (!condition)
    cv.wait(lock);

/* Critical Section */
```

### Working

1. Thread acquires the mutex.
2. Checks the condition.
3. If false:

   * `cv.wait(lock)` **atomically**

     * unlocks the mutex,
     * puts the thread to sleep,
     * places it in the condition variable's waiting queue.
4. Another thread updates the shared data.
5. It calls:

   * `notify_one()` or
   * `notify_all()`.
6. Waiting thread wakes up.
7. `cv.wait(lock)` automatically re-acquires the mutex.
8. Condition is checked again.
9. If true, enter the critical section.
10. Unlock the mutex after finishing.

---

# Why use `while` instead of `if`?

Because a thread may wake up even though the condition is still false.

Reasons:

* `notify_all()`
* Another thread acquires the mutex first and changes the shared state.
* Spurious wakeups.

Therefore:

```
Wake up
↓

Acquire Mutex
↓

Check Condition Again
↓

False?
↓

Sleep Again
```

---

# Common Interview Questions

### Difference between Process and Thread

**Process**

* Independent execution unit
* Own address space
* Expensive context switch

**Thread**

* Unit of execution
* Shares process memory
* Own Stack, Registers, Program Counter
* Lightweight

---

### What does a Thread contain?

* Program Counter
* Stack
* Stack Pointer
* CPU Registers
* Thread State

---

### What is stored in PCB?

* Process ID
* Process State
* Scheduling Information
* Memory Information
* Open Files
* Pointer to Threads

---

### What is stored in TCB?

* Thread ID
* Thread State
* Program Counter
* Stack Pointer
* CPU Registers
* Pointer to Stack
* Pointer to PCB

---

# Important Interview Tips

* CPU executes **threads**, not processes.
* Dispatcher performs the actual context switch.
* Scheduler decides **which** thread/process should run.
* `x++` is **not atomic**.
* Condition Variables are always used with a **Mutex**.
* `cv.wait(lock)` automatically:

  * Unlocks the mutex.
  * Sleeps.
  * Re-locks the mutex before returning.
* Stack is **not** stored inside the TCB.
* Only the **Stack Pointer** and execution context are saved.
* Heap and Code are **not copied** during a context switch.

---

# Final Takeaway

Understand the sequence rather than memorizing definitions:

```
Program
↓

Process
↓

Main Thread
↓

Ready Queue
↓

Scheduler

↓

Dispatcher

↓

CPU

↓

Execution

↓

Context Switch (Save → Restore)

↓

Synchronization (Mutex / Semaphore / Condition Variable)
```

# Recommended additions to your OS (00–04) Interview Revision Notes

Add the following sections after your current notes.

## CPU Scheduling

### Q. Difference between Waiting Time, Turnaround Time and Response Time

**Answer**

* **Waiting Time (WT)**: Total time a process spends waiting in the Ready Queue.
* **Turnaround Time (TAT)**: Total time from process arrival until completion.
* **Response Time (RT)**: Time from process arrival until it gets the CPU for the first time.

**Formulae**

```text
Turnaround Time = Completion Time - Arrival Time

Waiting Time = Turnaround Time - Burst Time

Response Time = First CPU Allocation Time - Arrival Time
```

---

### Q. Why does SJF give the minimum average waiting time?

**Answer**

SJF always executes the shortest available process first. Short jobs finish early and do not wait behind long jobs, reducing the total waiting time of all processes. It can be proven that no other scheduling algorithm produces a lower average waiting time when burst times are known.

---

### Q. What is Starvation? How is it solved?

**Answer**

Starvation occurs when a process waits indefinitely because other processes continuously receive CPU time.

**Solution:** Aging.

Aging gradually increases the priority of waiting processes so every process eventually gets CPU time.

---

### Q. Why does Round Robin improve Response Time?

**Answer**

Round Robin assigns each process a fixed time quantum.

Even long processes get CPU time quickly, improving response time and making the system more responsive for interactive users.

---

### Q. Scheduler vs Dispatcher

| Scheduler                               | Dispatcher                               |
| --------------------------------------- | ---------------------------------------- |
| Decides which process/thread should run | Gives CPU to the selected process/thread |
| Makes scheduling decision               | Performs context switch                  |
| Chooses from Ready Queue                | Loads saved context into CPU             |
| Higher-level decision                   | Actual execution transfer                |

---

# Threads

### Q. User-Level Threads vs Kernel-Level Threads

**User-Level Threads**

* Managed by user library.
* Faster creation and switching.
* Kernel is unaware of them.
* Blocking system call blocks all threads.

**Kernel-Level Threads**

* Managed by the OS kernel.
* Slightly slower.
* One blocked thread does not block others.
* Better parallelism on multi-core CPUs.

---

### Q. Why are Threads called Lightweight?

**Answer**

Threads share the process's address space, code, data and heap. Only their execution state (stack, registers, program counter) is private.

Therefore:

* Less memory
* Faster creation
* Faster context switching

---

### Q. Can two threads have separate heaps?

**Answer**

No.

Threads belonging to the same process share one heap.

Each thread has its own:

* Stack
* Registers
* Program Counter

---

### Q. What is shared between Threads?

Shared:

* Code (Text)
* Heap
* Global Variables
* Open Files

Private:

* Stack
* Stack Pointer
* Program Counter
* Registers

---

# Synchronization

### Q. Mutex vs Binary Semaphore

| Mutex                     | Binary Semaphore                       |
| ------------------------- | -------------------------------------- |
| Ownership exists          | No ownership                           |
| Only owner can unlock     | Any thread may signal                  |
| Used for mutual exclusion | Used for synchronization and signaling |
| Lock / Unlock             | Wait / Signal                          |

---

### Q. Why is Mutex preferred for Critical Sections?

**Answer**

Mutex guarantees ownership.

Only the thread that locks the mutex can unlock it, preventing accidental release by another thread.

---

### Q. When is a Counting Semaphore used?

**Answer**

A counting semaphore controls access to multiple identical resources.

Examples:

* Database connection pool
* Printer pool
* Multiple parking slots
* Producer-Consumer buffer

---

### Q. Producer-Consumer

**Idea**

Producer inserts data into a shared buffer.

Consumer removes data.

Synchronization ensures:

* Producer doesn't insert into a full buffer.
* Consumer doesn't remove from an empty buffer.
* Only one thread accesses the buffer at a time.

---

### Q. Readers-Writers Problem

**Answer**

Multiple readers may access shared data simultaneously.

A writer requires exclusive access.

Goal:

* Allow maximum concurrent readers.
* Prevent data inconsistency during writes.

---

### Q. Dining Philosophers

**Answer**

Each philosopher requires two forks.

If every philosopher picks one fork and waits for the other, a circular wait occurs, causing deadlock.

Common solutions:

* Limit simultaneous philosophers.
* Number forks and acquire in order.
* Use a waiter/monitor.

---

### Q. Monitor vs Condition Variable

**Monitor**

* High-level synchronization construct.
* Contains shared data, mutex and condition variables.
* Only one thread executes inside at a time.

**Condition Variable**

* Synchronization primitive used inside a monitor or with a mutex.
* Allows threads to sleep until a condition becomes true.

---

### Q. Why use notify_one() instead of notify_all()?

**Answer**

`notify_one()` wakes only one waiting thread.

Advantages:

* Fewer context switches.
* Less CPU overhead.
* Avoids the "thundering herd" problem.

`notify_all()` is used only when every waiting thread may be able to make progress.

---

# Frequently Asked One-Liners

* CPU executes **threads**, not processes.
* Dispatcher performs the context switch.
* Scheduler decides who should run.
* Context switching saves only execution state, not memory.
* Stack belongs to a thread.
* Heap belongs to a process.
* `x++` is not atomic.
* Condition Variables always work with Mutexes.
* Use `while`, not `if`, with `cv.wait()`.
* Mutex provides ownership; semaphore does not.
* SJF minimizes average waiting time.
* Round Robin minimizes response time.
* Aging prevents starvation.

## Q. When is the Long-Term Scheduler used?

### Answer

In the **classical OS model**, the **Long-Term Scheduler (Job Scheduler)** is responsible for **admitting new jobs from secondary storage (disk) into main memory (RAM)**. It controls the **degree of multiprogramming** by deciding how many processes are allowed into memory.

**Flow:**

```text
Disk (Job Pool)
      │
      ▼
Long-Term Scheduler
      │
      ▼
RAM (Ready Queue)
      │
      ▼
Short-Term Scheduler
      │
      ▼
CPU
```

**Example:**
If there are **1000 jobs on disk** but RAM can hold only **100**, the Long-Term Scheduler selects which 100 jobs are admitted into memory.

---

## Q. What is the difference between the Long-Term and Medium-Term Scheduler?

| Long-Term Scheduler                        | Medium-Term Scheduler                                  |
| ------------------------------------------ | ------------------------------------------------------ |
| Admits **new jobs** into RAM.              | Swaps **existing processes** out of and back into RAM. |
| Works on jobs waiting on disk.             | Works on processes already in memory.                  |
| Controls the degree of multiprogramming.   | Frees memory during memory pressure.                   |
| Process enters RAM for the **first time**. | Process is **temporarily removed** and later resumed.  |

---

## Q. Is the Long-Term Scheduler used in modern operating systems?

**Not as a separate scheduler.**

Modern operating systems (Windows, Linux, macOS) typically **do not have a distinct Long-Term Scheduler**.

Instead:

```text
User runs program
        │
        ▼
OS Loader loads executable
        │
        ▼
Kernel creates PCB & memory layout
        │
        ▼
Ready Queue
        │
        ▼
Short-Term Scheduler
        │
        ▼
CPU
```

The work traditionally associated with the Long-Term Scheduler is largely handled by the **loader**, **process creation**, and **memory management**.

---

## Interview Tip

If an interviewer says:

> "The Long-Term Scheduler loads the program into RAM."

A more accurate response is:

> "The **OS Loader** loads the executable into memory. The **Long-Term Scheduler** (in the classical OS model) decides **which jobs are admitted into memory**, not how the executable is loaded."

