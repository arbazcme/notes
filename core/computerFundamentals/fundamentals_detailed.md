# Computer Fundamentals

## Types of Software

```text
Software
│
├── System Software
│     ├── Operating System
│     ├── Device Drivers
│     └── Compilers
│
└── Application Software
      ├── Chrome
      ├── WhatsApp
      ├── Photoshop
      └── Microsoft Word
```

### System Software

Acts as a bridge between **Hardware** and **Applications**.

**Purpose:** Manage and control hardware resources.

**Examples:**

* Operating Systems
* Device Drivers
* Compilers

---

### Application Software

Programs used directly by end users.

**Purpose:** Perform specific user tasks.

**Examples:**

* Chrome
* WhatsApp
* Photoshop
* Microsoft Word

---

### Easy Way to Remember

```text
User
  ↓
Application Software
  ↓
System Software
  ↓
Hardware
```

**Application Software** helps the user.

**System Software** helps the computer.


How Hardware and Software Work Together

## How Hardware and Software Work Together

### Example: Pressing the Letter "A"

When you press **A**, the keyboard controller detects the key press and generates a **scan code**.


The keyboard then sends an **interrupt** to the CPU, notifying it that an input event has occurred.


The CPU temporarily pauses the current task and transfers control to the **Operating System**, specifically the **keyboard driver**.


The keyboard driver translates the scan code into the character:


The OS then sends a keyboard event to the active application (e.g., Notepad).


The application decides to display **A** and requests the OS to update the screen.


The OS forwards drawing instructions to the graphics driver/GPU, which renders the character and sends the updated image to the monitor.

### Complete Flow

```text
Press A
   ↓
Keyboard
   ↓
Interrupt
   ↓
CPU
   ↓
Operating System
   ↓
Keyboard Driver
   ↓
Application
   ↓
Operating System
   ↓
Graphics Driver / GPU
   ↓
Monitor
   ↓
A Appears
```

### Key Point

The **CPU does not create the interrupt**. The **keyboard hardware generates the interrupt**, and the CPU responds by allowing the Operating System to handle the event.

### Interview Answer

When a key is pressed, the keyboard generates a scan code and sends an interrupt to the CPU. The CPU transfers control to the Operating System, whose keyboard driver converts the scan code into a character. The OS sends the event to the active application, which requests a display update. The OS and GPU then render the character on the monitor.


---
# Computer Fundamentals

## CPU (Central Processing Unit)

The CPU executes program instructions using the:

```text
Fetch → Decode → Execute → Store
```

### Components

| Component         | Purpose                                        |
| ----------------- | ---------------------------------------------- |
| Control Unit (CU) | Fetches, decodes, and controls execution       |
| ALU               | Performs arithmetic and logical operations     |
| Registers         | Smallest and fastest storage inside CPU        |
| Cache             | High-speed memory storing frequently used data |




### Registers

Registers are tiny storage locations inside the CPU.

Uses:

- Store instructions
- Store memory addresses
- Store temporary results

They are the fastest memory in the computer because they are physically inside the CPU.


### CPU Cores

A core is an independent processing unit inside the CPU.

```text
Single Core → One execution flow

Multi-Core → Multiple execution flows
```

Examples:

* Dual Core → 2 Cores
* Quad Core → 4 Cores
* Hexa Core → 6 Cores
* Octa Core → 8 Cores

Benefits:

* Better multitasking
* Faster processing
* Parallel execution

Multiple cores allow multiple threads or tasks to execute simultaneously.

### Clock Speed

Number of CPU cycles executed per second.

```text
MHz = Million Cycles / Second
GHz = Billion Cycles / Second
```

Example:

```text
3 GHz = 3 Billion Cycles / Second
```

---

## Memory Hierarchy

```text
Registers
    ↓
Cache (SRAM)
    ↓
RAM (DRAM)
    ↓
SSD / HDD
```

Higher = Faster, Smaller, Expensive

Lower = Slower, Larger, Cheaper

### cache
Purpose:

Store frequently used data so the CPU does not need to access slower RAM repeatedly.

Without Cache:

CPU ↔ RAM

With Cache:

CPU ↔ Cache ↔ RAM

Result:

- Faster execution
- Reduced waiting time


---

## Primary vs Secondary Memory

### Primary Memory

Directly accessible by CPU.

Examples:

* Registers
* Cache
* RAM

Characteristics:

* Fast
* Expensive
* Smaller Capacity

### Secondary Memory

Permanent storage.

Examples:

* SSD
* HDD
* USB Drives

Characteristics:

* Slower
* Cheaper
* Larger Capacity

Data stored in SSD/HDD cannot be directly executed by the CPU.

It must first be loaded into RAM.

SSD/HDD → RAM → CPU

---

## RAM

**RAM (Random Access Memory)** stores data currently being used by programs.

Characteristics:

* Volatile
* Read/Write
* Fast

Example:

When Chrome runs, its data is loaded into RAM.

### Types of RAM

#### SRAM (Static RAM)

#### DRAM (Dynamic RAM)

### SRAM vs DRAM

| Feature | SRAM       | DRAM        |
| ------- | ---------- | ----------- |
| Storage | Flip-Flops | Capacitors  |
| Refresh | No         | Yes         |
| Speed   | Faster     | Slower      |
| Cost    | Expensive  | Cheap       |
| Usage   | Cache      | Main Memory |


### Why is Cache Faster than RAM?

Cache uses SRAM, while main memory uses DRAM.

SRAM:
- Uses transistor flip-flops
- No refresh required
- Very fast
- Expensive

DRAM:
- Uses capacitors
- Requires refresh operations
- Slower but cheaper

Since SRAM does not require refreshing and is physically closer to the CPU, cache is much faster than RAM.

---

## ROM

**ROM (Read Only Memory)** is non-volatile memory that stores firmware.

Example:

* BIOS / UEFI
* Startup Instructions

### Types

* PROM → Programmable once
* EPROM → Erased using UV light
* EEPROM → Electrically erasable and rewritable

---

## Storage Devices

### HDD (Hard Disk Drive)

Stores data on magnetic rotating disks.

**Pros**

* Cheap
* Large Capacity

**Cons**

* Slower
* Mechanical Parts

### SSD (Solid State Drive)

Stores data using flash memory.

**Pros**

* Faster
* Reliable
* Lower Power Consumption

**Cons**

* More Expensive

### HDD vs SSD

| Feature      | HDD     | SSD      |
| ------------ | ------- | -------- |
| Speed        | Slow    | Fast     |
| Moving Parts | Yes     | No       |
| Reliability  | Lower   | Higher   |
| Cost         | Cheaper | Costlier |

---

## Data Representation

### Bit

Smallest unit of data.

```text
0 or 1
```

### Byte

```text
1 Byte = 8 Bits
```

### Storage Units

| Unit | Value      |
| ---- | ---------- |
| Byte | 8 Bits     |
| KB   | 1024 Bytes |
|      |            |

---

## Number Systems

| System      | Base | Digits   |
| ----------- | ---- | -------- |
| Decimal     | 10   | 0-9      |
| Binary      | 2    | 0,1      |
| Octal       | 8    | 0-7      |
| Hexadecimal | 16   | 0-9, A-F |

### Example

```text
Decimal 10 = Binary 1010
```

```text
1010₂

= (1×2³)+(0×2²)+(1×2¹)+(0×2⁰)

= 8+0+2+0

= 10
```

---

## Program vs Process

### Program

A set of instructions stored on disk.

Examples:

* Chrome.exe
* VSCode.exe

### Process

A program currently being executed.

Example:

Opening Chrome creates a process.

| Program        | Process           |
| -------------- | ----------------- |
| Stored on Disk | Running in Memory |
| Passive        | Active            |
| Static         | Dynamic           |

---

## Compiler vs Interpreter

Programs must be translated into machine code.

### Compiler

Translates entire program before execution.

Examples:

* C
* C++

**Pros:** Faster execution

**Cons:** Compilation required

### Interpreter

Translates code line-by-line.

Examples:

* Python
* JavaScript

**Pros:** Easier debugging

**Cons:** Slower execution

### Java Approach

```text
Source Code
     ↓
Compiler
     ↓
Bytecode
     ↓
JVM
     ↓
Machine Code
```

This makes Java platform-independent.
# Why is Java Platform Independent?

## C++

```text
Source Code
    ↓
Compiler
    ↓
Windows Machine Code
```

The compiler generates machine code specific to the operating system.

A Windows executable works only on Windows.

---

## Java

```text
Source Code
    ↓
Java Compiler (javac)
    ↓
Bytecode (.class)
    ↓
JVM
    ↓
Machine Code
```

Java compiler does **not** generate Windows/Linux/Mac machine code directly.

Instead, it generates **Bytecode** (`.class` files).

The **JVM (Java Virtual Machine)** on each operating system converts the bytecode into machine code that the system understands.

```text
Same Bytecode
      ↓
Windows JVM → Runs
Linux JVM   → Runs
Mac JVM     → Runs
```

## Conclusion

Java is called **Platform Independent** because the same compiled bytecode can run on any operating system that has a JVM.

**Write Once, Run Anywhere (WORA)** is the main principle behind Java's platform independence.

