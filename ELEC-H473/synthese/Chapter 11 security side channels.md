## Resume: Session 11 - Vulnerabilities in Hardware: Side Channels

This session explores hardware vulnerabilities, focusing on side-channel attacks, their principles, prominent examples like interrupt latency attacks (Nemesis) and speculative execution attacks (Spectre), and potential mitigation strategies.

### 1. Introduction to Hardware Vulnerabilities & Side Channels

- **Motivation:** Even with software isolation mechanisms like enclaves, underlying hardware can introduce vulnerabilities. The operating system (OS) itself is often a source of bugs, and an untrusted OS can enable powerful side-channel attacks.
    
- **Side-Channel Attack Principle:** These attacks don't exploit flaws in the algorithm's design (like breaking a cipher mathematically) but rather exploit information leaked from the physical implementation of a system. The analogy used is like trying to open a vault: instead of breaking the door, one might observe how long it takes to turn the dials, or the sounds made, to infer the combination.
    

### 2. Understanding Side and Covert Channels

- **Definitions:**
    
    - **Covert Channel:** An _intentional_ communication path that was not designed to transfer information, often using unusual methods.
        
    - **Side Channel:** Similar to a covert channel, but the "sender" (victim program) does _not intend_ to communicate. Information leakage is an unintentional side effect of the hardware or software implementation. The "receiver" is the attacker.
        
- **Goals:**
    
    - Break logical protections and leak confidential/sensitive information (primary goal, attacking confidentiality).
        
    - Can also be used to attack integrity (e.g., fault attacks).
        
    - Leak control flow, execution patterns, memory access patterns, or hardware usage patterns.
        
- **Channel Direction:**
    
    - **Victim-to-Attacker (Typical):** Victim's operations unintentionally send information to the attacker.
        
    - **Attacker-to-Victim:** Attacker's behavior modulates processor state, affecting the victim's execution in a way that can be exploited.
        
- **Means of Transmission / Physical Emanations:**

    - Timing variations
        
    - Power consumption
        
    - Thermal emanations
        
    - Electromagnetic (EM) emanations
        
    - Acoustic emanations
        
- **Physical Proximity Requirements:**
    
    - **Timing Channels:** Can often be exploited remotely if the attacker can run code on the victim system or even just has network access (e.g., NetSpectre). No special equipment is usually needed.
        
    - **Power Channels:** Typically require physical connection to measure CPU power (unless on-chip sensors can be abused).
        
    - **Thermal, Acoustic, EM Channels:** Allow remote signal collection but depend on emanation strength and range.
        

### 3. Timing Side Channels

Timing channels exploit variations in the time it takes for operations to execute.

- **Six Key Sources of Timing Variations in Processors:**
    
    1. **Instruction with Different Execution Timing:** Different instructions inherently take different amounts of time (e.g., `ADD` vs. `FPMUL`).
        
    2. **Variable Instruction Timing:** The same instruction can take different amounts of time based on system state (e.g., AVX instructions with dynamic frequency scaling).
        
    3. **Functional Unit Contention:** Shared hardware units (ALUs, FPUs) lead to contention; delays can leak information about other programs' activity.
        
    4. **Stateful Functional Units:** A program can alter the state of a functional unit, and other programs can observe outputs or performance variations dependent on this state.
        
    5. **Prediction Units:** Branch predictors and other prediction units can be "trained" or have their state influenced, leading to observable timing differences.
        
    6. **Memory Hierarchy (Caches):** Accessing data from different cache levels (L1, L2, LLC) or main memory results in significant timing differences. This is a very common source for timing attacks (e.g., Flush+Reload, Prime+Probe).
        
    
    _(The slides depict these sources as "hot spots" on a generic processor pipeline diagram, including branch prediction, caches, functional units, and memory controller.)_
    

### 4. Case Study: Interrupt Latency as a Side Channel (e.g., Nemesis Attack)

This attack exploits the time it takes for the system to service an interrupt (Interrupt Request, IRQ).

- **Core Principle:**
    
    - Most CPUs complete the currently executing instruction before handling an IRQ.
        
    - Since different instructions (or the same instruction on different data paths) have variable execution times (latencies), the time until the IRQ is serviced (interrupt latency) leaks information about the interrupted instruction.
        
- **Mechanism:**
    
    - An attacker precisely schedules an interrupt (e.g., using a hardware timer).
        
    - They measure the time elapsed until their interrupt service routine (ISR) begins execution (e.g., using a Time Stamp Counter, `TSC`). This difference (ΔTSC) is the interrupt latency.
        
- **Secure Keypad Scenario (MSP430 with Sancus TEE):**
    
    - **Setup:** A secure module (`SM_secure`) on an MSP430 (enhanced with Sancus) processes keypad input. A loop checks each key: `if key_state & (0x1 << i) then secret_pin.add(keymap[i])`.
        
    - **Vulnerability:** The `if` branch (key pressed, PIN updated) takes a different number of CPU cycles than the `else` branch (key not pressed).
        
    - **Attack:**
        
        1. Attacker configures a timer to trigger an IRQ.
            
        2. Victim code (keypad polling) executes.
            
        3. Timer IRQ fires during the sensitive loop.
            
        4. Attacker measures IRQ latency.
            
        5. A shorter latency might indicate the 'else' branch, while a longer one might indicate the 'if' branch.
            
        6. By repeating this for each bit `i` of the `key_state`, the attacker can reconstruct the secret PIN.
            
- **Application to Intel SGX (Nemesis):**
    
    - **Goal:** Effectively "single-step" through an SGX enclave by interrupting each instruction and recording its corresponding IRQ latency.
        
    - **Technique:** Involves using kernel-level capabilities to set precise timer interrupts (`wrmsr TSC_DEADLINE`), trigger enclave execution, handle the Asynchronous Enclave Exit (AEX) upon interrupt, record `TSC` via `rdtscp`, and resume enclave execution (`ERESUME`).
        
    - **Revelations from Microbenchmarks (observing IRQ latency distributions):**
        
        - **Interrupted Instruction Type:** Different instructions like `add`, `rdrand`, `fscale`, `nop`, `pause` show distinct latency patterns.
            
        - **Micro-architectural Cache State:** Memory operations like `mov (%rdi),%rax` (load) or `mov %rax,(%rdi)` (store) show different latencies depending on whether the data is in cache (hit) or needs to be fetched from memory (miss). This was shown for enclave loads, stores, non-temporal stores, and unprotected flush+load sequences.
            
        - **Address Translation Latency:** Latency differences can reveal if a page table entry (PTE) for code or data had to be fetched, indicating memory page accesses.
            
    - **Macrobenchmark Example (RSA Decryption in SGX):**
        
        - An "X-ray" trace of IRQ latencies was extracted from a single dummy RSA decryption.
            
        - Distinctive high-latency instructions (like `RDRAND` used for blinding/canaries) clearly demarcated phases of the algorithm (initialization, blinding, square-and-multiply loop, unblinding).
            
        - The pattern of operations within the square-and-multiply loop (corresponding to '0's and '1's in the exponent bits) was visible, allowing for full 16-bit key recovery in the example.
            
- **Nemesis Attack Summary:**
    
    - Demonstrated as a remote side-channel for both embedded (MSP430-Sancus) and high-end TEEs (Intel SGX).
        
    - **Attack Requirements:**
        
        - Attacker and victim on the same processor.
            
        - Attacker can schedule interrupts precisely.
            
        - Victim code can be interrupted.
            
        - Instructions have measurably different execution times.
            
        - Interrupt logic waits for the completion of the current instruction.
            
        - Victim code is not "constant-time" hardened.
            

### 5. Case Study: Spectre Attacks (Exploiting Speculative Execution)

Spectre attacks exploit a performance optimization feature in modern CPUs: speculative execution.

- **Background on CPU Performance:** Modern CPUs use techniques like pipelining and speculation to improve performance.
    
    - **Speculative Execution:** When the CPU encounters a delay (e.g., waiting for data from slow memory for a conditional branch), it might _guess_ the likely execution path and start executing instructions from that path _speculatively_.
        
    - If the guess was correct, the results are committed (performance gain).
        
    - If the guess was wrong, the speculative work is discarded, and the CPU reverts to the correct path. Architecturally, no incorrect state should persist.
        
- **The "Built-in Fault Attack" Analogy:** Speculative execution means the CPU is, in a sense, "secretly making errors" (wrong guesses) on its own. While the architectural results of these errors are discarded, their _micro-architectural side effects_ (e.g., changes to cache state) can persist and be observed via side channels.
    
- **Spectre Variant 1 (Conditional Branch Misprediction Attack):**
    
    - **Vulnerable Code Pattern (Example):**
        
        ```
        if (x < array1_size) {
            y = array2[array1[x] * 512];
        }
        ```
        
    - **Attack Mechanism:**
        
        1. **Train Branch Predictor:** The attacker repeatedly calls the function with valid `x` values (where `x < array1_size`) to train the CPU's branch predictor to expect the `if` condition to be true.
            
        2. **Trigger Speculation:** The attacker then calls the function with an out-of-bounds `x` (e.g., `x` pointing to secret data outside `array1`). Crucially, `array1_size` is manipulated to be uncached (or slow to access) at this moment.
            
        3. **Speculative Execution:** While waiting for `array1_size`, the CPU predicts the `if` condition is true (due to training) and speculatively executes the body:
            
            - It reads `array1[x]`. Since `x` is out-of-bounds, this reads a secret byte from memory that the victim process has access to. Let this secret byte be `S`.
                
            - It then accesses `array2[S * 512]`. This memory access brings the cache line corresponding to `array2` at index `S*512` into the CPU cache.
                
        4. **Misprediction Realized:** Eventually, the CPU fetches the actual `array1_size`, realizes the `if` condition was false (because `x` was out-of-bounds), and discards the speculative results (the value of `y` is not architecturally updated).
            
        5. **Leak via Cache Timing:** The attacker then times accesses to all possible cache lines in `array2` (e.g., `array2[0*512]`, `array2[1*512]`, ..., `array2[255*512]`). The access to `array2[S*512]` will be significantly faster because it was brought into the cache during the speculative execution. This reveals the secret byte `S`.
            
- **Spectre as a "Messy Class of Vulnerabilities":** Many variants exist (Spectre v2, v4/Speculative Store Bypass, NetSpectre, Foreshadow, Spectre1.1, Rogue System Register Read, ret2spec, SpectreRSB, etc.), each exploiting different aspects of speculative or out-of-order execution.
    
- **Is Spectre a "Bug"?** From a narrow architectural compliance perspective, the CPU behaves as specified (architectural state is correctly rolled back). However, the specifications were insufficient for security, as they didn't account for micro-architectural side effects of speculation.
    
- **Spectre as a Symptom:**
    
    - It highlights excessive architectural ambiguity and a historical prioritization of performance and legacy compatibility over security.
        
    - It underscores the need for clearer architectural guarantees about what information can and cannot leak through micro-architectural state. Software developers cannot reliably secure their code if the underlying hardware behavior is unpredictable or underspecified regarding side effects.
        
    - A shift in mindset is needed towards designs that specialize for security, potentially co-existing with performance-optimized designs (analogous to ARM's big.LITTLE for power). A secure TCB should be less complex.
        

### 6. Side-Channel Resistance and Mitigation

Addressing side channels requires efforts in both hardware and software.

- **In Hardware:**
    
    - Stronger process isolation across the system stack, including caches and pipelines.
        
    - Predicated instructions (e.g., `cmov` on x86) to avoid secret-dependent branches.
        
    - Constant-time hardware features (e.g., arithmetic operations, interrupt logic that doesn't leak timing).
        
    - Atomic execution for critical code sections to prevent interruption or observation of intermediate states.
        
- **In Software:**
    
    - **Constant-Time Programming:** Eliminate secret-dependent branches and memory accesses. Ensure execution time and access patterns are independent of secret values.
        
    - Adherence to a "constant-time policy."
        
    - Tools and techniques (static/dynamic analysis) to detect potentially vulnerable code patterns.
        
    - **Fully Abstract Compilation:** A theoretical goal where source programs are observationally equivalent in the source language if and only if their compiled translations are observationally equivalent on the target architecture (including side channels).
        
- **General Issue:** A fundamental challenge is that side channels often rely on unspecified or poorly defined features of an architecture's execution model. This makes it extremely difficult to formally reason about whether a program is vulnerable or to provide strong security guarantees.
    

This session emphasizes that achieving robust security requires a deep understanding of hardware behavior and a co-design approach involving both hardware and software.