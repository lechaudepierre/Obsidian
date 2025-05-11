
# Summary: ELEC-H-473 Th05 - Memory Subsystem

This document summarizes the key concepts related to memory technologies, organization, caching, and management as presented in the lecture slides.

## 1. Memory Technologies (Slides 4-10)

Memory is crucial for system performance. Technologies are broadly classified based on volatility (data retention without power).

* **Volatile Memory:** Loses data when power is off. Key types:
    * **a) Flip-Flops (FFs):**
        * Basic digital storage element, bistable (two stable states: 0 or 1).
        * Edge-triggered (sensitive to clock signal transitions).
        * Fastest but largest/most expensive per bit.
        * **Use:** CPU Registers (like PC, IR, RF), pipeline stage registers, FSMs.
    * **b) SRAM (Static RAM):**
        * Stores bit using cross-coupled inverters (typically 6 transistors - 6T cell).
        * Holds data as long as power is supplied (static), no refresh needed.
        * Faster than DRAM, slower than FFs. Medium cost/density.
        * **Use:** Cache memory (L1, L2, L3).
        * *Cell Structure (Slide 7):* Two inverters feedback into each other to hold state. Two access transistors controlled by the Word Line (WL) connect the inverters to the Bit Lines (BL, BL').
    * **c) DRAM (Dynamic RAM):**
        * Stores bit as electrical charge on a tiny capacitor (typically 1 transistor + 1 capacitor - 1T1C cell).
        * Capacitor leaks charge, so data fades. Requires periodic **refresh** operations to maintain data, even when not actively accessed. Reading is also destructive.
        * Slower than SRAM due to access method and refresh cycles.
        * Highest density and lowest cost per bit.
        * **Use:** Main system memory.
        * *Cell Structure (Slide 8):* A single transistor acts as a switch controlled by the Word Line (WL). When ON, it connects the capacitor to the Bit Line (BL) for reading or writing charge.
* **Non-Volatile Memory:** Retains data without power (e.g., Flash SSDs, HDD, ROM - not the focus here).
* **Magnetic Hard Drive Disks (HDDs) (Slide 9):** Mechanical storage using spinning magnetic platters, tracks, and sectors. Much slower than semiconductor memory.
* **Comparison (Slide 10):**
    | Technology      | Transistors/bit | Access Time | Relative Density | Relative Cost/Bit |
    | :-------------- | :-------------: | :---------- | :--------------- | :---------------- |
    | Flip-Flop (FF)  |      ~20        | < 0.5 ns    | Lowest           | Highest           |
    | SRAM            |        6        | 1-10 ns     | Medium           | Medium            |
    | DRAM            |        1        | ~100 ns     | Highest          | Lowest            |

## 2. Memory Organization (Slides 11-19)

Individual bit cells are organized into larger structures.

* **2D Arrays (Slide 12):**
    * Memory cells arranged in a grid (rows x columns).
    * **Word Line (WL):** Selects an entire row. Driven by a **Row Decoder**.
    * **Bit Line (BL):** Selects/accesses columns within the selected row. Connected to **Column Decoder/Access** logic.
* **SRAM Array Details (Slide 13, 14):**
    * Uses the 6T SRAM cell grid.
    * **Row/Column Decoders:** Select the specific bit(s) based on the address.
    * **Peripheral Circuits for Speed:**
        * **Precharge Circuit:** Sets Bit Lines to a known voltage before access, speeding up detection.
        * **Sense Amplifier:** Detects the small voltage difference on Bit Lines during a read and quickly amplifies it to a full logic level.
    * Multiple arrays are often used in parallel to read/write a full data word (e.g., 8 arrays for an 8-bit word).
* **DRAM Array Details (Slide 15):**
    * Uses the 1T1C DRAM cell grid.
    * **Memory Controller:** External logic managing DRAM access.
    * **Two-Step Access (RAS/CAS):**
        1.  Controller sends **Row Address** (RAS). Entire row is copied to an **Internal Row Buffer** (like a small SRAM).
        2.  Controller sends **Column Address** (CAS). Data is accessed (read/write) from/to the **Row Buffer**.
    * This buffer helps speed up access to consecutive data within the same row (only CAS needed).
* **Memory Access Size (Word Parallelism) (Slide 16):** Accessing single bytes is inefficient. Modern systems access wider words (e.g., 64 bits, 512 bits) in parallel from multiple DRAM chips/arrays to increase bandwidth.
* **Memory Modules (Slide 17):** DRAM chips are packaged onto modules (e.g., DIMMs) that plug into the motherboard. Multiple chips work in parallel.
* **DDR SDRAM (Slide 18):** Double Data Rate Synchronous DRAM. Transfers data on both rising and falling clock edges, doubling the effective transfer rate. Standards evolved (DDR, DDR2, DDR3, DDR4, DDR5) increasing frequency and bandwidth. Still often a bottleneck ("Memory Wall").
* **High Bandwidth Memory (HBM) (Slide 19):**
    * Addresses the memory wall by 3D stacking of DRAM dies directly on top of or beside the CPU/GPU using a silicon **interposer**.
    * Uses thousands of short, on-package connections (**TSVs** - Through-Silicon Vias).
    * Achieves much higher bandwidth (e.g., >2 TB/s) and lower power compared to off-package DDR memory.

## 3. Cache Memory & Hierarchy (Slides 20-31)

Caches bridge the speed gap between fast CPUs and slow DRAM.

* **The CPU-Memory Gap (Slide 21, 22):** CPUs have become much faster than DRAM (100-1000x difference), leading to the "Memory Wall" where CPU waits for data.
* **Cache Concept (Slide 23):** A small, fast memory (typically SRAM) placed between the CPU and main memory (DRAM). It holds copies of recently used data/instructions.
    * **Cache Hit:** CPU requests data, finds it in the cache. Fast access.
    * **Cache Miss:** CPU requests data, not in cache. Must fetch from slower main memory. Slow access (CPU stalls).
* **Principle of Locality (Why Caches Work) (Slide 24, 25):** Programs tend to reuse data and instructions they have used recently.
    * **Temporal Locality:** If an item is accessed, it's likely to be accessed again soon (e.g., loop counter variable `j`, instructions inside a loop).
    * **Spatial Locality:** If an item is accessed, items with nearby addresses are likely to be accessed soon (e.g., accessing array elements `C[j]` then `C[j+1]`, fetching sequential instructions).
    * *Example (Slide 25):*
        ```c
        for (j=0; j<1000000; j++) {
          C[j] = A[j] * B[j]; // Good spatial locality for A, B, C
                             // Good temporal locality for j and loop code
        }
        for (j=0; j<1000000; j++) {
          C[j] = A[j] * B[j*100]; // Poor spatial locality for B
        }
        ```
* **Cache Design Trade-offs (Slide 26):** Larger caches increase hit rate but are more expensive (area, power) and potentially slower. Memory can be 30-60% (or more) of CPU die area.
* **Cache Performance Metrics (Slide 27):** `Memory Stall Cycles = Instructions * Misses_per_Instruction * Miss_Penalty`. Need to minimize misses and the cost (time) of a miss.
* **Memory Hierarchy (Slide 29, 30):** Multiple levels of caches are used, forming a hierarchy.
    * **Levels:** Registers -> L1 Cache -> L2 Cache -> L3 Cache -> Main Memory (DRAM) -> Secondary Storage (Disk/SSD).
    * **Properties:** As you go down the hierarchy: Size increases, Speed decreases, Cost per bit decreases.
    * **L1 Cache:** Smallest, fastest. Often split into L1d (Data) and L1i (Instruction) - a Modified Harvard approach. Closest to CPU cores.
    * **L2/L3 Cache:** Larger, slower than L1. Usually unified (hold both data and instructions). Often shared between multiple cores. LLC = Last Level Cache (L3 or L4).
    * *Example (Slide 31):* AMD 3D V-Cache stacks L3 cache vertically on top of CPU cores for massive LLC size and high bandwidth.

## 4. Implementing Caches (Slides 32-50)

How caches are organized and managed internally.

* **Cache Structure (Slide 33):**
    * Memory is transferred between main memory and cache in blocks called **Cache Lines** (e.g., 64 bytes).
    * Cache stores data in **Cache Entries**. Each entry contains:
        * **Data Block:** The actual data copied from main memory (size = cache line size).
        * **Tag:** Stores the high-order bits of the main memory address to identify which block this is.
        * **Flags:** Status bits, commonly:
            * **Valid Bit:** Is the data in this entry meaningful/loaded?
            * **Dirty Bit:** Has the data in this cache entry been modified by the CPU (relevant for write policies)?
* **Key Design Questions (Slide 34):**
    1.  **Block Placement:** Where can a block from main memory be placed in the cache?
    2.  **Block Identification:** How is a block found in the cache?
    3.  **Block Replacement:** If the cache is full, which block is evicted to make space for a new one?
    4.  **Write Strategy:** How are writes handled?
* **Cache Mapping Strategies:**
    * **1. Direct Mapped Cache (Slides 35-37):**
        * *Placement:* Each main memory block can only go into *one specific* cache line (set). Determined by index bits in the address.
        * *Address Split:* `[ Tag | Set Index | Byte Offset ]`
        * *Identification (Slide 36):* Use `Set Index` bits to go directly to the single possible cache entry. Compare the `Tag` bits from the address with the tag stored in that cache entry. Check the `Valid` bit. If tags match and valid=1, it's a **Hit**.
        * *Replacement:* Simple - the new block just overwrites the existing block in that specific line.
        * *Problem (Slide 37 - Conflict Misses):* If a program frequently accesses two different memory blocks that map to the *same* cache line index, they will constantly evict each other, causing many misses even if the cache isn't full.
            *Example:* Accessing addresses `0x0004` and `0x0024` repeatedly in the slide's example cache would cause conflicts if they map to the same set index.*
    * **2. Set-Associative Cache (Slides 38-40):**
        * *Placement:* A main memory block can be placed in any one of a *small set* of cache lines (`m` lines, where `m` is the associativity or "ways"). Cache is divided into Sets, each Set contains `m` ways (lines). Determined by index bits.
        * *Address Split:* `[ Tag | Set Index | Byte Offset ]` (Tag is larger, Index is smaller than direct mapped for same cache size).
        * *Identification (Slide 39):* Use `Set Index` bits to select the Set. **Compare the address Tag in parallel** with the tags stored in *all `m` ways* within that set. Check valid bits. If any tag matches and is valid, it's a **Hit**. A MUX selects data from the correct way.
        * *Replacement:* Needed when a new block comes into a set where all `m` ways are full. A **Replacement Policy** decides which existing block to evict.
        * *Benefit (Slide 40):* Reduces conflict misses compared to direct mapped. Higher associativity generally improves hit rate (like doubling cache size initially).
    * **3. Fully Associative Cache:**
        * *Placement:* A main memory block can be placed in *any* line in the entire cache. (Like 1 big Set).
        * *Address Split:* `[ Tag | Byte Offset ]`
        * *Identification:* Compare the address Tag with the tags in *every single line* of the cache in parallel. Most complex hardware.
        * *Replacement:* Policy needed to choose which block to evict from the whole cache.
        * *Use:* Practical only for very small caches (like TLBs) due to hardware cost of parallel comparators.
* **Replacement Policies (for Set/Fully Associative) (Slide 41, 42):**
    * **Random:** Pick a line within the set to evict randomly. Simple hardware.
    * **LRU (Least Recently Used):** Evict the block in the set that hasn't been accessed for the longest time. Good performance but complex hardware to track usage.
    * **FIFO (First-In, First-Out):** Evict the block that has been in the set the longest. Simpler than LRU, approximates its behavior.
* **Write Policies (Handling Cache Writes) (Slide 43, 44):**
    * **Write Hit:** Address to write is already in the cache.
        * **Write-Through:** Write data to *both* the cache line *and* main memory immediately. Simple, ensures consistency, but can be slow due to main memory write latency.
        * **Write-Back:** Write data *only* to the cache line and set its **Dirty Bit** to 1. Main memory is *not* updated immediately. The modified block is only written back to main memory later when it's evicted from the cache (if the dirty bit is 1). More complex, but much faster for frequent writes to the same block.
    * **Write Miss:** Address to write is not in the cache.
        * **Write-Allocate:** Fetch the block from main memory into the cache first, then perform the write (usually used with Write-Back).
        * **No-Write-Allocate (Write-Around):** Write directly to main memory, bypassing the cache (usually used with Write-Through).
    * **Write Buffer (Slide 45):** Optimization for Write-Through. CPU writes to a small buffer; the buffer handles writing to main memory in the background, allowing the CPU to continue sooner. Can still stall if the buffer fills up faster than main memory can drain it. Motivates multi-level caches.
* **Instruction Cache (Slide 46):** Caches specifically for instructions. Generally simpler as they are read-only (no writes, no dirty bits needed). Separate L1i/L1d allows simultaneous instruction fetch and data access (Modified Harvard).
* **Cache Miss Types (Slide 47):**
    * **Compulsory (Cold):** First access to a block; it *must* be fetched. Unavoidable.
    * **Conflict:** Occurs in Direct Mapped or Set-Associative when too many active blocks map to the same set, causing useful blocks to be evicted. Reduced by higher associativity.
    * **Capacity:** Cache is too small to hold all the blocks needed by the program, causing blocks to be evicted and later re-fetched. Reduced by larger cache size.
* **Caches & Context Switches (Slide 48, 49):** When the OS switches tasks (processes/threads), the new task likely needs different data. This causes many cache misses as the new task's data replaces the old task's data in the cache (cache pollution). Can be a significant performance penalty.
* **Software Optimization: Cache Blocking (Tiling) (Slide 50):**
    * Technique to improve cache performance for algorithms working on large datasets (like matrix multiplication).
    * Instead of processing entire rows/columns (which might stride through memory and evict useful cache lines), process the data in smaller blocks (tiles) that fit comfortably within the cache.
    * *Example:* For `C = A * B`, compute the result block-by-block, maximizing reuse of data already loaded into the cache for each block computation.
        ```c
        // Original (potentially poor cache usage for large matrices)
        for (i=0; i<N; i++)
          for (j=0; j<N; j++)
            for (k=0; k<N; k++)
              C[i][j] += A[i][k] * B[k][j];

        // Blocked (improved cache locality)
        for (ii=0; ii<N; ii+=BLOCK_SIZE)
          for (jj=0; jj<N; jj+=BLOCK_SIZE)
            for (kk=0; kk<N; kk+=BLOCK_SIZE)
              // Mini-matrix multiplication on blocks that fit in cache
              for (i=ii; i<ii+BLOCK_SIZE; i++)
                for (j=jj; j<jj+BLOCK_SIZE; j++)
                  for (k=kk; k<kk+BLOCK_SIZE; k++)
                    C[i][j] += A[i][k] * B[k][j];
        ```

## 5. Memory Management (Slides 51-57)

How the OS and hardware manage the memory space available to programs.

* **Physical vs. Virtual Addressing (Slide 52, 53):**
    * **Physical Address (PA):** The actual hardware address corresponding to a location in DRAM.
    * **Virtual Address (VA):** The address generated by the CPU/program. Programs operate in a *Virtual Address Space*.
    * **Why Virtual?** Allows multiple programs to run concurrently without interfering with each other's memory, lets programs use more memory than physically available, simplifies memory allocation for the OS.
* **Address Translation (Slide 54):** The process of converting a VA to a PA.
    * Done by hardware called the **Memory Management Unit (MMU)**, usually on the CPU chip.
    * Uses **Page Tables** (data structures managed by the OS, stored in main memory) as a lookup table.
* **Paging:** Virtual and physical memory are divided into fixed-size blocks:
    * **Virtual Pages (VPs):** Blocks in the virtual address space.
    * **Physical Pages (PPs) / Page Frames:** Blocks in the physical memory (DRAM).
    * The Page Table maps VPs to PPs.
* **Translation Lookaside Buffer (TLB) (Slide 55):**
    * A small, fast cache (on the MMU) that stores *recent* VA-to-PA translations from the Page Table.
    * **TLB Hit:** The translation for the current VA is found in the TLB. MMU gets the PA quickly.
    * **TLB Miss:** Translation not in TLB. Hardware (or OS) must perform a "page table walk" (accessing the potentially multi-level Page Table in main memory) to find the translation. This is slow. The result is then stored in the TLB for future use.
* **Virtual Memory (Using Disk as Memory Extension) (Slide 56):**
    * Allows the system to use more memory than physically available DRAM by using disk (HDD/SSD) as backing store.
    * The full Virtual Address Space exists conceptually. Some VPs map to PPs in DRAM, while others reside only on disk.
    * **Page Fault:** CPU accesses a VA whose VP is currently *not* in DRAM (marked invalid in Page Table). This triggers an exception:
        1.  OS takes control.
        2.  OS finds a victim PP in DRAM (if necessary) and writes its contents back to disk (if modified).
        3.  OS loads the required VP from disk into the chosen PP.
        4.  OS updates the Page Table to map the VA to the newly loaded PP.
        5.  OS returns control to the program, which re-executes the faulting instruction (now successfully).
    * This makes the disk act like a very large, very slow extension of DRAM, cached by the actual DRAM.
* **Advantage Example (Slide 57):** A program can `malloc` memory vastly larger than physical RAM (e.g., 256GB on a 4GB machine). The OS allocates virtual address space. Physical pages are only allocated (and potentially loaded from/swapped to disk via page faults) as the program actually *accesses* different parts of that huge allocation.

