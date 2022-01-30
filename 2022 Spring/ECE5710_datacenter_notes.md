# ECE5710 Datacenter

[TOC]

## Lecture1

### 1.1 Questions when reading paper

- What is the problem the paper tackles?
- Why is it important?
- How do thy tackle it?
- Advantages/disadvantages?
- Open questions for future work?
- Broader implications?

### 1.2 What is a Datacenter?

- The **compute infrastructure** for internet-scale services & cloud computing
- Both consumer and enterprise services

***A simplistic view***

- scaled-up/out version of machine rooms for enterprise computing
- A large collection of commodity components
  - PC-based servers (CPUs, DRAM, disks)
  - Ethernet networking
  - Commodity OS and software stack
  - 10s to 100s of thousands of nodes
- High-bandwidth networking (10Gbps -> 40 Gbps -> 100 Gbps)
- Power delivery, cooling, UPS (**50-200MW per DC**)
- System software for DC management (centralized or distributed)
- Software that implements internet services

### 1.3 Why we need a datacenter?

- Some applications need big machines
- Improve the user experience
  - flexibility, cost-efficiency
- Vendor benefits
  - faster application development
  - lower cost

### 1.4 DC Hardware

- Computer & Memory
  - A PC-based server (most of them x86)
  - Custom Linux OS (optimized for latency)
  - 1U, 2U, or blade from factors are popular
    - E.g. 2 processor sockets, tens of GB of DRAM, 2 disks, ...
- Networking
  - 10Gbps ehernet links to rack switch
  - A few 10 Gbps links to cluster level switches

- Storage

  - Distributed file system using disks on servers (GFS)

    - better fault-tolerance across nodes, lower cost, better scalability

  - Netork attached storage (NAS) devices

  - **Interesting** **observations**

    - Remote memory is often faster than locak disk

    - Bandwidth bottlenecks

      <img src=".\ECE5710_datacenter_notes.assets\image-20220129174924097.png" alt="image-20220129174924097" style="zoom:50%;" />

      **The network's latency is lower than disk's**

## Lecture2

- project list will come soon on Piazza
- sign up for paper presentation
- get permission number before Jan 27, if not, send email

### 2.1 Total Cost of Ownership (TCO)

- The key metric in datacenters
  - Scalable capabilities at low TCO (large-scale needs low cost)
  - **Key** competitive advantage
- TCO components
  - Capital expenses (CAPEX)
    - Facilities, compute, storage, networking
  - Operational expenses (OPEX)
    - Energy, maintenance, employees
- Keep in mind
  - Facilities & equipment amortized over a certain period
  - cost of money (interest rate)

#### 2.1.1 Efficiency in DCs

- PUE (Power Usage Effectiveness) standard DC metric for how much power goes towards useful work
  $$
  PUE = \frac{Total Facility Power}{IT Equipment Power}
  $$

- Idealy PUE=1.0 (no overhead from cooling)
- Used to be ~2.0
- Best today 1.07 (Facebook), 1.12 (Google)

### 2.2 Cost Model: System CAPEX and OPEX

- Servers = Monthly 2000K $ CAPEX 
- Networking = Monthly 309K $ CAPEX
- Power = Monthly 475K $ OPEX
- People = 85K $ OPEX
- Network bandwidth costs to internet
- Vendor maintenance fees + sysadmins

<img src="image/ECE5710_datacenter_notes/1643227244916.png" style="zoom:50%" />

Summary:

- 34% costs related to power 
- Networking high 8% of overall costs; 15% of server costs

#### 2.2.1 Enterprise Vs Internet-scale Computing: a Cost Perspective

- Enterprise computing approach
  - Largest cost is people (~100:1)
  - Enterprise interests focus on cosolidation & utilization

- Internet-scale services approach
  - Largest costs is server H/W
    - people cost under 10% & often under 5%
    - followed by cooling, power, distribution, power
  - Services intersts centered around work-done-per-$(or watt)
- Obvervations
  - People cost shifts from top to nearly irrelevant
  - Focus instead on work done / $ & work done/watt

#### 2.2.2 Cost analysis

Datacenter Applications

- Batch application (cares about **throughput**)
- Interactive services (cares about tail **latency**)
- Native processes, containers, or virtual machines

#### 2.2.3 Example 3-tier App: WebMail

<img src=".\ECE5710_datacenter_notes.assets\image-20220129183956771.png" alt="image-20220129183956771" style="zoom: 67%;" />

- 1st tier: protocol processing
- 2nd tier: application logic
- 3rd tier: data storage

**Why Tiered Application?**

- Simplifies aoolication application
- Simplifies performance tuning
- Simplifies reliable operation

Disadvantages?

- High request level application
- High workload chun
- Platform homogeneity
- Require fault-free operation

### 2.3 Performance Metrics

- Throughput
- Quality of Service (QoS)
  - Latency of individual requests (90th, 95th, 99th percentile)
- Interesting notes
  - distribution matters, not just average
  - Optimizing thoughput often hurts latency
    - optimizing latency often hurts power consumtion

**Reason for Variability**

- Interference
- Heterogeneity
- Power limits & energy management
- Input load
- Often hurt avg performance as well

**Reducing Variability**

- Differentiate service classes
- Overprovisioning
- Reduce head-of-line blocking

- Manage background tasks

**Reducing Tail Latency**

- Accept service time variability and work around it
- Replicate requests

- Address tail-latency through long-term techniques



### 2.4 What else is running in a DC

- Platform-level software
- Cluster-level infrastructure
- Application services

















