# ECE 5720 Parallel Computing

[TOC]

## Lecture 1

<font color="red">Need for fast computers in engineering</font>

- data mining, machine learning, computational finance
- video games, medical imaging, drug design, etc

Parallelism is not a new idea

- 1960's theory
- Early parallel computers
- VLSI revolution of 80's
- parallelism in CPU invisible to the programmer

**Cost of Algorithms**

- cost of arithmetic/logic operations 运算，例如加减乘，位移
- cost of moving data - local vs. remote read/write 移动数据所需的时间
- cost of control 控制所需

<font color="red">Potential Question here</font>

### 1.1 Most common "walls"

#### 1.1.1 Memory Wall

Time to fetch data from ouside the CPU chip is much greater than time per flop

- long latency and limited communication bandwidth beyond the CPU chip
  $$
  T_{move\_data}=latency+\frac{bytes}{bandwidth}
  $$
  **Latency**->data from start to reponse

  **Bandwidth**->bytes transferred per unit of time

  time/operation < time/data moves on chip \<\< time/data moves off chip <font color="red"><font color="red">Potential Question here</font></font>

#### 1.1.2 Power Wall

- Faster speed -> higher clock frequency
- Power dissipation is proportional to clock frequency and feature length
- Increase in clock speed without expensive cooling is difficult to achieve

#### 1.1.3 Instruction Level Parallelism (ILP) Wall

- concurrent execution of independent instructions on duplicate hardware
- one needs to exmine large subblocks of instructions to find independent ones (done mostly in hardware)**DO NOT UNDERSTNAD**
- large and complex execution units with diminishing returns

### 1.2 Caches

Construct an intermediate small but fast storage **(cache)** between registers and main memory. Use cache to store **frequently** **used** **data**.

<img src=".\ECE5720_Parallel_Computing_notes.assets\image-20220129221940275.png" alt="image-20220129221940275" style="zoom:80%;" />

Several levels of larger caches

- L1 on chip, 32KB, 64KB, 128KB <font color="red">Only L1 on the chip</font>
- L2 (256KB, 512KB, 1MB) and L3 (vary from 4MB to 32MB) off chip
- This improves the "Average Memory Access Time" (AMAT)

#### 1.2.1 How can caches help?

- **spatial** **locality**

  if a data item is accessed, it is likely that a nearby data will be accessed next -> fetch multiple bytes (a cache line) all at once

- **temporal** **locality**

  if a data item is accessed, it is likely it will be used again -> hides memory access costs by reusing data

The above operation is mostly automatic and implicit (But programmers can help)

<font color="red">Potential Question here</font>

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

<font color="red">Potential Question here</font>

- Less sets, more slots per set (longer tags)
- Higher associativity (8 is common) higher cost (searching a matching tag costs more)

#### 1.2.3 Matching tags

![image-20220129231418133](.\ECE5720_Parallel_Computing_notes.assets\image-20220129231418133.png)

- Cache **hit** when copy of needed data in cache
- Cache **miss** otherwise

<font color="red">Potential Question here</font>

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



For block size, we need to make sure A, B, C must fit in fast memory, Therefore
$$
3b^2<=M \to q\approx b <= \left(\frac{M}{3} \right)^{\frac{1}{2}}
$$
Thus
$$
t_{total}\approx N_{t_{fl}} \left(1+\frac{200 \sqrt3}{\sqrt M} \right)
$$
**TH (Kung, 1981)** Any reorganization of this matrix-matrix multiply algorithm is bounded by $q=O(M^{\frac{1}{2}})$.



**Implications**

- data locality
  - accessing (remote) data is the most expensive operation
  - reuse of recent or nearby data 

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

<font color="red">potential quiz problem</font>

- SISD - single instruction, single data

  - One instruction stream is acted on by a single PE
  - Deterministic execution (result will not change)
  - Oldest type of computer

- SIMD

  <img src=".\ECE5720_Parallel_Computing_notes.assets\image-20220127150142241.png" alt="image-20220127150142241" style="zoom:50%;" />

  - Operates on arrays od data
  - Elements of an array are acted opon by the same single instruction but by different PEs
  - PEs execute same instructions at the same cycle (lock step execution)
  - PEs operate on different elements
  - Specialized for problems with high degree of regularity

- MISD ?

- MIMD - multiple instruction, multiple data

  <img src=".\ECE5720_Parallel_Computing_notes.assets\image-20220127150418425.png" alt="image-20220127150418425" style="zoom:50%;" />
  
  - Every PE may be executing a different instruction possibly on different data at its own pace.
  - Synchronous or asynchronous execution, possibliliy of <font color="red">race condition</font>
  - Common gobal address space visible to all PEs
  - Communication via shared data
  - Cache coherence mechanism required

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
- NUMA (non-uniform memory access) times

**Granularity**: Task's computation to communication time ratio.

- Coarse: large amount of computation of work is done between communication events - often distributed memory.
- Fine: small amount of computational work is done between communication events - often shared memory

### 2.3 Performance metrics



Speed-up: Ratio of sequential, $t_seq$, to parallel, $t_{par}(n)$, execution times with $n$ PEs,
$$
s_n=\frac{t_{seq}}{t_{par}(n)}
$$

**Efficiency**: how well the resources are utilized
$$
e_n=\frac{s_n}{n}=\frac{t_{seq}}{n\cdot t_{par}(n)}
$$
**Scalability**: tell whether the algorithm can handle a growing amount of work or PEs efficiency.

- Can increase of the number of PEs increase the speed-up?
- Can a parallel system keep efficiency by increasing the number of PEs and the problem size, simultaneously?

**Amdahl's Law**: For a <font color="red">fixed size workload</font> the speedup is determined by the fraction of sequential code.



When $q=\frac{t_{fast}}{t_{slow}}<0.1$, a single fast PE always better then $n$ slow PEs.

- For a fixed wordload, speed-up is determined by the fraction $\alpha$ of parallel code
  $$
  s_n<=\frac{1}{1-\alpha}
  $$

- 

<font color="red">potential quiz question</font>

**Gustafson's law** if $n$ PEs execute in time $t_{par}$ with $\alpha$ fraction of parallel code then the speedup over a single PE is 
$$
s_n=\frac{t_{seq}}{t_{par}}=(1-\alpha)+\alpha\cdot n \to \infty
$$
**PRAM**

Parallel Random Access Memory



## Lecture 3



**mutex**



## Lecture 5





## 

