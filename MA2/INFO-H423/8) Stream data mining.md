## Overview

**Modern Data Streams**: Applications generating continuous, high-volume data flows:

- Credit card transactions
- Wearable sensors
- Connected vehicles
- Industry 4.0 systems
- Internet of Things (IoT) devices

---

## Data Streams vs. Databases

|Databases|Data Streams|
|---|---|
|Data at rest|Data in motion/flow|
|Store then query|Query on-the-fly|
|Multiple passes allowed|Single pass only|
|Fixed size|Potentially infinite|

---

## Key Challenges in Stream Data Mining

### 1. One-Pass Constraint

- **Problem**: Data is assumed infinite; cannot store everything or make multiple passes
- **Impact**: Traditional algorithms like K-means need adaptation
- **Solution**: Use synopsis structures and approximation algorithms

### 2. Concept Drift

- **Problem**: Data distribution and statistical properties evolve over time
- **Impact**: Models become outdated; outlier definitions change
- **Solution**: Adaptive algorithms that update continuously

### 3. Resource Constraints

- **Problem**: Variable arrival rates require highly efficient algorithms
- **Impact**: Must process in real-time with limited memory
- **Solution**: Probabilistic data structures with bounded memory

### 4. Massive Domain

- **Problem**: Attributes may have extremely large numbers of distinct values (e.g., social network IDs)
- **Impact**: Traditional frequent itemset mining becomes infeasible
- **Solution**: Sketching and sampling techniques

---

## Synopsis Structures for Massive Domains

**Three Fundamental Questions**:

1. **Have I seen you before?** → Bloom Filter
2. **How many times did I see you?** → Count-Min Sketch
3. **How many distinct persons attended?** → Flajolet-Martin / HyperLogLog

---

## 1. Bloom Filter

### Purpose

**Question**: Has a particular element ever occurred in the data stream?

### Structure

A Bloom filter consists of:

- **Binary bit array** of length `m`: `[0, 0, ..., 0]` (indices 0 to m-1)
- **w independent hash functions**: `h₁(.), h₂(.), ..., hᵥ(.)`

### How It Works

**Hash Functions Explained**:

- Each hash function takes an element and **computes a position** in the array
- Example: `h₁("alice") = 3` means "set bit at position 3"
- The hash functions **don't move** - they calculate which position to use
- Different hash functions give different positions for the same element

**Detailed Example**:

Let's say we have:

- Array length m = 10 (positions 0-9)
- 3 hash functions (w = 3)

```
Initial array: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
Position:       0  1  2  3  4  5  6  7  8  9
```

**Insertion - Adding "alice"**:

```
Step 1: Compute positions
  h₁("alice") = 2   → Position 2
  h₂("alice") = 5   → Position 5
  h₃("alice") = 8   → Position 8

Step 2: Set those bits to 1
  Array: [0, 0, 1, 0, 0, 1, 0, 0, 1, 0]
          Position: 2     5        8 are now 1
```

**Insertion - Adding "bob"**:

```
Step 1: Compute positions
  h₁("bob") = 1   → Position 1
  h₂("bob") = 5   → Position 5 (collision with alice!)
  h₃("bob") = 7   → Position 7

Step 2: Set those bits to 1
  Array: [0, 1, 1, 0, 0, 1, 0, 1, 1, 0]
          New:  1              7
          Already 1: 2, 5, 8 (from alice)
```

**Query - Check if "alice" exists**:

```
Step 1: Compute positions
  h₁("alice") = 2   → Check position 2 → 1 ✓
  h₂("alice") = 5   → Check position 5 → 1 ✓
  h₃("alice") = 8   → Check position 8 → 1 ✓

Result: ALL bits are 1 → "Probably YES"
```

**Query - Check if "charlie" exists** (never inserted):

```
Step 1: Compute positions
  h₁("charlie") = 4   → Check position 4 → 0 ✗
  h₂("charlie") = 6   → Check position 6 → 0 ✗
  h₃("charlie") = 9   → Check position 9 → 0 ✗

Result: At least one bit is 0 → "Definitely NO"
```

**Query - Check if "dave" exists** (never inserted, but collision):

```
Step 1: Compute positions
  h₁("dave") = 2   → Check position 2 → 1 ✓ (set by alice)
  h₂("dave") = 5   → Check position 5 → 1 ✓ (set by alice & bob)
  h₃("dave") = 7   → Check position 7 → 1 ✓ (set by bob)

Result: ALL bits are 1 → "Probably YES" ⚠️ FALSE POSITIVE!
```

**Key Points**:

- Hash functions **compute the position** (index) in the array
- The **element doesn't move** - we just mark positions in the bit array
- Same element always gives same positions (deterministic)
- Different hash functions give different positions for same element
- Collisions cause false positives (multiple elements set same bits)
### Key Properties

✅ **No False Negatives**: If Bloom filter says "NO" → element is definitely absent

⚠️ **Possible False Positives**: If Bloom filter says "YES" → element might be present (or hash collision)

### Real-World Applications

**Example 1: Username Availability**

```
Stream: user registrations (millions of usernames)
Query: "Is username 'alice123' already taken?"
Bloom Filter: Quick check before database lookup
```

**Example 2: Malicious URL Detection**

```
Stream: known malicious URLs
Query: "Is this URL dangerous?"
Bloom Filter: Fast screening before detailed analysis
```

**Example 3: Cache Systems**

```
Stream: cached web pages
Query: "Is this page in cache?"
Bloom Filter: Avoid expensive disk lookups for non-existent items
```

### Tuning Parameters

- **Larger m** (array size): Fewer false positives, more memory
- **More hash functions w**: Better accuracy, slower operations
- **Trade-off**: Balance accuracy vs. speed vs. memory

---

## 2. Count-Min Sketch

### Purpose

**Question**: How many times has a particular element appeared in the data stream?

### Structure

A Count-Min sketch consists of:

- **w numeric arrays** (rows), each of length `m`
- **w independent hash functions**: `h₁(.), h₂(.), ..., hᵥ(.)`
- All arrays initialized to 0

### How It Works

**Construction** (when element x arrives):

```
For each row i (i = 1 to w):
    1. Compute hash: j = hᵢ(x)
    2. Increment counter: array[i][j]++
```

**Querying** (count frequency of element y):

```
For each row i (i = 1 to w):
    1. Compute hash: j = hᵢ(y)
    2. Read counter: count[i] = array[i][j]
    
Return: minimum(count[1], count[2], ..., count[w])
```

### Why Multiple Rows (w > 1)?

**The Problem with w=1 (Single Row)**:

Imagine we only have 1 row with 5 positions:

```
Stream: "alice", "bob", "alice", "charlie", "alice", "bob"
Array: [0, 0, 0, 0, 0]
       0  1  2  3  4

Insert "alice" (appears 3 times):
  h("alice") = 2 → increment position 2 three times
  Array: [0, 0, 3, 0, 0]

Insert "bob" (appears 2 times):
  h("bob") = 2 → COLLISION! Same position as alice
  Array: [0, 0, 5, 0, 0]  ← position 2 now has 3+2=5

Insert "charlie" (appears 1 time):
  h("charlie") = 2 → COLLISION AGAIN!
  Array: [0, 0, 6, 0, 0]  ← position 2 now has 3+2+1=6

Query: How many times did "alice" appear?
  h("alice") = 2 → read position 2 → 6
  WRONG! True answer is 3, but we get 6 due to collisions
```

**With w=1, we cannot distinguish between**:

- Actual frequency of our element
- Extra counts from hash collisions

**The Solution: Multiple Rows (w > 1)**:

Now use w=3 rows:

```
Row 1: [0, 0, 0, 0, 0]  uses h₁(.)
Row 2: [0, 0, 0, 0, 0]  uses h₂(.)
Row 3: [0, 0, 0, 0, 0]  uses h₃(.)

Insert "alice" 3 times:
  h₁("alice") = 2 → Row 1: [0, 0, 3, 0, 0]
  h₂("alice") = 1 → Row 2: [0, 3, 0, 0, 0]
  h₃("alice") = 4 → Row 3: [0, 0, 0, 0, 3]

Insert "bob" 2 times:
  h₁("bob") = 2 → Row 1: [0, 0, 5, 0, 0] ← collision with alice
  h₂("bob") = 3 → Row 2: [0, 3, 0, 2, 0] ← no collision
  h₃("bob") = 0 → Row 3: [2, 0, 0, 0, 3] ← no collision

Insert "charlie" 1 time:
  h₁("charlie") = 1 → Row 1: [0, 1, 5, 0, 0]
  h₂("charlie") = 1 → Row 2: [0, 4, 0, 2, 0] ← collision with alice
  h₃("charlie") = 4 → Row 3: [2, 0, 0, 0, 4] ← collision with alice

Query: How many times did "alice" appear?
  Row 1: h₁("alice") = 2 → read 5 (collided with bob)
  Row 2: h₂("alice") = 1 → read 4 (collided with charlie)
  Row 3: h₃("alice") = 4 → read 4 (collided with charlie)
  
  Take MINIMUM(5, 4, 4) = 4
  Still overestimate, but much better than 5 or 6!
```

### Why Take the Minimum?

**Key Insight**: Hash collisions can only **increase** counts, never decrease them.

- If a row has **no collision** for our element → true count
- If a row has **collision** → inflated count (overestimate)
- The **minimum** across rows is the **least contaminated** estimate
- More rows → higher probability at least one row has minimal collisions

**Think of it like witnesses**:

- 1 witness (w=1): Might be mistaken
- Multiple witnesses (w>1): Take the most conservative testimony
- The minimum is the "most reliable witness"

**Mathematical Guarantee**:

- Count-Min NEVER underestimates (always ≥ true count)
- With w rows, probability of large overestimation decreases exponentially
- More rows (w) = better accuracy, but more memory

### Real-World Applications

**Example 1: Network Traffic Analysis**

```
Stream: IP packets (billions per day)
Query: "How many packets from IP 192.168.1.100?"
Count-Min: Track traffic per IP without storing all IPs
```

**Example 2: Real-Time Analytics**

```
Stream: user clicks on website
Query: "How many times was product X clicked today?"
Count-Min: Maintain counts for millions of products efficiently
```

**Example 3: Fraud Detection**

```
Stream: credit card transactions
Query: "How many transactions from card number XXXX-1234?"
Count-Min: Monitor transaction frequency per card
```

**Example 4: Word Frequency in Text Streams**

```
Stream: tweets/social media posts
Query: "How often does hashtag #AI appear?"
Count-Min: Track trending topics in real-time
```

### Accuracy

- More rows (w) → Better accuracy
- Larger arrays (m) → Fewer collisions
- Typical error: Over-estimates due to collisions, never under-estimates

---

## 3. Flajolet-Martin Algorithm

### Purpose

**Question**: How many **distinct** elements are in the data stream?

**Example**: How many unique people attended a concert?

### Key Insight

Uses probability of trailing zeros in binary representations to estimate cardinality.

### How It Works

**Setup**:

- Hash function `h(·)`: Maps stream element x → integer in range `[0, 2^L - 1]`
- Typically L = 64 (to handle large cardinalities)
- Requirement: `2^L` should be greater than expected distinct elements

**Algorithm Steps**:

1. **For each element x in stream**:
    
    - Compute `h(x)` and convert to binary
    - Count trailing zeros (zeros at the end)
    - Example: `000010111010110101000000000` has **9 trailing zeros**
2. **Track maximum**:
    
    - Keep `Rₘₐₓ` = maximum number of trailing zeros seen
3. **Estimate cardinality**:
    
    ```
    n ≈ 2^Rₘₐₓ / 0.77351
    ```
    

### Mathematical Intuition

**Probability of trailing zeros**:

- Probability of r trailing zeros ≈ `1 / 2^r`
- If we have n distinct elements:
    - Expect to see `log₂(n)` trailing zeros eventually

**Formula**: `E[Rₘₐₓ] = log₂(0.77351 × n)`

**Reversing**: `n = 2^Rₘₐₓ / 0.77351`

### Example Walkthrough

```
Concert attendance stream:
Person 1: h(ID) = 000...010000 → 4 trailing zeros → Rₘₐₓ = 4
Person 2: h(ID) = 000...011000 → 3 trailing zeros → Rₘₐₓ = 4
Person 3: h(ID) = 000...000100 → 2 trailing zeros → Rₘₐₓ = 4
Person 4: h(ID) = 001...000000 → 6 trailing zeros → Rₘₐₓ = 6
...
After stream: Rₘₐₓ = 8
Estimate: n ≈ 2^8 / 0.77351 ≈ 331 distinct people
```

### Problems and Improvements

**Problem 1**: High Variance

- What if the first person triggers a record of trailing zeros?
- Algorithm would overestimate dramatically

**Solution 1**: Use Multiple Hash Functions

- Use k independent hash functions
- Get k estimates: `R₁, R₂, ..., Rₖ`
- Take **average** or **median** of estimates
- Reduces variance significantly

**Problem 2**: Multiple Entry Points (Distributed Streams)

- Concert hall has many doors (distributed counting)
- How to combine Rₘₐₓ from different locations?

**Solution 2**: Maximum is Mergeable

```
max(max(A), max(B)) = max(A ∪ B)
```

- Each door tracks its own Rₘₐₓ
- Final estimate: `max(Rₘₐₓ_door1, Rₘₐₓ_door2, ...)`

### Real-World Applications

**Example 1: Unique Website Visitors**

```
Stream: page views (millions)
Query: "How many unique users visited today?"
Flajolet-Martin: Count distinct user IDs efficiently
```

**Example 2: Database Query Optimization**

```
Stream: database records
Query: "How many distinct values in this column?"
Flajolet-Martin: Estimate cardinality for query planning
```

**Example 3: Network Monitoring**

```
Stream: network packets
Query: "How many unique source IPs?"
Flajolet-Martin: Detect unusual traffic patterns
```

---

## 4. HyperLogLog

### Evolution

```
Flajolet-Martin (1985) → LogLog (2003) → HyperLogLog (2007)
```

### Why HyperLogLog?

**Improvements over Flajolet-Martin**:

- ✅ **Avoids multiple hash functions** for error reduction
- ✅ **Uses bucketing** strategy instead
- ✅ **Better accuracy** with same memory
- ✅ **Industry standard** for cardinality estimation

### How It Works

**Key Innovation**: Stochastic Averaging

1. **Divide hash space into m buckets**
2. **Each element** goes to one bucket (using first few bits of hash)
3. **Track Rₘₐₓ per bucket** (trailing zeros in remaining bits)
4. **Harmonic mean** of bucket values → final estimate

### Accuracy

**Standard Error**: `1.04 / √m`

- Where m = number of buckets

**Examples**:

- m = 256 buckets → ~6.5% error
- m = 2048 buckets → ~2.3% error
- m = 16384 buckets → ~0.8% error

### Memory Efficiency

**Extremely compact**:

- 1.5 KB of memory → count billions of distinct items
- 12 KB of memory → 0.8% standard error

### Real-World Applications

**Example 1: Redis Database**

```
Command: PFADD key element [element ...]
Command: PFCOUNT key [key ...]
Use: Count unique users, unique IPs, unique events
Memory: ~12 KB per key for billions of items
```

**Example 2: Google BigQuery**

```
Function: APPROX_COUNT_DISTINCT(column)
Use: Fast cardinality estimates on massive datasets
Speedup: 1000x faster than exact COUNT(DISTINCT)
```

**Example 3: Social Media Analytics**

```
Stream: user interactions (likes, shares, views)
Query: "How many unique users engaged with this content?"
HyperLogLog: Track engagement across millions of posts
```

**Example 4: Internet Traffic Analysis**

```
Stream: billions of web requests
Query: "Unique visitors per website?"
HyperLogLog: ISPs use this to generate traffic reports
```

**Example 5: Advertising Technology**

```
Stream: ad impressions
Query: "Unique users who saw this ad?"
HyperLogLog: Calculate reach without storing user IDs
```

### When to Use HyperLogLog

✅ **Use when**:

- Need to count distinct elements
- Approximate answer is acceptable
- Memory is limited
- Speed is critical
- Working with billions of items

❌ **Don't use when**:

- Need exact counts
- Working with small datasets (< 10,000 items)
- Need to retrieve actual elements (HLL only counts)

---

## Comparison Table

|Method|Purpose|Memory|Accuracy|Use Case|
|---|---|---|---|---|
|**Bloom Filter**|"Have I seen this?"|O(m) bits|False positives possible|Membership testing|
|**Count-Min**|"How many times?"|O(w × m) counters|Over-estimates|Frequency counting|
|**Flajolet-Martin**|"How many distinct?"|O(1) integers|High variance|Basic cardinality|
|**HyperLogLog**|"How many distinct?"|O(m) small values|~1-2% error|Production cardinality|

---

## Applying These Methods to Real Data Streams

### Step-by-Step Application Guide

#### 1. Choose the Right Tool

**Decision Tree**:

```
Need to check existence? → Bloom Filter
Need frequency counts? → Count-Min Sketch
Need distinct count? → HyperLogLog (or Flajolet-Martin for learning)
```

#### 2. Set Parameters

**Bloom Filter**:

- Array size m: Based on expected elements and acceptable false positive rate
- Hash functions w: Typically 3-7 functions

**Count-Min Sketch**:

- Rows w: 5-10 for good accuracy
- Columns m: Depends on memory budget

**HyperLogLog**:

- Buckets m: Powers of 2 (256, 1024, 2048, 16384)
- Trade-off: More buckets = better accuracy but more memory

#### 3. Implementation Pattern

```python
# Pseudocode for stream processing

# Initialize data structure
structure = HyperLogLog(m=2048)

# Process stream
for element in data_stream:
    structure.add(element)
    
    # Optional: Query at intervals
    if time_to_query():
        result = structure.query()
        report(result)

# Final query
final_count = structure.query()
```

#### 4. Real-World Integration Examples

**Web Analytics**:

```
Stream: User clicks
Bloom Filter: Check if user seen before (new vs. returning)
Count-Min: Track clicks per URL
HyperLogLog: Count unique daily visitors
```

**IoT Sensor Network**:

```
Stream: Sensor readings
Bloom Filter: Detect duplicate sensor IDs (fault detection)
Count-Min: Count readings per sensor
HyperLogLog: Count active sensors
```

**E-commerce**:

```
Stream: Product views
Bloom Filter: Check if user viewed product (recommendation filter)
Count-Min: Track views per product
HyperLogLog: Count unique viewers per product
```

---

## Key Takeaways

1. **Stream data cannot be stored entirely** → Use synopsis structures
2. **Bloom Filter**: Fast membership testing with no false negatives
3. **Count-Min Sketch**: Frequency estimation with over-estimation guarantee
4. **Flajolet-Martin**: Pioneering cardinality estimation algorithm
5. **HyperLogLog**: Industry-standard cardinality estimation (Redis, BigQuery)
6. **Trade-off**: All methods sacrifice exactness for speed and memory efficiency
7. **Production-ready**: HyperLogLog is the go-to for real-world cardinality problems

---

## Resources

- Interactive HyperLogLog Demo: http://content.research.neustar.biz/blog/hll.html
- Research Papers: See references in original document
- Libraries: Redis (PFADD/PFCOUNT), Python (hyperloglog package), Java (stream-lib)