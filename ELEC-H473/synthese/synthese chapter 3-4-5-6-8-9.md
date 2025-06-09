# Microprocessor Architecture Course Overview

## Chapters 3, 4, 5, 6, 8, 9

_Based on ELEC-H473 by Professor Dragomir Milojevic_  
_Compiled from Achten Alexandre's course materials_

---

## Chapter 3: Basic Processor Architecture

### 3.1 Basic Computer Architecture Concepts

**Definition of a Computer:** According to John von Neumann (1945), computers differ from electronic calculators because they "store electronically the information that controls the computational process."

**Key Components:**

- **ALUs** implement arithmetic operations (adders, multipliers, dividers) through dedicated circuits or microcoded complex operations
- **Register File (RF)** connected to ALU through multiplexers for operand selection
- **Control Unit (CTRL)** interprets instructions and generates control signals for hardware operation

### 3.2 Minimum System Architecture

A computer is defined as: **Microarchitecture + ISA**

**Von Neumann Architecture Elements:**

- Central Processing Unit (CPU)
- Memory system
- Input/Output interfaces
- Interconnection system (buses)

### 3.3 Instruction Execution Cycle

**Four-Stage Pipeline (F-D-Ex-W):**

1. **Fetch (F):** Retrieve instruction from memory using Program Counter (PC)
2. **Decode (D):** Interpret instruction opcode and prepare operands
3. **Execute (Ex):** ALU performs the selected operation on operands
4. **Write (W):** Store result in destination register or memory

**Performance Equation:**

```
tCPU = NInst × CPI × (1/FCLK)
```

Where:

- NInst = Number of instructions
- CPI = Cycles per instruction
- FCLK = CPU clock frequency

### 3.4 Different ISAs: RISC vs. CISC

**RISC Philosophy:**

- Simplified instructions requiring only one clock cycle
- Load/store architecture (separate memory operations)
- Increases NInst but reduces CPI and improves FCLK
- Optimized for general-purpose servers and mobile computing

**CISC Philosophy:**

- Complex instructions that can load, compute, and store in one instruction
- Decreases NInst but increases CPI and may reduce FCLK
- Rich instruction sets with variable execution cycles
- Used in high-performance computing and supercomputers

### 3.5 Pipeline Execution

**Critical Path Reduction:**

- Breaking large circuits into smaller subcircuits connected by Flip-Flops
- Reduces critical path delay, enabling higher operating frequency
- Introduces latency but improves instruction throughput

**Pipeline Acceleration Formula:** For N instructions and n pipeline stages:

- SIBM (Single Issue Base Machine): CSIBM = N × n
- Pipelined: Cpipe = n + N - 1
- Acceleration: Acc = Cpipe/CSIBM ≈ n (for N ≫ n)

**Instruction Level Parallelism (ILP):**

- **Instructions Per Cycle (IPC):** Maximum IPC = 1 for single ALU
- Pipeline efficiency measured by how well stages are filled
- Best performance when all pipeline stages take equal time

---

## Chapter 4: Super-scalar Architecture

### 4.1 Pipelined, Super-scalar Architectures

**Super-scalar Concept:**

- Multiple ALUs enable IPC > 1
- Two approaches:
    1. Duplicate complete pipelines (multi-core)
    2. Enhanced F/D stage feeding multiple execution units

**Super-scalar Acceleration:** For N instructions, n pipeline stages, m execution units:

```
CSSC = n + (N-m)/m
Acceleration = m (for N ≫ n,m)
```

### 4.2 Execution Hazards Overview

**Types of Dependencies:**

1. **Name Dependencies**
2. **Data Dependencies**
3. **Instruction Dependencies**
4. **Control Dependencies**

### 4.3 Name Dependencies

**WAR (Write After Read):** False dependency where write operation must wait for previous read

**WAW (Write After Write):** False dependency where second write must complete after first

**Solutions:**

- Register renaming
- Out-of-order execution with dependency tracking

### 4.4 Data Dependencies

**RAW (Read After Write):** True dependency where data must be written before it can be read

**Data Forwarding:** Hardware technique to pass results directly between pipeline stages without waiting for write-back

**Pipeline Stalls:** When dependencies cannot be resolved through forwarding

### 4.5 Branching and Control Dependencies

**Branch Prediction:** Hardware attempts to predict branch outcomes to avoid pipeline stalls

**Branch Penalties:** Performance cost when predictions are incorrect

**Solutions:**

- Static branch prediction
- Dynamic branch prediction with history tables
- Speculative execution

### 4.6 Loop Unrolling

**Concept:** Replicate loop body multiple times to reduce branch overhead and increase ILP

**Benefits:**

- Reduces branch instructions
- Exposes more parallelism
- Enables better instruction scheduling

**Trade-offs:**

- Increased code size
- More complex register management

---

## Chapter 5: Memory Subsystem

### 5.1 Memory Technologies

**SRAM (Static RAM):**

- Fast access (1-2 cycles)
- Expensive, large area
- Used for cache memories
- 6-transistor cell design

**DRAM (Dynamic RAM):**

- Slower access (100+ cycles)
- Cheap, dense
- Used for main memory
- 1-transistor + capacitor cell

**Performance Gap:** 100-1000x speed difference between CPU and DRAM

### 5.2 Memory Organization

**DRAM Structure:**

- Row/Column addressing with RAS/CAS signals
- Row buffer for efficient access
- Memory modules: SIMM (32-bit), DIMM (64-bit)

**DDR SDRAM:** Double Data Rate Synchronous DRAM with standardized protocols

### 5.3 Cache Memory and Hierarchy

**Cache Principle:** Insert fast SRAM between CPU and main memory

**Locality of Reference:**

- **Temporal Locality:** Recently accessed data likely to be accessed again
- **Spatial Locality:** Nearby data likely to be accessed soon

**Cache Performance Metrics:**

```
memStallCycles = NInstructions × CacheMissesPerInstruction × missCost
```

**Memory Hierarchy:**

- L1: Split instruction/data cache (Harvard architecture)
- L2, L3, L4: Unified caches (instructions + data)
- Last Level Cache (LLC) interfaces with DRAM

### 5.4 Implementing Caches

**Cache Organization:**

- **Cache entries** store data plus metadata
- **Tag bits** identify which memory address is cached
- **Valid bits** indicate if cache entry contains valid data

**Cache Mapping:**

- **Direct mapped:** One possible location per address
- **Set associative:** Multiple possible locations
- **Fully associative:** Any location possible

**Cache Policies:**

- **Replacement:** LRU, FIFO, Random
- **Write:** Write-through, Write-back

### 5.5 Memory Management

**Virtual Memory:** Provides address space larger than physical memory

**Translation Lookaside Buffer (TLB):** Cache for address translation

**Memory Protection:** Prevents unauthorized access to memory regions

---

## Chapter 6: Data Parallelism - SIMD

### 6.1 Data Parallelism Motivation

**SIMD Concept:** Single Instruction, Multiple Data - same operation on multiple data elements simultaneously

**Flynn's Classification:**

- **SISD:** Single Instruction, Single Data (traditional)
- **SIMD:** Single Instruction, Multiple Data (vector processing)
- **MIMD:** Multiple Instruction, Multiple Data (multiprocessor)

### 6.2 SIMD Extensions in Intel CPUs

**Evolution of SIMD:**

- **MMX:** 64-bit registers, integer operations
- **SSE:** 128-bit registers, floating-point operations
- **SSE2:** Double-precision floating-point
- **SSE3:** Horizontal operations, complex arithmetic
- **AVX:** 256-bit registers
- **AVX-512:** 512-bit registers

### 6.3 Memory Alignment

**Alignment Requirements:** Data must be aligned to SIMD register size for optimal performance

**Performance Impact:** Misaligned accesses can cause significant slowdowns

### 6.4 SIMD Programming

**Intrinsics:** Compiler built-in functions that map directly to SIMD instructions

**Automatic Vectorization:** Compiler attempts to generate SIMD code automatically

**Examples:**

- Image processing operations
- Vector arithmetic
- Digital signal processing

### 6.5 SIMD Programming Examples

**Parallel Addition:**

```c
// 8 parallel byte additions
paddB mm1, mm0  // mm1[i] += mm0[i] for i=0..7
```

**Complex Number Multiplication:** SSE3 enables efficient complex arithmetic with specialized instructions

---

## Chapter 8: Multi-processor and Thread Level Parallelism

### 8.1 Multiprocessor Architectures

**Terminology:**

- **Processor:** Generic computational unit
- **Core:** Complete instruction pipeline with ALUs
- **CPU:** Single integrated circuit package

**Classification by Core Architecture:**

- **Homogeneous:** All cores identical (same ISA, same capabilities)
- **Heterogeneous:** Different core types (e.g., Apple M1)

**Memory Models:**

- **UMA (Uniform Memory Access):** Equal access time to all memory
- **NUMA (Non-Uniform Memory Access):** Variable access times
- **COMA (Cache-Only Memory Architecture):** No central memory

### 8.2 Interconnects for Multiprocessors

**Hierarchical Organization:**

- **IC Level:** Up to hundreds of cores per chip
- **System Level:** Multiple chips on motherboard
- **Data Center Level:** Multiple systems connected

### 8.3 Cache Coherency

**Problem:** Multiple caches may contain different values for same memory location

**Solutions:**

- **Snooping Protocols:** Broadcast-based coherency
- **Directory Protocols:** Centralized coherency tracking

### 8.4 Processes and Threads

**Process:** Independent execution context with separate address space

**Thread:** Smallest unit of execution within a process

- Shares process memory space
- Has own stack and register state
- Managed by OS scheduler

### 8.5 Simultaneous Multi-Threading (SMT)

**Concept:** Single physical core executes multiple threads simultaneously

**Benefits:**

- Better resource utilization
- Overlap memory latency with computation
- Higher throughput per core

**Implementation:** Logical cores within physical core, expanded register files

---

## Chapter 9: Practical Processor Architecture

### 9.1 Microprocessors & CMOS

**Moore's Law:** Transistor density doubles every 18 months

**CMOS Scaling Benefits:**

- Capacitance reduced by 30%
- Voltage reduced by 30%
- Frequency increased by 40%
- Power per transistor reduced by 50%

**Dennard Scaling:** Area and power scale by same factor, but cooling improvements lag behind

**Current Challenges:**

- Wire delays increasingly dominant
- Power density limitations
- Economic constraints on advanced nodes

### 9.2 Intel Architecture

**Intel Core Components:**

1. **Cores:** Out-of-order execution, multiple ALUs
2. **Cache Hierarchy:** L1 (private), L2/L3 (shared)
3. **Ring Interconnect:** High-bandwidth core communication
4. **System Agent:** Memory controller, coherency
5. **Graphics Engine:** Integrated GPU with 96 execution units
6. **I/O Controllers:** DRAM, peripherals

**Power Management (ACPI):**

- **S-States:** Sleep levels (5 levels)
- **C-States:** Core idle states
- **P-States:** Performance states with DVFS
- **Turbo Boost:** Temporary frequency increases using thermal budget

### 9.3 ARM Architecture

**ARM Features:**

- **Advanced RISC Machines** architecture
- Highly configurable core designs
- Instruction compression (32-bit to 16-bit)
- SIMD extensions for multiple data sizes
- Vector Floating Point (VFP) processor
- **TrustZone:** Hardware security layer

**SoC Integration:**

- Heterogeneous processing (ASMP)
- Specialized processors: cellular, sensor, display, GPU
- Machine learning accelerators
- Power management integration

**TrustZone Security:**

- Hardware/software protection for valuable data
- Secure and non-secure execution domains
- Confidentiality, integrity, authenticity guarantees

### 9.4 Recent Many-Core Architecture

**General-Purpose Many-Core:**

- Up to 1024 simple RISC-V cores
- 12k gates per core
- Configurable cache hierarchy
- Low-latency interconnect (1-5 cycles)

**3D Implementation:**

- Constant power despite 20% frequency increase
- Performance improvement or power savings
- Advanced packaging technologies

---

## Key Performance Metrics Summary

**Pipeline Efficiency:**

- **IPC (Instructions Per Cycle):** Measure of pipeline utilization
- **CPI (Cycles Per Instruction):** Average cycles needed per instruction

**Memory Performance:**

- **Cache Hit Rate:** Percentage of memory accesses served by cache
- **Memory Wall:** Growing gap between processor and memory speed

**Parallel Performance:**

- **Speedup:** Performance improvement from parallelization
- **Scalability:** Performance retention as system size increases

**Power Efficiency:**

- **DVFS (Dynamic Voltage/Frequency Scaling):** Power management technique
- **TDP (Thermal Design Power):** Maximum sustainable power dissipation

---

## Important Formulas Reference

**CPU Performance:**

```
CPU Time = Instructions × CPI × Clock Period
Throughput = 1 / CPU Time
```

**Pipeline Acceleration:**

```
Speedup = (N × n) / (n + N - 1) ≈ n (for large N)
```

**Cache Performance:**

```
Average Access Time = Hit Time + Miss Rate × Miss Penalty
```

**Amdahl's Law:**

```
Speedup = 1 / (S + (1-S)/P)
```

Where S = sequential fraction, P = number of processors

---

_This overview covers the essential concepts from chapters 3, 4, 5, 6, 8, and 9 of the microprocessor architecture course. Each chapter builds upon previous concepts, from basic processor operation through advanced parallel architectures and practical implementations._