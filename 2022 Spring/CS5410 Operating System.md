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

<img src="./CS5410 Operating System.assets/image-20220129021238806.png?raw=true" style="zoom: 33%;" />

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

- **Modularity**->minimizes the connections between components
- **Abstraction**->separate interface from internals, sperate specification from implementation
- **Hierarchy**->easier to understand

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
- Glue: Offer set of commmon services, seperate apps from I/O devices. Decouples Hardware and app development

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

==*Phase* *1*== Computer expensive, human cheap

- Early Era (1945-1955)
- Batch Processing (1955-1960)
- Time-Sharing (1960-)
- Time-Sharing + Security (1965-)

*==Phase 2==* computer cheap, human expensive

- Personal Computers (1975-)
- Modern Era (1990-)



### 2.2 Support for Processes

#### 2.2.1 Hardware support

- objective of an O.S *kernel* is to manage and isolate multiple processes

  - kernel runs in **supervisor mode**

  - processes run in **user mode**

  - kernel maintains a **Process Control  Block (PCB)** for each process 

- Two architectures of O.S kernels

  <img src="./CS5410 Operating System.assets/image-20220127132414197.png?raw=true" style="zoom: 67%;" />

- Comparison

  <img src="https://github.com/daylightZhang/Computer-Science-Study-Notes/blob/main/2022%20Spring/CS5410%20Operating%20System.assets/image-20220127132605055.png?raw=true" alt="image-20220127132605055" style="zoom: 50%;" />

#### 2.2.2 How does the kernel get control?

- Boot(reset, power cycle, ...)
- Signals
  - Exceptions(Synchronous, Process misssteps, Privileged instructions)
  - System Calls(Synchronous/Non-maskable, User program requests OS services)
  - (Device or I/O) interrupts(Asynchronous/Maskable)

### 2.3 H/W interrupt Management

<img src=".\CS5410 Operating System.assets\image-20220129140352870.png" alt="image-20220129140352870" style="zoom: 50%;" />

- A CPU has only one device interrupt input
- An ***interrupt*** ***Controller*** manages interrups from multiple devices
  - Interrupts have descriptor of interrupting device
  - Priority selector circuit examines all interrupting devices, reports highest priority level to the CPU

#### 2.3.1 Interrupt Handling

- Two objectives:
  1. handle the interrupt and remove its cause
  2. restore what was running before the interrupt
- Two "actors" in handling the interrupt:
  1. the hardware goes first
  2. the kernel code takes control in *interrupt handler*

==Working Process/Principle==

- There is a supervisor SP and a user SP
  - both called SP
  - determined by "supervisor mode bit"
- On signal, hardware:
  1. disable("masks") devices interrupts
  2. sets supervisor mode
  3. pushes <font color="red">PC(IP), SP, and PSW </font>from before interrupt
  4. set PC to "signal handler"

**Reason for separating user SP / supervisor SP**

- user SP may be illegal
- user stack **may be not be large enough** and cause important data to be overwritten
- user may use SP for other things than stack
- security risks if only one SP
  - kernel could push sensitive data on user stack and unwittingly leave it there
  - process could corrupt kernel code or data by pointing SP to kernel address

**Interrupt Handling**

<font color="red">PSW(Processor Status Word)</font>:

| Supervisor mode bit | Interrupts enabled bit | condition codes |
| ------------------- | ---------------------- | --------------- |

"return from interrupt" instruction:

- hardware pops PC, SP, and PSW
- depending on contents of PSW
  - switch to user mode
  - Re-enable interrupts
- partly privileged: process cannot switch to supervisor mode or disable interrupts this way

### 2.4 Software support

**Saving Registers**

- On interrupt, the kernel needs to save the registers as the kernel code needs to use the registers to handle the interrupt
- Saving / restoring registers is expensive. Not all registers need to be saved (**SP, PC, PSW** is saved by *hardware*, other registers are saved by *software*)

<img src=".\CS5410 Operating System.assets\image-20220129141810001.png" alt="image-20220129141810001" style="zoom:50%;" />

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

#### 2.4.1 How kernel starts a new process

1. allocate and initialize a PCB
2. set up initial page table
3. push process arguments onto user stack
4. *simulate* an interrupt
   - push initial PC, user SP
   - push PSW
5. clear all other registers
6. return-from-interrupt

### 2.5 Support for Devices

Device management



Device Registers

- A device presents itself to the CPU as (pseudo) memory
- Simple example:
  - each pixel on the screen is a word in memory that can be written
- Devices define a range of *device registers*
  - accessible through LOAD and STORE operations

**Example: Disk Device**

- can only read and write blocks, not words
- registers:
  1. block number: which block to read or write
  2. memory address: where to copy block from/to
  3. command register: to start read/write operations
     - device interrupts CPU upon completion
  4. interrupt ack register: to tell device interrupt received
  5. status register: to examine status of operations

**Example: Network Device**

- register:
  1. receive memory address: for incoming packets
  2. send memeory address: for outgoing packets
  3. command register: to send/receive packet
     - device interrupts CPU upon completion
  4. interrupt ack register: to tell device interrupt received
  5. status register: to examine status of operations

### 2.6 Booting an O.S.

Steps:

- CPU starts at fixed address 
  - in supervisor mode with interrupts disabled
- BIOS (in ROM) loads "boot loader" code from specified storage or network device into memory and runs it
- boot loader loads O.S. kernel code into memory and runs it

**O.S. initialization**

1. determine location/size of physical memory
2. set up initial MMU / page tables 
3. initialize the interrupt vector
4. determine which devices the computer has
   - invoke device driver initialization code for each
5. initialize file system code
6. load first process from file system
7. start first process

<img src=".\CS5410 Operating System.assets\image-20220129144219701.png" alt="image-20220129144219701" style="zoom:50%;" />

### 2.7 Anatomy of computer

Architecture Diagram

<img src="./CS5410 Operating System.assets/image-20220202110830705.png" alt="image-20220202110830705" style="zoom:67%;" />

**Important notes with Bus**

- Control bus: Load/Store/Interrupt/...
- Data bus: x lines -> **word** is x bits
  - e.g: 32 lines: **word** is 32 bits (4 bytes)
- Address bus: y lines -> address is y bits
  - process can address at most $2^y$ bytes



## Lecture 3

### 3.1 Intro of process

Program VS program

- program consists of code and data
- typically stored in a file on disk
- "running a program" -> create a process

**What is a "Process"?**

- An executable running on an abstraction of a computer:
  - Address Space (memorry)+Execution Context (registers)
  - Environment (clock, files, network,...)

Good abstraction:

- is portable and hides implementation details
- has an intuitive and easy-to-use interface
- can be instantiated many times
- is efficient to implement

 **System Calls**

- A process runs on CPU
- Can access O.S. kernel through "system calls"
- Skinny interface
  - **Why?**
    - Portability
    - Security

<img src="./CS5410 Operating System.assets/image-20220204155633040.png" alt="image-20220204155633040" style="zoom: 33%;" />

### 3.2 Multiple processess

A proces physically runs on the CPU

Each process has its own

- Registers
- Memory
- I/O resources
- "thread of control"

**Process Control Block (PCB)**

- location in memory
- location of executation on disk
- which user is executing this process (uid)
- process identifier (pid)
- process status (running, waiting, finished, etc.)
- scheduling information
- interrupt stack
- save kernel SP (when process is not running)
- More...

**Invariants to keep in mind**

- At most 1 process is RUNNING at any time (per core)
- When CPU is in user mode, current process is RUNNING and its interrupt stack is empty
- If process is RUNNING
  - its PCB is not on any queue
  - however, not necessarily in user mode
- If process os RUNNABLE or WAITING
  - its interrupt stack is non-empty and can be switched to 
  - its PCB is either on the run queue or wait queue
- If a process is FINISHED
  - its PCB is on finished queue

**Cleaning up zombies**

- Process cannot clean up itself
- Process can be cleaned up 
  - by any other process
  - by parent when it waits for it
  - by dedicated "reaper" process

**Process of yidld/wait** (from process 1 to process2)

1. save kernel registers of process 1 on its interrupt stack
2. save kernel sp of process 1 in its PCB
3. restore kernel sp of process 2 from its PCB
4. resotre kernel registers from its interrupt stack

**Three "kinds" of context switches**

1. Interrupt: From user to kernel space
   - system call, exception, or interrupt
2. yield: between two processes **inside**
   - inside the kernel, switching from one PCB/interrupt stack to another
3. From kernel space to user space
   - Through a return_from_interrupt

Note that each involves a stack switch:

> 1. Px user stack -> Px interrupt stack
> 2. Px interrupt stack -> Py interrupt stack
> 3. Py interrupt stack -> Py user stack

**Creating and managing process**

| fork()          | create a child process as a clone of the current process. Return to both parent and child. Returns child pid to parent process, **0 to child process.** |
| --------------- | ------------------------------------------------------------ |
| exec(prog,args) | Run the application prog in the current process with the specified arguments (replacing any code and data that was in the process already) |
| wait(&status)   | Pause until a child process has exited                       |
| exit(status)    | Tell the kernel the current process is complete and shoule be garbega collected |
| kill(pid,type)  | Send an interrupt of a specified type to a process           |

**Code example**

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    int child_pid = fork();
    
    if (child_pid == 0) { // child process
        printf("I am process %d\n", getpid());
    } else {              // parent process
        printf("I am the parent of process %d\n", child_pid);
    }
    return 0;
}
```

Output:

```powershell
I am the parent of process 13125
I am process 13125
```

**Review**

- A **process** is an abstraction of a processor
- A **context** captures the running state of a process:
  - registers (including PC, SP, PSW)
  - memory (including the code, heap, stack)
- The **implementation** uses two contexts:
  - user context
  - kernel (supervisor) context
- A **Process Control Block (PCB)** points to both contexts and has other information about the process

- Modes of process:
  1. Initializing
  2. Running
  3. Runnable (on the "run" aka "ready" queue)
  4. Waiting (aka **Sleeping** or **blocked**)
  5. Zombie

---

**Load**

- length of the run queue
- On linux, it records
  - last 1 min, 5 min, 15 min

Example of **Load Average**

<img src="./CS5410 Operating System.assets/image-20220211143127297.png" alt="image-20220211143127297" style="zoom:80%;" />



---

### 3.3 Threads! (Chapter 25 - Chapter 27)



**Process VS. Thread Abstraction**

- A process is an abstraction of a computer 
  - CPU, memeory, devices
- A thread is an abstraction of a core
  - registers (including PC and SP)
- Process typically have their **own** (virtual) memory, but differen threads **share** virtual memory.
- Process tend to be mutually **distrusting**, threads must be mutually trusting.

**Preemotion(抢占)**

- Two kinds of threads:
  - non-preemptive: explicitly yield to other threads
  - preemptive: yield automatically upon clock interrupts
- Most moderen threading systems are preemptive

**Implementation of Threads**

1. "kernel threads": each thread has its own PCB in the kernel, but the PCBs point to the same physical memeory
2. "user threads": one PCB for the process; threads implemented entirely in user space. Each thread has its own Thread Control Block(TCB) and context.

**Kernel-level threads**

- separate PCB for each thread
- PCBs have:
  - same: page table base register
  - different: PC, SP, registers, interrupt stack

**User-level threads**

- Single PCB
- Thread Control Block (TCB) for each thread

Comparsion

<img src="./CS5410 Operating System.assets/image-20220211144909169.png" alt="image-20220211144909169" style="zoom:80%;" />

**Event-based programming**

- No "blocking operations"
  - No read(), wait(), lock(), etc.
- Code is a collection of event handlers
  - invoked when some event happens
  - runs to completion
  - (similar to I/O interrupt handlers)

<img src="./CS5410 Operating System.assets/image-20220211145814201.png" alt="image-20220211145814201" style="zoom:70%;" />



## Lecture 4

- mechanism: -context switch between processes
- policy: -scheduling: which process to run next



**Schedulers in the OS**

- <font color="red">CPU Scheduler</font> selects a process to run from run queue (Main focus)

- <font color="red">Disk Scheduler</font> selects next read/write operation
- <font color="red">Network Scheduler</font> selects next packet to send or process
- <font color="red">Page Replacement Scheduler</font> selects page to evict



**CPU Burst Prediction**

Approximate duration of the next CPU-burst

- Based on the durations of the past bursts
- Use past as a predictor of the future

*Use exponential moving average:*

$t_n$ actual duration of $n^{th}$ CPU burst

$\tau _{n}$ predicted duration of $n^{th}$ CPU burst

$\tau_{n+1}$ predicted duration of $(n+1)^{th}$ CPU burst
$$
\tau_{n+1}=\alpha \tau_n + (1-\alpha)t_n
$$
  where $0<= \alpha <=1$, $\alpha$ determines weight placed on past behavior



**Metrices**

<img src="./CS5410 Operating System.assets/image-20220219132738658.png" alt="image-20220219132738658" style="zoom: 50%;" />

**Context switch overhead**

- Cost of saving registers
- Plus cost of scheduler determining the next process to run
- Plus cost of restoring registers

**First In First Out (FIFO)**

Advantages:

- Simple, low-overhead, no starvation

Disadvantages:

- Average turnaround time very sensitive to schedule order
- Not responsive to interactive jobs

**Shortest Job First (SJF)**

Ad:

- Optimial average turnaround time

Disad:

- Pessimal variance in turnaround time
- Needs estimate of execution time
- Can starve long jobs

**Earliest Deadline First (EDF)**

Ad:

- Meets deadlines if possible
- Free of starvation

Disad:

- Does not optimize other metrics
- Cannot decide when to run jobs without deadlines

**Round Robin (RR)**

Ad:

- No starvation
- Can reduce response time

Disad:

- Context switch overhead
- Mix of I/O and CPU bound
- bad avg. turnaround time for equal length jobs

**Shortest Remaining Time First (SRTF)**

Ad:

- Good for response time and turnaround time of I/O-bound processes

Disad:

- Needs estimate of execution time of each job
- Suffers from starvation

**Completely Fair Scheduler (CFS)**

- Define "Spent Execution Time (SET)" to be the amount of time that a process has been executing

- Scheduler selects process with lowest SET

- let $\Delta$ be some time (typically, 50ms or so)

- let N be the number of processes on the run queue

- Process runs for $\frac{\Delta}{N}$ time

- If it uses up this quantum, reinsert into the queue

  ​	SET += $\Delta$ / N

- If a process is new or it sleeps and wakes up, then its SET is initialized to the minimum of the SETs of the processes on the run queue

**Multi-Level Feedback Queue (MLFQ)**

- Multiple levels of RR queue
- Jobs start at the top
- Periodically all jobs back to top
- Approximates SRTF

**Priority Inversion**

- Problem: some high priority process is waiting for some low priority process, maybe low priority process has a lock on some resource
- Solution: high priority process (needing lock) temporarily donates priority to lower priority process (with lock)

**Gaming the Scheduler**

- splitting app into multiple process
- periodically terminating and restarting
- yielding CPU just before quantum expires

**Multi-core Scheduling**

Desirables:

- Balanced load
- Scheduling affinity
- Avoid access contention

<img src="./CS5410 Operating System.assets/image-20220305183928265.png" alt="image-20220305183928265" style="zoom:50%;" />

<img src="./CS5410 Operating System.assets/image-20220305184034433.png" alt="image-20220305184034433" style="zoom:50%;" />



## Lecure 05 Critical Sections and locks



**Harmony Virtual Machine *State***

Contain three parts:

1. code (whcih never changes)
2. values of the shared variables
3. states of each of the running processes - "contexts"

Context (state of a process)

- Method name and parameters 
- PC (program counter)
- stack (+ implicit stack pointer)
- local variables
  - parameters (aka arguments)
  - result - there is no return statement
  - local variables - declared in **var, let** and **for** statement

**Non-determinism in Harmony**

Three sources:

1. **choose** expressions
2. thread interleavings
3. interrupts

<font color="red">Limitation</font>: models must be finite, **But** models are allowed to have cycles, executions are allowed to be unbounded.

**What is an invariant?**

A property that holds in all reachable states

A property is a set of states.







