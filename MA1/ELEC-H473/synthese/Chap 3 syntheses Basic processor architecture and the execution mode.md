# Summary: ELEC-H-473 Th03 - Basic Processor Architecture and Execution Model

This document summarizes the key concepts presented in the ELEC-H-473 Th03 lecture slides by Dragomir Milojevic (Université libre de Bruxelles, 2023).

## 1. Basic Computer Architecture Concepts

* **Definition of a Computer:** Distinguished from calculators by electronically storing the program (instructions) that controls computation (Von Neumann).
* **Von Neumann Architecture:**
    * Model with a Central Processing Unit (CPU), Memory, and Input/Output (IO).
    * CPU contains Arithmetic-Logic Unit (ALU), Control Unit (CTRL), and Register File (RF).
    * Key concept: Unified memory stores both instructions and data. Allows programs to be treated as data (e.g., for compilation, self-modifying code).
* **Harvard Architecture:**
    * Variant with separate functional blocks for Data Memory and Instruction Memory.
    * Allows concurrent access to instructions and data, potentially improving throughput.
    * Pure Harvard is rare, used in some DSPs/microcontrollers.
* **Modified Harvard Architecture:**
    * Common in modern systems, uses separate L1 caches for instructions and data, but unified L2/L3 caches and main memory.
    * Combines benefits: unified memory model (Von Neumann) with concurrent access (Harvard).
* **CPU Components:**
    * **ALU:** Executes a fixed set of arithmetic/logic operations. May have specialized units (integer, floating-point).
    * **RF:** Fast storage for operands/results. Often multi-ported for parallel access (multiple reads, typically one write).
* **Memory:**
    * Stores instructions and data (binary). Instructions often in protected regions.
    * Accessed via ports: Address (location), Data (value, often bidirectional), Control (operation type, e.g., R/W, enable).
    * Performance: Capacity vs. Access Time trade-off. Bandwidth ($BW = F_{RW} \times m$) depends on frequency and data width.
* **Masters and Slaves:** CPU (master) initiates transfers with Memory (slave). DMA controllers can offload data transfers from the CPU.
* **Performance Considerations:**
    * **Hardware:** CPU speed vs. Memory speed vs. Interconnect speed. Bottlenecks arise if speeds don't match (CPU stalls). Wire RLC limits frequency.
    * **Software:** Execution time can be Computation-bound or Memory-bound. Goal is to balance computation, memory access, and communication. "Memory Wall" is a major challenge.

## 2. Minimum System Architecture

* Adds key components to the basic model:
    * **Program Counter (PC):** Holds the address of the *next* instruction to be fetched. Incremented sequentially or updated by jumps/branches.
    * **Instruction Register (IR):** Holds the *current* instruction's opcode after fetching, ready for decoding.
    * **Accumulator (ACC):** Optional register for intermediate ALU results.
* Illustrates data paths: Memory to RF, Memory to IR, RF to ALU, ACC to ALU, ALU to PC.

## 3. Instruction Execution Cycle (Von Neumann Model)

A sequential process for executing each instruction:

1.  **Fetch:** Get instruction from Memory (address specified by PC) into the IR.
2.  **Decode:** Interpret the opcode in the IR to generate control signals for other CPU components.
3.  **Execute:** Perform the operation (using ALU) on operands (fetched from RF or Memory). PC is updated.
4.  **Write:** Store the result back into the destination (RF or Memory).

* **Memory Access Architectures:**
    * **Load/Store:** Operations only on RF data; explicit memory loads/stores needed.
    * **Register/Memory:** Operands can be in RF *or* Memory (but not mixed).
    * **Register + Memory:** Operands can be mixed between RF and Memory.

## 4. Different ISAs: RISC vs. CISC

* **Instruction Set Architecture (ISA):** Defines *what* a processor can do (instructions, data types, registers, memory model, addressing modes, I/O). Abstract interface between hardware and software.
* **Common Instruction Classes:** Data movement, Arithmetic, Shifts, Logical, Control (jumps/branches), Subroutine calls, Interrupt handling.
* **Addressing Modes:** Various ways to specify operand locations (Register, Immediate, Indirect, Displacement, Indexed, Direct, etc.).
* **Instruction Frequency:** Most programs spend most time executing a small subset of instructions (load, branch, compare, store, add are dominant).
* **RISC (Reduced Instruction Set Computer):**
    * **Philosophy:** Simple, fixed-length instructions, optimized for speed (often 1 clock cycle). Complex operations built from simple ones in software. Typically Load/Store architecture.
    * **Goal:** Reduce Cycles Per Instruction (CPI), potentially increase $F_{Clk}$. May increase Instruction Count ($N_{Inst}$).
    * **Examples:** SPARC, MIPS, RISC-V, ARM (historically).
* **CISC (Complex Instruction Set Computer):**
    * **Philosophy:** Rich set of instructions, including complex, multi-cycle ones. Can operate directly on memory.
    * **Goal:** Reduce Instruction Count ($N_{Inst}$). May increase CPI (variable) and $F_{Clk}$.
    * **Examples:** Intel x86, AMD x86.
* **Performance Trade-off:** $t_{CPU} = N_{Inst} \times CPI \times \frac{1}{F_{Clk}}$. Neither RISC nor CISC is universally superior; depends on algorithm, compiler, architecture.
* **Modern Trend:** Blurring lines, heterogeneous architectures (e.g., Apple M1 with performance/efficiency cores, GPUs, neural engines).

## 5. Instruction Execution Cycle & Circuit

* **Single Issue Base Machine (SIBM):** Simplest model. Executes one instruction completely (Fetch, Decode, Execute, Write) before starting the next. Slow ($T = N \times 4 \times t$). Low $F_{Clk}$ due to large critical path.
* **Pipelining Motivation:**
    * CPU performance is limited by the critical path delay.
    * Larger circuits generally have longer critical paths and lower $F_{Clk}$.
    * Idea: Split the large CPU circuit into smaller stages (e.g., F, D, Ex, W).
* **Pipelining Implementation:**
    * Insert registers (Flip-Flops) between stages.
    * Registers store intermediate results and synchronize stages to the clock.
    * Breaks the critical path, allowing each stage to be smaller and faster $\rightarrow$ higher $F_{Clk}$.
    * Introduces **latency**: the result of one instruction takes multiple cycles (equal to the number of stages) to complete.

## 6. Pipeline Execution

* **Operation:** Instructions move through stages (F, D, Ex, W) sequentially. Multiple instructions are processed concurrently in different stages.
    * e.g., At time $t_3$: Fetch $i_3$, Decode $i_2$, Execute $i_1$, Write $i_0$.
* **Throughput:** Once the pipeline is full, it ideally completes one instruction per clock cycle.
* **Latency:** Remains the number of stages (e.g., 4 cycles for F, D, Ex, W).
* **Acceleration:** Compared to SIBM, speedup approaches $n$ (number of stages) for large $N$ instructions: $Acc \approx n$.
* **Instruction Level Parallelism (ILP):** Pipelining achieves ILP by overlapping the execution of different instructions.
* **IPC (Instructions Per Cycle):** Measures pipeline efficiency. Ideal IPC = 1 for a single-issue pipeline. In practice, IPC < 1 due to hazards (discussed later).
* **Pipeline Depth:** Trade-off between higher $F_{Clk}$ (more stages) and increased latency (more stages). Modern CPUs typically have 10-20 stages, down from >30 in some older designs.
* **Hardware/Software Interaction:** Pipeline execution is handled by HW, but SW choices can significantly impact pipeline efficiency (e.g., code structure affecting branches).

## Key Takeaways

* Modern CPUs use Modified Harvard architectures, blending RISC/CISC ideas.
* Pipelining is a fundamental technique to increase instruction throughput by exploiting ILP, allowing higher clock frequencies at the cost of increased latency per instruction.
* Performance depends on balancing $N_{Inst}$, CPI, and $F_{Clk}$, which are influenced by ISA, microarchitecture (pipelining), and compiler optimizations.
