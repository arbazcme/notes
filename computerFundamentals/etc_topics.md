## Threads

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
