# Threads

A **Thread** is the smallest unit of execution within a process. A process may contain one or more threads, all sharing the same process memory and resources.

A thread primarily represents an **execution context**, consisting of:

* Program Counter (PC)
* CPU Register Values
* Stack
* Thread State (Running, Ready, Blocked, etc.)

A CPU core executes instructions using this thread context.

### Execution Flow

1. The OS scheduler selects a thread.
2. The thread's saved state (PC, registers, stack pointer) is loaded into the CPU core.
3. The core executes instructions and updates its hardware registers and PC.
4. During a context switch, the updated CPU state is saved back to the thread.
5. Later, the thread can resume execution from exactly where it stopped.

### Key Idea

A thread does not contain hardware like an ALU or Control Unit. It is a **software representation of execution state**, while the CPU core is the actual hardware that executes instructions.

**Process → Contains Threads**

**Thread → Stores Execution Context**

**CPU Core → Executes Instructions Using That Context**

---
# SRAM vs DRAM


Both SRAM and DRAM are types of RAM, but they are used for different purposes.

### SRAM (Static RAM)

* Very fast memory.
* Stores data using transistor circuits (flip-flops).
* Does not require refreshing.
* Expensive and available in small sizes.
* Used for **CPU Cache (L1, L2, L3)**.

### DRAM (Dynamic RAM)

* Slower than SRAM.
* Stores data using capacitors.
* Capacitors gradually lose charge, so data must be periodically **refreshed**.
* Cheaper and available in large sizes.
* Used as **Main Memory (System RAM)**.

### Why is SRAM Faster?

SRAM does not need refresh operations, while DRAM must continuously refresh its capacitors to prevent data loss.

### Quick Summary

**SRAM → Fast, Expensive, Cache**

**DRAM → Slower, Cheaper, Main Memory**

## Why DRAM Needs Refresh (SRAM vs DRAM)

### How DRAM Stores a Bit

DRAM stores each bit using a tiny **capacitor**.

Think of a capacitor like a very tiny battery:

```text
Charged      = 1
Not Charged  = 0
```

### The Problem

Capacitors naturally leak electricity over time.

So if a capacitor stores:

```text
1
```

after some time it becomes:

```text
0.9
0.8
0.7
...
```

Eventually, the computer can no longer determine whether the value was a **1** or a **0**.

### What is Refresh?

The memory controller periodically checks every DRAM cell and restores its charge.

Before refresh:

```text
1 -> 0.85 -> 0.72 -> 0.61
```

After refresh:

```text
0.61 -> 1
```

The charge is restored.

This process happens thousands of times per second.

### Why is it Called Dynamic RAM?

The stored charge continuously changes because it leaks over time.

Since the data must be constantly maintained and refreshed, it is called:

```text
DRAM = Dynamic RAM
```

### Why Doesn't SRAM Need Refresh?

SRAM does not use capacitors.

It stores data using transistor circuits called **flip-flops**.

```text
1 stays 1
0 stays 0
```

as long as power is supplied.

No periodic refresh is required.

Therefore:

```text
SRAM = Static RAM
```

### Interview Answer

DRAM stores data in capacitors, which gradually lose charge over time. To prevent data loss, the memory controller periodically restores the charge in each cell, a process called **refresh**. SRAM uses transistor flip-flops instead of capacitors, so it does not require refreshing and is therefore faster.
