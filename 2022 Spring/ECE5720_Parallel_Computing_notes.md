# ECE 5720 Parallel Computing

[TOC]

## Lecture 1



Parallelism is not a new idea

- 1960's theory
- Early parallel computers
- VLSI revolution of 80's
- parallelism in CPU invisible to the programmer

**Cost of Algorithms**

- cost of arithmetic / logic operations
- cost of moving data - local vs. remote read / write 
- cost of control

### 1.1 Most common "walls"

#### 1.1.1 Memory Wall

Time to fetch data from ouside the CPU chip is much greater than time per flop

- long latency and limited communication bandwidth beyond the CPU chip
  $$
  T_{move\_data}=latency+\frac{bytes}{bandwidth}
  $$
  **Latency**->data from start to reponse

  **Bandwidth**->bytes transferred per unit of time

  time/operation < time/data moves on chip \<\< time/data moves off chip 

#### 1.1.2 Power Wall

- Faster speed -> higher clock frequency
- Power dissipation is proportional to clock frequency and feature length
- Increase in clock speed without expensive cooling is difficult to achieve

#### 1.1.3 Instruction Level Parallelism (ILP) Wall

- concurrent execution of independent instructions on duplicate hardware
- one needs to exmine large subblocks of instructions to find independent ones (done mostly in haardware)**DO NOT UNDERSTNAD**
- large and complex execution units with diminishing returns

### 1.2 Caches

Construct an intermediate small but fast storage **(cache)** between registers and main memory. Use cache to store **frequently** **used** **data**.

<img src=".\ECE5720_Parallel_Computing_notes.assets\image-20220129221940275.png" alt="image-20220129221940275" style="zoom:80%;" />

Several levels of larger caches

- L1 on chip, 32KB, 64KB, 128KB
- L2 (256KB, 512KB, 1MB) and L3 (vary from 4MB to 32MB) off chip
- This improves the "Average Memory Access Time" (AMAT)

#### 1.2.1 How can caches help?

- **spatial** **locality**

  if a data item is accessed, it is likely that a nearby data will be accessed next -> fetch multiple bytes (a cache line) all at once

- **temporal** **locality**

  if a data item is accessed, it is likely it will be used again -> hides memory access costs by reusing data

The above operation is mostly automatic and implicit (But programmers can help)

#### 1.2.2 Cache organization

<img src=".\ECE5720_Parallel_Computing_notes.assets\image-20220129224832294.png" alt="image-20220129224832294" style="zoom:80%;" />

Capacity **C**, Line size **b,** Degree of associativity **N**
$$
lines=\frac{C}{b}\\
sets=\frac{C}{N}
$$

- a collection of **sets**
- each set is a collection of $N>=1$ **lines** (degree of associativity)
- each line contains b **words**

#### 1.2.3 Matching tags

![image-20220129231418133](.\ECE5720_Parallel_Computing_notes.assets\image-20220129231418133.png)

- Cache **hit** when copy of needed data in cache
- Cache **miss** otherwise

Miss types:

- **Compulsory**: first use of data
- **Capacity**: need more data than can fit in cache
- **Conflict**: insufficient associativity

#### 1.2.4 Average Memory Access Time

Caches lower than AMAT.
$$
AMAT=Hit_{time}(L1)+Miss_{rate}(L1)\cdot Miss_{penalty}(L1)\\
Miss_{penalty}(L1)=Hit_{time}(L2)+Miss_{rate}(L2)\cdot Miss_{penalty}(L2)
$$

### 1.3 Arrays

```c
// columnwise
for (i = 0; i < 4; i++) {
    for (j = 0; j < 4; j++) {
        x[j][i] = x[j][i] + 1.0;
    }
}
// rowwise
for (i = 0; i < 4; j++) {
    for (j = 0; j < 4; i++) {
        x[j][i] = x[j][i] + 1.0;
    }
}
```

- columnwise

  If there are 4 words in a cache line there will be 4 misses in 4 reads

- rowwise

  If there are 4 words in a cache line there will be 1 misses in 4 reads

Execution time depends **on memory access pattern**

### 1.4 Single CPU simple performance model

$$
Time: N\cdot t_{fl}+K\cdot t_{mem}=N\cdot t_{fl}\left(1+\frac{K}{N} \cdot \frac{t_{mem}}{t_{fl}}\right)=N\cdot t_{fl}\left(1+\frac{1}{q} \cdot \frac{t_{mem}}{t_{fl}}\right)
$$

Larger $q=\frac{N}{K}$ is better

$K$ - words read from slow memory

$t_{mem}$ - slow memory access time

$N$ - number of flops

$t_{fl}$ - time per flop

$q=\frac{N}{K}$ - average flops / slow memory access, larger $q$ the faster the algorithm

smaller $\frac{t_{mem}}{t_{fl}}$ , faster the machine is.







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

  <img src=".\ECE5720_Parallel_Computing_notes.assets\image-20220127150142241.png" alt="image-20220127150142241" style="zoom:50%;" />

- MISD ?

- MIMD - multiple instruction, multiple data

  <img src=".\ECE5720_Parallel_Computing_notes.assets\image-20220127150418425.png" alt="image-20220127150418425" style="zoom:50%;" />

Shared memory threads model

<img src=".\ECE5720_Parallel_Computing_notes.assets\image-20220127150942291.png" alt="image-20220127150942291" style="zoom:50%;" />

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
