
# Summary: ELEC-H-473 Th06 - Data Parallelism: SIMD

This document summarizes the key concepts related to data parallelism using SIMD (Single Instruction, Multiple Data) as presented in the lecture slides.

## 1. Data Parallelism: Motivation (Slides 4-9)

* **Problem:** Modern CPUs often have wide ALUs (e.g., 64-bit) designed for the largest common data types. When performing operations on smaller data types (e.g., 8-bit `char`), most of the ALU's width goes unused.
    * *Example (Slide 6):* Adding two 8-bit numbers (`0xB5 + 0x11`) on a 32-bit ALU leaves 24 bits unused. On a 64-bit ALU, 56 bits are unused (Slide 7). This is inefficient in terms of performance and power.
* **Solution: Data Parallelism / Sub-word Parallelism:** Pack multiple smaller data items into the wide ALU operand width and perform the *same* operation on all of them simultaneously within a single instruction cycle.
    * *Example (Slide 8):* Instead of adding one pair of 8-bit `char`s per instruction, pack four pairs into 32 bits (or eight pairs into 64 bits).
        ```
        // Original loop (inefficient ALU usage)
        for (i = 0; i < N; i++) { C[i] = A[i] + B[i]; }

        // Concept: Pack 4 bytes into a 32-bit register
        // Perform ONE 32-bit operation that acts like FOUR 8-bit additions
        // (Requires modified ALU that prevents carry between bytes)
        Bits:    | 31-24 | 23-16 | 15-08 | 07-00 |
        Op1:     | A[i+3]| A[i+2]| A[i+1]| A[i]  |
        Op2:     | B[i+3]| B[i+2]| B[i+1]| B[i]  |
        Result:  | C[i+3]| C[i+2]| C[i+1]| C[i]  |
        ```
* **Vector Processing:** This technique treats the packed data as a "vector".
* **Benefits (Slide 9):**
    * **Improved Compute Performance:** Perform multiple operations per instruction cycle.
    * **Improved Memory Access:** Transfer larger chunks (vectors) of data between memory, cache, and registers, improving bandwidth utilization.

## 2. Parallel Processing Classification (Flynn's Taxonomy) (Slides 10-16)

A way to classify computer architectures based on instruction and data streams (Slide 13-14):

1.  **SISD (Single Instruction, Single Data):**
    * Traditional sequential processing. One instruction operates on one piece of data (or a pair) at a time.
    * *Example:* A basic CPU executing standard instructions one after another.
2.  **SIMD (Single Instruction, Multiple Data):**
    * One instruction operates on multiple pieces of data (a vector) simultaneously.
    * *Example:* Vector processors, GPUs, CPU extensions like MMX, SSE, AVX. This is the focus of this lecture.
3.  **MISD (Multiple Instruction, Single Data):**
    * Multiple instructions operate on the same data stream concurrently.
    * Rare. *Example:* Fault-tolerant systems (like Space Shuttle computers running the same calculation on multiple processors), potentially some systolic arrays.
4.  **MIMD (Multiple Instruction, Multiple Data):**
    * Multiple instructions operate on multiple data streams concurrently.
    * *Example:* Multi-core processors, clusters, supercomputers, distributed systems. Each core runs its own instruction stream (often SISD or SIMD) on its own data.

* **SIMD History (Slide 16):** Used in early supercomputers (Cray, Connection Machines). Became mainstream in general-purpose CPUs to accelerate multimedia tasks.
* **Why SIMD in Modern CPUs? (Slide 17):** Driven by multimedia (audio, video, graphics) and scientific computing needs, which often involve applying the same operation to large arrays of small data types (pixels, audio samples, etc.).

## 3. SIMD Extensions in Intel CPUs (Slides 21-29)

Intel integrated SIMD capabilities through evolving instruction set extensions:

* **MMX (MultiMedia eXtension) (1997) (Slide 23, 25):**
    * Reused the 80-bit Floating-Point Unit (FPU) registers.
    * Operated on 64-bit packed integers (2x32b, 4x16b, 8x8b).
    * Could not mix MMX and FPU instructions easily.
    * *Example (Slide 25):* `paddb mm1, mm0` adds 8 packed bytes in `mm0` to 8 packed bytes in `mm1`. `paddw` adds 4 packed words (16-bit).
* **SSE (Streaming SIMD Extensions) (1999 onwards) (Slide 26, 27):**
    * Introduced dedicated **128-bit** registers (`xmm0`-`xmm7`, later more). Separate from FPU.
    * Added instructions for packed **single-precision (32-bit) floating-point** numbers.
    * Included instructions for data movement (aligned/unaligned), arithmetic, comparison, shuffling, logic.
    * **SSE2:** Added double-precision (64-bit) float support and integer operations on `xmm` registers.
    * **SSE3/SSE4:** Added more specialized instructions (DSP, graphics, string processing).
* **AVX (Advanced Vector Extensions) (2011) (Slide 28):**
    * Extended registers to **256 bits** (`ymm0`-`ymm15`).
    * Introduced **3-operand instructions** (e.g., `result = op1 + op2`) avoiding the need to overwrite one source operand, reducing extra copy instructions. Required OS support.
* **AVX2 (2013) / AVX-512 (2015 onwards) (Slide 29):**
    * Extended registers to **512 bits** (`zmm0`-`zmm31`). `xmm` and `ymm` registers are the lower parts of `zmm` registers.
    * Added many more instructions, often specific to CPU families (more fragmentation).
    * Supports 4-operand instructions (using masking).

## 4. Identifying the CPU (Slides 30-33)

* **Problem:** How to write code that uses the best SIMD features available on a specific CPU without crashing on older CPUs?
* **Solution:** Use the `cpuid` assembly instruction.
    * This instruction returns information about the processor's manufacturer, model, family, and supported features (including SIMD extensions like SSE, AVX, AVX2, AVX-512) in registers like EAX, EBX, ECX, EDX. (Slide 31, 32).
* **Manual Dispatch (Slide 33):** A programming technique (often using compiler-specific directives like Intel's `_declspec(cpu_dispatch)` and `_declspec(cpu_specific)`) to:
    1.  Write multiple versions of a function, each optimized for a different SIMD level (e.g., one generic, one SSE4, one AVX2).
    2.  At runtime, check the CPU features using `cpuid`.
    3.  Call the most optimized function version supported by the current CPU.

## 5. Memory Alignment (Slides 34-41)

* **Problem:** Memory is accessed in chunks (e.g., cache lines are 64 bytes). Accessing data that crosses the boundary of these natural hardware chunks is inefficient. SIMD instructions often require loading large vectors (16, 32, 64 bytes).
* **Alignment:** An address `A` is `n`-byte aligned if `A` is perfectly divisible by `n` (i.e., `A mod n == 0`). Accessing `n` bytes starting at an `n`-byte aligned address is most efficient.
    * *Example (Slide 36):* For 8-byte alignment, addresses 0x0, 0x8, 0x10 are aligned. 0x3 is not.
    * Intel AVX-512 prefers 64-byte alignment for optimal performance.
* **Misaligned Access Penalty:** Accessing data that is not aligned to the required boundary (e.g., loading a 16-byte vector starting at address 0x3) forces the hardware to perform extra work: potentially multiple memory accesses, shifting, and merging data. This significantly slows down execution. (Slide 40 shows speedups of 1.1x to 5.5x for aligned vs. misaligned access).
* **Cache Line Splits (Slide 41):** A misaligned access that crosses a cache line boundary requires loading *two* cache lines instead of one, doubling the cache/memory traffic for that access.
* **How to Align Data (Slide 37, 38):**
    * **Static Data:** Use compiler directives (e.g., `_declspec(align(64)) float A[1000];`).
    * **Dynamic Data:** Use special allocation functions (e.g., `_mm_malloc(size, 64);` instead of `malloc`). Remember to use the corresponding free function (`_mm_free`).
    * **Compiler Hint:** Tell the compiler that data *is* aligned (e.g., `_assume_aligned(ptr, 64);`) so it can generate faster, aligned move instructions (`movaps`) instead of slower, unaligned ones (`movups`). (Slide 39).
    * **Struct Padding (Slide 38):** Compilers often automatically insert unused bytes (padding) within structures to ensure that members are aligned according to their size. This wastes some space but improves access speed.

## 6. SIMD Programming for Intel CPUs (Slides 42-50)

Methods to write SIMD code:

1.  **a) Assembly (Slide 45):**
    * Directly write SIMD assembly instructions (e.g., `addps`, `movaps`).
    * Offers maximum control and potentially highest performance.
    * Difficult, time-consuming, error-prone, not portable.
    * Can use **inline assembly** within C/C++ code (`asm { ... }`).
2.  **b) Intrinsics (Slide 46):**
    * C/C++ functions provided by the compiler (e.g., via `<xmmintrin.h>`) that map directly to specific SIMD assembly instructions (e.g., `_mm_load_ps`, `_mm_add_ps`, `_mm_store_ps`).
    * Looks like function calls but compiles down to single instructions.
    * Almost as performant as assembly but much easier to write and read. More portable across compilers that support the same intrinsics.
3.  **c) Performance Libraries (Slide 48, 49):**
    * Use pre-optimized libraries provided by vendors (e.g., Intel IPP - Integrated Performance Primitives).
    * Contain highly tuned functions for common tasks (signal processing, image processing, math, crypto).
    * Easiest way to get high performance for supported domains. Just link the library and call the functions. Performance might be slightly less than hand-tuned assembly/intrinsics for very specific cases.
    * *Example (Slide 49):* Calling `ippsTriangle_16s` to generate a signal using optimized SIMD code internally.
4.  **d) Automatic Vectorization (Slide 50, also Section 7):**
    * Rely on the compiler to automatically detect loops in standard C/C++ code and convert them into SIMD instructions.
    * Easiest approach - just use compiler optimization flags (like `-O2`, `-O3`).
    * Effectiveness varies greatly. Compilers have strict requirements for loops they can vectorize. Performance may not be optimal. Often needs programmer hints (`pragmas`).

* **Trade-off (Slide 44):** Performance vs. Ease of Programming/Portability. Assembly is hardest but potentially fastest. Auto-vectorization is easiest but potentially slowest/least reliable. Libraries and Intrinsics offer good compromises.

## 7. Automated Compiler Vectorization (Slides 51-64)

Conditions and obstacles for compilers to automatically generate SIMD code from standard loops.

* **Compiler Flags (Slide 52):** Optimization flags like `-O2` or `-O3` usually enable auto-vectorization attempts. Flags like `-vec-report` show which loops were vectorized. `-no-vec` disables it.
* **Requirements for Vectorizable Loops:**
    1.  **Countable (Slide 53):** Loop trip count must be known at loop entry (can be variable, but fixed during loop execution). No data-dependent exits within the loop body.
    2.  **Single Entry/Exit (Slide 54):** No `goto` in/out, no multiple `break` conditions based on data computed within the loop.
    3.  **Straight-Line Code (Slide 55):** No complex branching inside the loop. Simple `if` statements *might* be vectorized using *masked assignments* (compute all paths, only store results where the condition was true).
    4.  **Innermost Loops of Nested Loops:** Often, only the innermost loop is a candidate.
    5.  **No Loop-Carried Dependencies (Slide 56, 59-61):** An iteration cannot depend on the result computed in a *previous* iteration.
        * *Example (RAW - Read-After-Write, Slide 59):* `A[j] = A[j-1] + 1;` - Cannot vectorize directly as `A[j]` needs the value computed just before.
        * *Example (WAR - Write-After-Read, Slide 60):* `A[j-1] = A[j] + 1;` - Sometimes vectorizable, but tricky if other operations use `A[j]` before it's potentially overwritten early by the vectorized `A[j-1]=...`.
        * *Example (WAW - Write-After-Write, Slide 61):* `sum = sum + ...;` (Reduction) - Can often be vectorized with special handling. `c[i] = a[i] * b[i];` - Vectorizable only if `a`, `b`, `c` don't overlap in memory (aliasing).
    6.  **No Function Calls (Slide 57):** Cannot call regular functions (even `printf`). Exceptions: Intrinsics or `inline` functions.
* **Obstacles (Things that Prevent/Hinder Vectorization):**
    * **Non-Contiguous Memory Access (Slide 58):** Accessing array elements with strides (e.g., `A[i*2]`) or indirectly (`A[index[i]]`) requires complex "gather/scatter" operations, often preventing vectorization or making it slow.
    * **Data Dependencies (as above).**
    * **Pointer Aliasing (Slide 61):** If the compiler cannot be sure that pointers (`a`, `b`, `c` in `c[i]=a[i]*b[i]`) do not point to overlapping memory regions, it usually won't vectorize for safety.
* **Helping the Compiler (`pragmas`) (Slide 62, 63):**
    * Special comments giving hints to the compiler.
    * `#pragma ivdep`: Ignore potential vector dependencies (use if *you* know they don't really exist or don't matter).
    * `#pragma loop count(n)`: Hint about typical loop iteration count.
    * `#pragma vector align`: Assert data is aligned.
    * `#pragma vector`: Try harder to vectorize.
    * `#pragma novector`: Don't vectorize this loop.
    * `#pragma vector nontemporal`: Hint to use streaming stores (bypass cache).
* **Streaming Stores (Slide 64):** Special instructions (`MOVNT...`) that write data directly to memory, bypassing the cache hierarchy. Useful for data that won't be immediately reused (non-temporal), avoiding cache pollution. Often used with `#pragma vector nontemporal`.
