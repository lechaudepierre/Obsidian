## 🎯 **What is Channel Capacity?**

Channel capacity answers a fundamental question: **"What is the maximum transmission rate achievable through a noisy channel?"**

Think of it like a highway: even with traffic (noise), there's a maximum flow of cars (information) that can pass through per hour.

---

## 📊 **4.1 Memory-less Discrete Channel**

### **Basic Setup**

A discrete channel consists of:

- **Input alphabet X** (what we send)
- **Output alphabet Y** (what we receive)
- **Transition probabilities p(y|x)** (probability of receiving y when sending x)

**Memory-less means**: The current output only depends on the current input, not on past inputs/outputs.

### **Two Definitions of Capacity**

#### **1. Operational Definition**

> **C = Highest transmission rate R (bits/channel use) achievable with arbitrarily low error probability**

In simple terms: How many bits can we reliably send per channel use?

#### **2. Informational Definition**

> **C = max_{p(x)} I(X:Y)**

Where I(X:Y) is the mutual information between input X and output Y.

**Key insight**: We maximize over all possible input distributions p(x) to find the best strategy.

---

## 🔍 **Understanding Through Examples**

### **Example 1: Noiseless Binary Channel**

```
Input:  0 → Output: 0 (always)
Input:  1 → Output: 1 (always)
```

**Analysis**:

- No noise = perfect transmission
- I(X:Y) = H(X) - H(X|Y) = H(X) - 0 = H(X)
- Maximum H(X) = 1 bit (when input is uniform: p(0) = p(1) = 1/2)
- **Capacity C = 1 bit/channel use**

### **Example 2: Binary Symmetric Channel (BSC)**

```
Input 0 → Output 0 (probability 1-p), Output 1 (probability p)
Input 1 → Output 1 (probability 1-p), Output 0 (probability p)
```

**Key insight**: Each bit has probability p of being flipped.

**Analysis**:

- I(X:Y) = H(Y) - H(Y|X)
- H(Y|X) = H[p, 1-p] (entropy of the noise)
- When input is uniform: H(Y) = 1
- **Capacity C = 1 - H₂[p]**

**What this means**:

- If p = 0 (no noise): C = 1 - 0 = 1 bit/use
- If p = 0.5 (maximum noise): C = 1 - 1 = 0 bits/use
- If p = 0.1 (10% error rate): C ≈ 0.53 bits/use

### **Example 3: Binary Erasure Channel (BEC)**

```
Input 0 → Output 0 (probability 1-α), Output "Erased" (probability α)
Input 1 → Output 1 (probability 1-α), Output "Erased" (probability α)
```

**Analysis**:

- Some bits are lost (erased) but never corrupted
- **Capacity C = 1 - α**

**Interpretation**: We can reliably transmit (1-α) fraction of bits, since erased bits can be retransmitted.

### **Example 4: Noisy Typewriter**

- 26 letters input, but each maps to next letter cyclically
- A→B, B→C, ..., Z→A

**Smart strategy**: Use only every other letter (A,C,E,G,...) to avoid overlap

- **Capacity C = log₂(13) ≈ 3.7 bits/use**

---

## 🔄 **4.2 Symmetric Channels**

### **What Makes a Channel Symmetric?**

A channel is **symmetric** if:

1. Each row of transition matrix is a permutation of every other row
2. Each column is a permutation of every other column

A channel is **weakly symmetric** if:

1. Each row is a permutation of every other row
2. All columns sum to the same value

### **Why Symmetry Matters**

**Theorem**: For (weakly) symmetric channels: **C = log|Y| - H[r̄]**

Where r̄ is any row of the transition matrix.

**Key insight**: For symmetric channels, uniform input distribution is always optimal!

### **Example: Symmetric Channel**

```
Transition matrix:
     [0.3  0.2  0.5]
P =  [0.5  0.3  0.2]
     [0.2  0.5  0.3]
```

- Each row is a permutation of [0.3, 0.2, 0.5]
- **Capacity C = log₂(3) - H[0.3, 0.2, 0.5]**

---

## 📈 **Properties of Channel Capacity**

### **Five Key Properties**

1. **C ≥ 0** (capacity is never negative)
    
    - Since I(X:Y) ≥ 0 always
2. **C ≤ log|X|** (bounded by input alphabet size)
    
    - Can't send more than log|X| bits per symbol
3. **C ≤ log|Y|** (bounded by output alphabet size)
    
    - Can't distinguish more than |Y| different outputs
4. **I(X:Y) is continuous in p(x)**
    
    - Small changes in input distribution cause small changes in mutual information
5. **I(X:Y) is concave in p(x)**
    
    - This guarantees a unique maximum exists

---

## 🎯 **4.3 Shannon's Noisy Coding Theorem (2nd Theorem)**

### **The Main Result**

> **If transmission rate R ≤ C, then there exists a code such that error probability can be made arbitrarily small.**

**In mathematical terms**:

- If R ≤ C: ∀ε > 0, ∃ code such that P_error < ε
- If R > C: No code can achieve reliable transmission

### **What This Means Practically**

1. **Achievability**: Below capacity, perfect transmission is theoretically possible
2. **Converse**: Above capacity, errors are unavoidable
3. **Sharp threshold**: Capacity is the exact boundary between possible and impossible

### **The Intuition Behind the Proof**

**Think of it as a "sphere packing" problem**:

1. **Received signals** form "clouds" around each transmitted codeword
2. **Noise** creates overlap between these clouds
3. **Below capacity**: We can choose codewords far enough apart that clouds don't overlap significantly
4. **Above capacity**: No matter how we choose codewords, clouds will overlap too much

### **Key Concepts in the Proof**

- **Jointly typical sequences**: Input-output pairs that are "typical" together
- **Random codes**: Instead of finding the best code, prove that random codes work well on average
- **Error probability**: Goes to 0 exponentially fast when R < C

---

## 💡 **Key Takeaways**

### **Practical Implications**

1. **Design Rule**: Always operate below capacity (R < C) for reliable communication
    
2. **Trade-offs**:
    
    - Higher rates → higher error probability
    - Lower rates → more reliable but slower transmission
3. **Noise Management**:
    
    - Can't eliminate noise, but can work around it
    - Error-correcting codes become essential near capacity

### **Fundamental Insights**

1. **Capacity sets fundamental limits** - no coding scheme can exceed it
2. **Optimal input distributions** vary by channel type
3. **Symmetric channels** have uniform optimal inputs
4. **Shannon's theorem** is both achievability and impossibility result

### **Connection to Real World**

- **WiFi, 4G/5G**: Use sophisticated codes operating near channel capacity
- **Satellite communication**: Must account for very noisy channels
- **Data storage**: Hard drives use error-correcting codes based on these principles

---

## 🔗 **Chapter Connections**

- **Chapter 1 (Entropy)**: Provides tools for calculating I(X:Y)
- **Chapter 2 (AEP)**: Justifies "typical sequences" used in capacity proofs
- **Chapter 3 (Source Coding)**: Deals with compression, channel coding with transmission
- **Chapters 5-7 (Error Correction)**: Practical codes that approach capacity limits

The beauty of information theory is how these concepts all fit together to give us both theoretical limits and practical designs!