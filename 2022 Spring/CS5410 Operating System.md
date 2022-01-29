# CS5410 Operating System



[TOC]

## Lecture 1

### 1.1 What is OS?

- OS is an ==intermediary== between programs and hardware
- OS creates an environment to execute programs
  - allocate resources(CPU,storage)
  - controls programs
    - cooperation (sharing and synchronization)
    - isolation (protection and resource management)

<img src="https://github.com/daylightZhang/Study-Notes/blob/main/2022%20Spring/CS5410%20Operating%20System.assets/image-20220129021238806.png?raw=true" style="zoom: 33%;" />

View the OS in two ways:

- **Services** it provides to programs
- **Components** implementing those services

Motivation to learn OS:

- Resource sharing (scheduling)
- Cooperation (concurrent programming: communication, synchronization)
- System sturcture (abstractions, interfaces)

### 1.2 Difference between *Systems* and *Programs*

#### 1.2.1 What OS cares:

- **Measure** **of** **success**: OS concerned with **extensibility, security, reliability**
- **External** **interface**: OS more complicated and subject to change E.g I/O devices
- **Structuring** **techniques**: OS employs modules, layers, client-server, event-handler, transction

#### 1.2.2 OS must bridge mismatched performance characteristics

- Registers vs RAM vs Disk
- Phone vs Laptop vs Server

#### 1.2.3 OS is more complicated:

- OS became very complicated when components are **combined**.

- **Propagation** **of** **effects**: small changes have disproportionate effects

- **Incommensurate** **scaling**: different parts follow different scaling rules 

#### 1.2.4 How to manage complexity

- Modularity->minimizes the connections between components
- Abstraction->separate interface from internals, sperate specification from implementation
- Hierarchy->easier to understand

#### 1.2.5 Potential rules that OS can have

- Referee: Manages shared resources
  - Resource allocation
  - Isolation
  - Communication/Coordination
- Illusionist: Virtual memory, multithread
  - Virtualization
  - Abstraction 
    - Atomic operation - hardware guarantee atomicity at word level
    - Reliable communication channels
- Glue: Offer set of commmon services, seperate apps from I/O devices

### 1.3 Issue in OS design

- Sturcture: how to organize OS?
- Concurrency: how are parallel activities created and controlled?
- Sharing: how are resources shared?
- Naming: how are resources named by users?
- Protection: how are distrusting parties protected from each other?
- Security: how to authenticate, authorize, and ensure privacy?
- Performance: how to make it fast?  

- Reliability: how to deal with failures?
- Portability: how to write once, run anywhere?
- Extensibility: how do we add new features?
- Communication: how do we exchange information?
- Scale: what happens as demands increase?
- Persistence: how do we make information outlast the processes that created it?
- Accounting: who pays the bill and how do we control resource usage? 

## Lecture 2

### 2.1 History of Operating System



### 2.2 Hardware support for processes

- objective of an O.S *kernel* is to manage and isolate multiple processes

  - kernel runs in **supervisor mode**

  - processes run in **user mode**

  - kernel maintains a **Process Control  Block (PCB)** for each process 

- Two architectures of O.S kernels

  <img src="https://github.com/daylightZhang/Computer-Science-Study-Notes/blob/main/2022%20Spring/CS5410%20Operating%20System.assets/image-20220127132414197.png?raw=true" style="zoom: 67%;" />

- Comparison

  <img src="https://github.com/daylightZhang/Computer-Science-Study-Notes/blob/main/2022%20Spring/CS5410%20Operating%20System.assets/image-20220127132605055.png?raw=true" alt="image-20220127132605055" style="zoom: 50%;" />

### 2.3 How does the kernel get control?

- Boot(reset, power cycle, ...)
- Signals
  - Exceptions(Synchronous, Process misssteps, Privileged instructions)
  - System Calls(Synchronous/Non-maskable, User program requests OS services)
  - (Device or I/O) interrupts(Asynchronous/Maskable)

### 2.4 H/W interrupt Management

- A CPU has only one device interrupt input
- An *interrupt* Controller

### 2.5 Interrupt Handling

- Two objectives:
  1. handle the interrupt and remove its cause
  2. restore what was running before the interrupt
- Two "actors" in handling the interrupt:
  1. the hardware goes first
  2. the kernel code takes control in *interrupt handler*

### 2.6 Interrupt Handling (conceptually)

- There is a supervisor SP and a user SP
  - both called SP
  - determined by "supervisor mode bit"
- On signal, hardware:
  1. disable("masks") devices interrupts
  2. sets supervisor mode
  3. pushes PC(IP), SP, and PSW from before interrupt
  4. set PC to "signal handler"

**Reason for separating user SP / supervisor SP**

- user SP may be illegal
- user stack may be not be large enough and cause important data to be overwritten
- user may use SP for other things than stack
- security risks if only one SP
  - kernel could push sensitive data on user stack and unwittingly leave it there
  - process could corrupt kernel code or data by pointing SP to kernel address

**Interrupt Handling**

PSW(Processor Status Word)

"return from interrupt" instruction:

- hardware pops PC, SP, and PSW
- depending on contents of PSW
  - switch to user mode
  - Re-enable interrupts
- partly privileged: process cannot switch to supervisor mode or disable interrupts this way

**Saving Registers**

- On interrupt, the kernel needs to save the registers as the kernel code needs to use the registers to handle the interrupt
- Saving / restoring registers is expensive. Not all registers need to be saved (**SP, OC, PSW** is saved by *hardware*, other registers are saved by *software*)

**Example of Handler in C**

```c
#define CLK_DEV_REG 0xFFFE0300

void handleClockInterrupt(){
    int *cdr = (int*)CLK_DEV_REG;
    *cdr = 1;     // turn off clock interrupt
    scheduler()   // run another process?
}
```

**Example of System Call Handler in C**

```C
struct pcb *current_process;

int handle_syscall(int type){
    switch(type){
        case GETPID: return current_process->pid;
            ...
    }
}
```

### 2.7 How kernel starts a new process

1. allocate and initialize a PCB
2. set up initial page table
3. push process arguments onto user stack
4. *simulate* an interrupt
   - push initial PC, user SP
   - push PSW
5. clear all other registers
6. return-from-interrupt

## Lecture 3