# RISC-V Unix-like Operating System

A Unix-like operating system implemented in **C and RISC-V assembly**, featuring virtual memory, processes and threads, system calls, interrupt handling, ELF loading, VIRTIO devices, and filesystem support.

This project focused on understanding how the major components of an operating system interact—from low-level hardware interfaces and memory management to processes, system calls, and user-space execution.

---

## Overview

The system implements a small but complete operating-system stack on RISC-V.

Major components include:

- Virtual memory and page-table management
- Kernel and user address spaces
- Processes and user threads
- System calls
- Interrupt and trap handling
- ELF executable loading
- VIRTIO block-device support
- Filesystem support
- UART and other device interfaces
- User-space programs

The project required both implementing individual kernel subsystems and integrating them into a functioning system.

---

## Architecture

```text
                 User Programs
                      |
                 System Calls
                      |
        +-------------+-------------+
        |                           |
     Processes                    Threads
        |                           |
        +-------------+-------------+
                      |
               Virtual Memory
                      |
        +-------------+-------------+
        |                           |
  Interrupts / Traps            File System
                                    |
                              VIRTIO Block
                                    |
                              Virtual Disk
```

The operating system separates user-space execution from kernel functionality while providing controlled access to memory, devices, and filesystem operations through the system-call interface.

---

## My Contributions

My primary responsibilities were **Virtual Memory** and the **VIRTIO Block Device subsystem**.

I also participated in integration, cross-verification, debugging, and code review across the rest of the operating system. Because the project required full-system understanding for integration and the final technical demonstration, I worked with and debugged interactions between most major subsystems.

### Virtual Memory

I worked primarily on the virtual-memory subsystem, including the mechanisms needed to support protected kernel and application address spaces.

My work involved concepts such as:

- Page-table management
- Virtual-to-physical memory mappings
- Page allocation and mapping
- Memory permissions
- User and kernel address-space separation
- Application memory setup
- Memory cleanup and lifecycle management
- Debugging faults caused by invalid mappings or permissions

A major challenge was ensuring that memory-management behavior remained correct across interactions with process creation, executable loading, system calls, and user-space execution.

---

### VIRTIO Block Device

I was also primarily responsible for the **VIRTIO block-device implementation**, which provides the operating system with access to a virtual disk.

This involved working with:

- Memory-mapped VIRTIO device registers
- Device initialization and feature negotiation
- Virtqueue configuration
- Descriptor management
- Block read/write requests
- Device interrupts
- Request completion handling
- Synchronization between the driver and the rest of the kernel

The block-device driver serves as an important layer between the filesystem and the underlying virtualized hardware.

```text
Filesystem
    |
Block I/O Interface
    |
VIRTIO Block Driver
    |
Virtqueue / Descriptors
    |
Virtual Block Device
```

Implementing this subsystem required reasoning about both **hardware-visible data structures** and the higher-level kernel interfaces consuming the device.

---

## Virtual Memory

The memory subsystem provides address translation and isolation between different execution contexts.

At a high level:

```text
Virtual Address
      |
      v
Page Table Walk
      |
      v
Physical Address
```

The system manages mappings for both kernel memory and user applications.

Important responsibilities include:

- Creating and modifying page tables
- Mapping application code and data
- Assigning appropriate permissions
- Maintaining isolation between user and kernel memory
- Supporting process execution
- Handling invalid memory accesses safely

Virtual memory became one of the central integration points of the project because processes, ELF loading, system calls, and user execution all depend on correct address-space configuration.

---

## Processes & Threads

The operating system includes support for concurrent execution through kernel threads and user processes.

The relevant subsystem handles:

- Thread creation
- Scheduling-related state
- Execution contexts
- Process creation
- User-program execution
- Process lifecycle management

Low-level assembly is used where necessary to transition between execution contexts and interact with processor state.

Although this was not my primary subsystem, I worked with and debugged these components during system integration and cross-verification.

---

## System Calls

User applications interact with the kernel through a system-call interface.

The system-call layer connects user-space requests to kernel functionality such as:

- Process operations
- I/O
- Filesystem access
- Memory-related operations
- Device-backed functionality

System-call handling also requires careful validation because requests originate from user space while executing privileged kernel operations.

I participated in debugging and verifying syscall behavior as part of the full-system integration process.

---

## Interrupts & Traps

The kernel handles hardware and software events through its interrupt and trap infrastructure.

This includes interactions with:

- Processor traps
- External interrupts
- Timers
- Device interrupts
- VIRTIO completion notifications

Interrupt handling was especially important for the VIRTIO block-device implementation because I/O operations depend on correctly coordinating device requests and completions.

---

## VIRTIO Devices

The system contains support for virtualized I/O devices, including a block device used by the filesystem.

VIRTIO provides a standardized interface between the guest operating system and virtual hardware.

The block-device path can be viewed as:

```text
Kernel Request
      |
      v
VIRTIO Descriptor Chain
      |
      v
Available Ring
      |
      v
Virtual Device
      |
      v
Used Ring
      |
      v
Interrupt / Completion
```

This subsystem gave me hands-on experience with the boundary between **operating-system software and virtualized hardware**.

---

## Filesystem

A filesystem layer is built on top of the block-device interface.

It provides higher-level operations while relying on the VIRTIO block driver for persistent storage access.

Conceptually:

```text
User Program
     |
System Call
     |
Filesystem
     |
Block Device
     |
VIRTIO
```

Although the filesystem was not my primary ownership area, I participated in integration and debugging between the filesystem and the block-device layer.

---

## ELF Loading & User Space

The kernel supports loading user executables in ELF format.

Launching a user application requires cooperation between several subsystems:

```text
ELF File
   |
ELF Loader
   |
Virtual Memory Setup
   |
Process / Thread Creation
   |
User Execution
```

This integration demonstrates how memory management, filesystem access, processes, and executable loading work together to create a running user program.

---

## System Integration

One of the most valuable parts of the project was debugging interactions between independently developed OS components.

My involvement extended beyond my primary Virtual Memory and VIRTIO responsibilities through:

- Cross-verifying teammates' subsystem implementations
- Making integration fixes
- Debugging interactions between kernel components
- Understanding subsystem interfaces
- Testing full-system behavior
- Preparing for the final technical demonstration

Because failures in operating systems frequently cross subsystem boundaries, debugging required understanding the overall architecture rather than only the code owned by one team member.

---

## What I Learned

### Hardware–software interfaces

Implementing VIRTIO required understanding how software communicates with hardware through registers, descriptors, queues, interrupts, and shared memory structures.

### Virtual memory is a system-wide dependency

Memory management cannot be treated as an isolated subsystem. Processes, ELF loading, user execution, system calls, and device interactions all depend on correct address-space behavior.

### OS bugs often cross abstraction boundaries

A failure observed in a filesystem call may originate in a block driver, synchronization mechanism, page mapping, or syscall path.

Debugging therefore requires reasoning across multiple layers of the system.

### Integration matters as much as individual components

A subsystem can work correctly in isolation and still fail after being connected to the rest of the kernel.

Cross-verification and integration testing were therefore a major part of the engineering process.

---

## Technologies

**Languages**

`C` · `RISC-V Assembly`

**Operating Systems**

`Virtual Memory` · `Paging` · `Processes` · `Threads` · `System Calls`

**Low-Level Systems**

`Interrupts` · `Traps` · `ELF Loading` · `Device Drivers`

**I/O**

`VIRTIO` · `Block Devices` · `Filesystem`

**Architecture**

`RISC-V`

---

## Repository Scope

This repository is a curated technical showcase of the operating-system project.

The original implementation was developed inside a larger academic course framework containing starter code and collaborative team code. To respect course policies and clearly distinguish original work from provided infrastructure, this public repository focuses on architecture, subsystem design, implementation experience, and engineering analysis rather than reproducing the entire original codebase.

---

## Team Project

This operating system was developed collaboratively as a team project.

My **primary implementation responsibilities** were:

- **Virtual Memory**
- **VIRTIO Block Device**

I also contributed to integration, debugging, cross-verification, and modifications across other subsystems and developed a working understanding of the complete operating-system architecture for final system verification and demonstration.

---

## Author

**Yuxuan (Neal) Guo**

Interested in **computer systems, GPU computing, AI/ML systems, and low-level software engineering**.
