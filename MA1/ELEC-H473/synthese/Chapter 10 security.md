## Resume: Security in Processor Architectures (ELEC-H473 Session 10)

This document provides an overview of security concepts within microprocessor architectures, focusing on how hardware can enhance software protection against various threats.

### 1. Introduction & Motivation

- **Problem:** Modern computing systems face numerous security threats. Sandboxing mechanisms like the one used in Chromium, while effective, rely on the underlying OS security, which can have vulnerabilities.
    
    - **Example:** CVE-2020-0981 allowed a Chrome sandbox escape due to a Windows bug, demonstrating how OS vulnerabilities can compromise application-level security.
        
- **Goal:** Processor architectures aim to provide hardware-level support to protect code and data confidentiality, integrity, and availability against sophisticated attacks.
    

### 2. Processor Architecture & Security Principles

- **Traditional Principles:** Focus on performance via Caching, Pipelining, Predicting, Parallelizing, Indirection (Virtualization), and Specialization.
    
- **Security Principles:** Extend hardware to actively counter threats. Processor security aims to protect against intelligent adversaries, distinct from reliability which handles random errors.
    
- **Secure Processors:** A subset of processors with added hardware features for logical isolation and protection.
    

### 3. Hardware Security Mechanisms

Hardware can provide security through various features:

- **Encryption:** Protecting data confidentiality.
    
- **Process Isolation:** Using security rings (privilege levels) to separate components.
    
- **Probabilistic Defences:** Techniques like Address Space Layout Randomization (ASLR) and Shadow Stacks make attacks harder.
    
- **Trusted Execution Environments (TEEs):** Isolated environments within the processor to execute sensitive code and protect data.
    
- **Memory Protection:** Mechanisms like Memory Protection Keys or Memory Encryption protect data even from privileged software or physical access.
    
- **Temporal Guarantees:** Ensuring timing properties for security-critical operations.
    
- **Minimizing Trusted Computing Base (TCB):** Reducing the amount of software/hardware that needs to be trusted, ideally to just the hardware and the specific secure application code.
    

### 4. Privilege Levels & Isolation

- **Ring-Based Security:** Traditional systems use rings (Ring 0 for Kernel, Ring 3 for Applications) where inner rings are more privileged.
    
- **Extended Levels:** Modern systems add more privileged levels (Ring -1: Hypervisor, Ring -2: SMM, Ring -3: Management Engine). This maintains a linear trust model where lower levels are trusted by higher levels.
    
- **Orthogonal Separation:** Some architectures (e.g., ARM TrustZone) introduce separate "secure" and "normal" worlds, creating a lattice of privileges rather than a purely linear hierarchy.
    
- **Breaking Linear Trust:** TEEs like Intel SGX or AMD SEV allow enclaves or secure VMs to run isolated from even the most privileged system software (OS, Hypervisor), breaking the traditional linear trust model.
    

### 5. Trusted Computing & TEEs

- **Trusted Computing Base (TCB):** The set of hardware and software components responsible for enforcing security policies for a TEE. The goal is often to minimize the TCB.
    
- **Trusted Execution Environment (TEE):** An environment providing confidentiality and integrity for code and data executed within it. Protected code/data units are often called "enclaves".
    
- **Key Trusted Computing Concepts**:
    
    - **Remote Attestation:** Allows a remote party to verify the hardware and software configuration of a platform before interacting with it. This involves nonces and cryptographic measurements signed by a hardware key.
        
    - **Sealed Storage:** Encrypting data such that it can only be decrypted by a specific software component on a specific device.
        
    - **Memory Curtaining/Encryption:** Isolating or encrypting memory used by secure components.
        
- **Goals:** Protect against software attacks (malicious OS/VMM) and potentially physical attacks (memory snooping). Reduce the application attack surface.
    
- **Use Cases**: Digital Rights Management (DRM), preventing cheating in online games, secure remote computation, privacy-preserving services.
    
    - **Example:** Signal uses Intel SGX to perform private contact discovery without revealing the user's social graph to Signal's servers.
        

### 6. Specific Architectures & Examples

- **Intel SGX (Software Guard Extensions):**
    
    - Provides protected enclaves within an application's address space.
        
    - Uses memory encryption and restricted entry points (call gates).
          
    - Supports remote attestation via a complex infrastructure involving provisioning services and attestation services managed by Intel.
        
- **Sancus:**
    
    - A light-weight TEE for embedded microcontrollers (based on openMSP430).
        
    - Provides software component isolation, cryptography, attestation, and secure I/O.
        
    - Uses a cryptographic key hierarchy derived from node and software provider keys for attestation and secure communication.
        
    - Low overhead (≤2 kLUTs, +6% power).
        
    - **Example (VulCAN):** Uses Sancus to secure Controller Area Network (CAN) communication in automotive systems by authenticating messages within enclaves, protecting against injection/replay attacks even if ECU software is compromised.
        
- **Other Architectures:** The document briefly mentions and compares others like ARM TrustZone, AMD SEV, TPM, Bastion, SMART, etc., highlighting differences in features like attestation, sealing, TCB type, target ISA, and side-channel resistance.
    

### 7. Limitations & Challenges

- **TCB Vulnerabilities:** Bugs within the TEE's own hardware or trusted software can undermine security.
    
- **Physical & Supply Chain Attacks:** Hardware Trojans inserted during manufacturing or physical probing of the chip are often outside the threat model.
    
- **Side-Channel Attacks:** Information can leak through indirect means like timing variations, power consumption, or electromagnetic emissions, which are often underestimated.
    
    - **Example:** Spectre attacks exploit speculative execution, a performance optimization, to leak data. Interrupt latency can also be a side channel.
        
- **Trusted Code Bugs:** TEEs don't protect against bugs within the application code running _inside_ the enclave.
    
- **Availability:** While integrity/confidentiality might be protected, attackers might still affect service availability (e.g., by crashing untrusted OS components needed by the enclave).
    

### 8. Conclusion

Processor security architectures, particularly TEEs like SGX and Sancus, offer powerful hardware-based mechanisms for isolating code and data, reducing attack surfaces, and enabling features like remote attestation and sealed storage. However, they are not a silver bullet and face limitations regarding side-channel attacks, TCB vulnerabilities, and the security of the code running within the TEE itself.