# ECE 5720 Parallel Computing

[TOC]

## Lecture 1



Parallelism is not a new idea

- 1960's theory
- Early parallel computers
- VLSI revolution of 80's
- parallelism in CPU invisible to the programmer

## Lecture 2

### 2.1 Terminology

- **PE**: any physical computational device
- **Task**: a unit of execution
- **Stream**: is a set of instructions executed in sequence
- **Parallel Task**: is a set of tasks that are executed concurrently by multiple PEs
- **Communication**:
- **Shared Memory**:
- **Distributed memory**: PE have direct access only to their own local data
- **Synchronization**: coordination of parallel tasks

### 2.2 Parallel computing models

**Soft issues need to be considered**

- Control
- Data sharing
- Synchronization
- Cost

**Flynn's classification**

- SISD - single instruction, single data

- SIMD

  <img src="C:\Users\Zhangjingkai\OneDrive - Cornell University\Course Notes\2022 Spring\ECE5720_Parallel_Computing_notes.assets\image-20220127150142241.png" alt="image-20220127150142241" style="zoom:50%;" />

- MISD ?

- MIMD - multiple instruction, multiple data

  <img src="C:\Users\Zhangjingkai\OneDrive - Cornell University\Course Notes\2022 Spring\ECE5720_Parallel_Computing_notes.assets\image-20220127150418425.png" alt="image-20220127150418425" style="zoom:50%;" />

Shared memory threads model

![image-20220127150942291](C:\Users\Zhangjingkai\OneDrive - Cornell University\Course Notes\2022 Spring\ECE5720_Parallel_Computing_notes.assets\image-20220127150942291.png)

**Shared memory**

Advantage of shared memory:

- golobal address space, each PE can see the global data
- communication by shared variables

Disadvantage of shared memory:

- not scalable, more CPUs increase traffic on the memory-CPU path
- increased cache / memory management time
- programmer responsible for maintaining correct access to shared data

**Distributed memory**

Advantages:

- memory scales well when increasing the number of PEs
- no overhead for maintaining cache memory

Disadvantages:

- programmer is responsible how and when data is communicated between PEs
- NUMA times









### 2.3 Performance metrics



Speed-up: Ratio of sequential, $t_seq$, to parallel, $t_{par}(n)$, execution times with $n$ PEs,
$$
s_n=\frac{t_{seq}}{t_{par}(n)}
$$
