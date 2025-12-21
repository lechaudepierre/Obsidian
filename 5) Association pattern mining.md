## 1. Introduction & Problem Definition

### Core Problem

- **Input**: A dataset of transactions where each transaction is a set of items
- **Goal**: Find interesting associations between items in the form: `if itemset1 then itemset2 (with degree of certainty)`

### Key Definitions

- **Universe of items U**: Set of all possible items
- **Transaction Ti**: A subset of items from U
- **Database T**: Contains n transactions T₁...Tₙ
- **Binary representation**: Transactions can be represented as binary vectors

### Application Domains

- Market basket analysis: `{Bread, Cheese, Yogurt} => {Milk, Eggs}`
- Medical diagnosis: `{Coronavirus} => {Fever, Cough, Shortness of breath}`
- Power plant failure prediction
- Text mining, web log mining, classification

---

## 2. Fundamental Measures

### Support

**Definition**: The support of an itemset I is the fraction of transactions in database T that contain I as a subset.

```
Support(I) = (Number of transactions containing I) / (Total number of transactions)
```

**Example**:

- Database with 5 transactions
- Support({Milk, Yogurt}) = 3/5 = 0.6

### Confidence

**Definition**: Measures the reliability of an association rule X ⇒ Y.

```
Confidence(X ⇒ Y) = Support(X ∪ Y) / Support(X)
```

**Interpretation**: Among transactions containing X, what fraction also contains Y?

**Example**:

```
Conf({Eggs, Milk} ⇒ {Yogurt}) = Sup({Eggs, Milk, Yogurt}) / Sup({Eggs, Milk})
                                = 0.4 / 0.6 = 2/3
```

### Lift

**Definition**: Measures how much more likely Y is purchased when X is purchased, compared to Y being purchased randomly.

```
Lift(X ⇒ Y) = Support(X ∪ Y) / (Support(X) × Support(Y))
```

**Interpretation**:

- Lift > 1: Positive correlation (X and Y occur together more than expected)
- Lift = 1: Independent (no correlation)
- Lift < 1: Negative correlation (X and Y occur together less than expected)

**Example**:

```
Lift({Tea} ⇒ {Coffee}) = 0.15 / (0.2 × 0.8) = 0.15 / 0.16 < 1
→ Tea and Coffee are negatively correlated
```

### Correlation Analysis

Uses chi-square test to determine if two itemsets are statistically correlated.

```
χ² = Σ [(Observed - Expected)² / Expected]
```

**Contingency Table**:

```
       B    ¬B   Total
A     f11   f10   f1-
¬A    f01   f00   f0-
Total f-1   f-0    N
```

### Comparison of Measures

|Measure|Limitation|When to Use|
|---|---|---|
|**Support-Confidence**|Can be misleading; high confidence doesn't guarantee positive correlation|Initial filtering of frequent patterns|
|**Lift**|Can overemphasize rare co-occurrences|When you need to detect correlation direction|
|**Correlation (χ²)**|More computationally expensive|When statistical significance is important|

**Key Insight**: No single measure is perfect. The choice depends on:

- Inversion invariance properties
- Null addition invariance
- Domain knowledge
- Application requirements

---

## 3. Frequent Itemset Mining

### Definition

Given a database T and minimum support threshold (minsup), find all itemsets I where Support(I) ≥ minsup.

### The Apriori Property

**Downward Closure Property**:

- **If an itemset is frequent, then all of its subsets must also be frequent**
- **If an itemset is infrequent, then all of its supersets must be infrequent**

**Mathematical Formulation**:

```
∀ I, I' : I' ⊆ I ⇒ Support(I') ≥ Support(I)
```

**Applications in Optimization**:

1. **Pruning candidates**: If {Butter} is infrequent, no need to test {Butter, X} for any X
2. **Lattice traversal**: Can prune entire branches of the itemset lattice
3. **Reduced search space**: From 2^|U| possible itemsets to much smaller space
4. **Level-wise approach**: Generate k-itemsets only from (k-1)-frequent itemsets

---

## 4. Apriori Algorithm

### Overview

- **Strategy**: Generate-and-test with level-wise search
- **Traversal**: Breadth-first, general-to-specific
- **Key Idea**: Use Apriori property to prune candidates

### Algorithm Steps

**Input**: Database T, minimum support threshold (minsup) **Output**: All frequent itemsets

```
1. Find frequent 1-itemsets (F₁)
2. For k = 2, 3, ... until no new frequent itemsets:
   a. Generate candidates Cₖ from Fₖ₋₁
   b. Scan database to count support of candidates
   c. Fₖ = candidates in Cₖ with support ≥ minsup
3. Return ∪ Fₖ (all frequent itemsets)
```

### Detailed Example Walkthrough

**Database**:

```
TID | Items
----|----------
1   | A, B, E
2   | B, D
3   | B, C
4   | A, B, D
5   | A, C
6   | B, C
7   | A, C
8   | A, B, C, E
9   | A, B, C
```

**Minimum support count = 2**

**Step 1**: Generate C₁ (scan database)

```
C₁: A(6), B(7), C(6), D(2), E(2)
```

**Step 2**: Filter to get F₁ (all have count ≥ 2)

```
F₁: A(6), B(7), C(6), D(2), E(2)
```

**Step 3**: Generate C₂ (join F₁ with F₁)

```
C₂: AB(4), AC(4), AD(1), AE(2), BC(4), BD(2), BE(2), CD(0), CE(1), DE(0)
```

**Step 4**: Filter to get F₂

```
F₂: AB(4), AC(4), AE(2), BC(4), BD(2), BE(2)
```

**Step 5**: Generate C₃ (join F₂ with pruning)

```
C₃: ABC, ABE (others pruned by Apriori property)
Count: ABC(2), ABE(2)
```

**Step 6**: Get F₃

```
F₃: ABC(2), ABE(2)
```

**Step 7**: Generate C₄

```
C₄: ABCE
Count: ABCE(1) → Doesn't meet minsup
```

**Result**: All frequent itemsets = F₁ ∪ F₂ ∪ F₃

### Optimization Techniques

1. **Hash-based technique**: Hash itemsets to buckets; if bucket count < minsup, all itemsets in bucket are infrequent
    
2. **Transaction reduction**: Remove transactions that don't contain any k-frequent itemsets (they can't contain (k+1)-frequent itemsets)
    
3. **Partitioning**:
    
    - Divide database into partitions
    - Mine each partition separately
    - A globally frequent itemset must be frequent in at least one partition
    - Enables parallel processing
4. **Sampling**:
    
    - Run Apriori on a random sample (e.g., 10%)
    - Trade accuracy for efficiency
    - Suitable for applications that tolerate approximation

### Complexity Analysis

- **Candidate generation**: C₂ is O(|U| choose 2) = O(|U|²)
- **Database scans**: One scan per level
- **Total scans**: Maximum |U| scans

---

## 5. FP-Growth Algorithm

### Overview

**Key Innovation**: Mines frequent itemsets without candidate generation

**Strategy**:

1. Compress database into FP-tree
2. Mine FP-tree directly using divide-and-conquer

### FP-Tree Structure

**Properties**:

- **Type**: Trie (prefix tree) data structure
- **Compressed representation**: Shares common prefixes
- **Node count**: Number of transactions containing that path
- **Ordering**: Nodes ordered by frequency (most frequent first)
- **Header table**: Links to all nodes with same item for efficient traversal

**Path interpretation**:

- Root to leaf: A repeated sub-transaction (frequent pattern)
- Root to internal node: Either a frequent pattern or a prefix

### Algorithm Steps

**Phase 1: FP-Tree Construction**

```
1. Scan database once to find frequent 1-itemsets
2. Sort items by frequency (descending)
3. For each transaction:
   a. Remove infrequent items
   b. Sort remaining items by frequency
   c. Insert into FP-tree, incrementing counts on shared paths
```

**Phase 2: Mining the FP-Tree**

```
For each frequent item (from least to most frequent):
1. Find its conditional pattern base
2. Construct conditional FP-tree
3. Recursively mine conditional FP-tree
4. Combine results with the item
```

### Example Illustration

Starting with item 'I₃':

1. Find all frequent itemsets ending with I₃
2. Decompose into sub-problems: find itemsets ending with {I₁,I₃}, {I₂,I₃}, {I₄,I₃}, {I₅,I₃}
3. Recursively solve each sub-problem
4. Merge solutions

### Lattice Traversal

- **Direction**: Depth-first search
- **Pattern growth**: Builds patterns by adding items to suffixes
- **Pruning**: Uses Apriori property (if parent not frequent, children won't be)

---

## 6. Algorithm Comparison

### Brute Force Enumeration

**Approach**: Enumerate all possible itemsets (2^|U|) and count support for each

**Characteristics**:

- Complete lattice traversal
- No optimization
- Complexity: O(2^|U| × |T|)

**When acceptable**: Very small item universes only

### Apriori vs FP-Growth

|Aspect|Apriori|FP-Growth|
|---|---|---|
|**Database scans**|Multiple (k+1 scans for k-itemsets)|Two (once to build tree, once implicitly)|
|**Candidate generation**|Yes, can be huge (O(|U|
|**Memory usage**|Stores candidates|Stores compressed tree|
|**Search strategy**|Breadth-first|Depth-first|
|**Traversal**|General-to-specific|Suffix-based pattern growth|
|**When better**|Sparse datasets, low support thresholds|Dense datasets, many frequent patterns|

### Why FP-Growth Outperforms Apriori

1. **Single counting**: Database counted only once (for tree construction)
2. **No candidate generation**: Avoids expensive generate-and-test cycles
3. **Compressed structure**: Works on smaller, compressed representation
4. **Direct mining**: Mines patterns directly from tree
5. **Space efficiency**: No need to store large candidate sets

### Performance Considerations

**Apriori performs better when**:

- Dataset is sparse
- Few frequent itemsets exist
- Support threshold is high

**FP-Growth performs better when**:

- Dataset is dense
- Many frequent patterns exist
- Support threshold is low
- Memory can accommodate FP-tree

---

## 7. Association Rule Mining

### Definition

An association rule is X ⇒ Y where:

- X, Y are itemsets
- X ∩ Y = ∅ (disjoint)

### Mining Process (Two-Step)

**Step 1: Find Frequent Itemsets**

- Use Apriori, FP-Growth, or other algorithms
- All itemsets with Support ≥ minsup

**Step 2: Generate Rules from Frequent Itemsets**

```
For each frequent itemset I:
    For each non-empty subset s of I:
        Rule: s ⇒ (I - s)
        If Confidence(s ⇒ (I - s)) ≥ minconf:
            Output the rule
```

### Quality Assessment

**Valid Association Rule Criteria**:

1. Support(X ∪ Y) ≥ minsup
2. Confidence(X ⇒ Y) ≥ minconf

### Example: Rule Generation

**Given**: Frequent itemsets with minsup=0.4, minconf=0.8

```
{Bread, Milk}, {Milk, Cheese}, {Eggs, Milk}, {Eggs, Yogurt}, {Eggs, Milk, Yogurt}
```

**From {Eggs, Milk, Yogurt}**:

```
{Eggs} ⇒ {Milk, Yogurt}
{Milk} ⇒ {Eggs, Yogurt}
{Yogurt} ⇒ {Eggs, Milk}
{Eggs, Milk} ⇒ {Yogurt}: Conf = 0.4/0.6 = 0.67 < 0.8 (rejected)
{Eggs, Yogurt} ⇒ {Milk}: Check confidence...
{Milk, Yogurt} ⇒ {Eggs}: Check confidence...
```

### Rule Assessment Framework

**Objective Measures** (Automatic filtering):

- Support, Confidence, Lift, Correlation
- Good for initial filtering

**Subjective Measures**:

- Domain expert knowledge
- Business rules
- Template-based search (e.g., "rules that increase sales of bio products")

**Interactive Assessment**:

- Visualization tools
- User feedback
- Actionable insights

---

## 8. Advanced Topics

### Multi-Level Association Rules

**Concept Hierarchy**:

- Level 0 (root): Most general
- Level k (leaves): Most specific
- Example: Milk → 2% Milk → Brand X 2% Milk

**Mining Strategy**: Top-down

1. Start at Level 1
2. Find frequent itemsets
3. Move to more specific levels
4. Stop when no more frequent itemsets

**Support Threshold Strategies**:

1. **Uniform minsup**: Same threshold for all levels
    
    - ✓ Simple, single parameter
    - ✓ Can use parent-child pruning
    - ✗ Too high: only high-level rules
    - ✗ Too low: redundant rules
2. **Reduced minsup at lower levels**: Lower threshold for specific items
    
    - Handles varying item frequencies
3. **Group-based minsup**: Different thresholds for different item groups
    
    - High minsup for frequent items
    - Low minsup for rare but important items (e.g., expensive cameras)

### Multi-Dimensional Association Rules

**Traditional**: Single dimension

```
buys(X, Camera) ⇒ buys(X, Printer)
```

**Multi-dimensional**: Multiple predicates

```
age(X, "20-29") ∧ occupation(X, "student") ⇒ buys(X, "laptop")
```

**Mining approach**: Count predicate fulfillment instead of item occurrences

---

## 9. Applications of Frequent Pattern Mining

1. **Market Basket Analysis**: Product recommendations, store layout optimization
    
2. **Noise Filtering (Data Cleaning)**: Frequent patterns less likely to be noise
    
3. **Data Reduction/Compression**: Remove very frequent non-interesting patterns (e.g., stop words)
    
4. **Recommender Systems**: "Users who did X also did Y"
    
5. **Cluster Discovery**: Co-authorship networks, community detection
    
6. **Web Usage Mining**: User navigation patterns
    
7. **Bioinformatics**: Gene expression patterns
    

---

## 10. Key Exam Strategies

### Problem-Solving Approach

**For Apriori Algorithm**:

1. Count items to create C₁
2. Filter by minsup to get F₁
3. Join Fₖ with Fₖ to generate Cₖ₊₁
4. Apply Apriori property to prune candidates
5. Count remaining candidates
6. Repeat until no frequent itemsets found

**For Measure Calculations**:

1. Build contingency table if needed
2. Calculate support values first
3. Then calculate confidence/lift/correlation
4. Interpret results (correlation direction, statistical significance)

**For Rule Extraction**:

1. Identify all frequent itemsets
2. For each itemset, generate all possible rules
3. Calculate confidence for each rule
4. Filter by minconf
5. Assess using multiple measures

### Common Pitfalls to Avoid

1. Confusing support with confidence
2. Assuming high confidence implies positive correlation
3. Forgetting to apply Apriori property for pruning
4. Miscounting in candidate generation
5. Not considering all subsets when generating rules

---

## Summary Checklist for Exam

- [ ] Understand support, confidence, lift, and their limitations
- [ ] Can explain Apriori property and how it enables pruning
- [ ] Can manually execute Apriori algorithm on small datasets
- [ ] Understand FP-tree structure and how FP-Growth works
- [ ] Can compare algorithms: brute force vs Apriori vs FP-Growth
- [ ] Can identify optimization techniques and when to apply them
- [ ] Can extract rules from frequent itemsets
- [ ] Can calculate and interpret multiple quality measures
- [ ] Understand when to use which measure
- [ ] Know applications and practical considerations