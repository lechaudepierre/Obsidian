![[Capture d’écran 2025-12-20 à 14.43.14.png]]
###  1. Illustrate and compare the different model validation methods: holdout, cross validation, bootstrap.

#### 1.1 Holdout Method

##### Description

- Divide labeled data into **two disjoint subsets**, randomly selected:
    - **Training set**: 60-75% of data
    - **Testing set**: 25-40% of data
- Validate the model accuracy using the test set

##### Illustration

```
Dataset (100 records)
        ↓
   [Random Split]
        ↓
    ┌───┴────┐
    ↓        ↓
Training  Testing
(70%)     (30%)
  ↓         ↓
Build     Evaluate
Model     Accuracy
```

##### Characteristics

- **Simple and fast**: One split, one training, one test
- **Potential issue**: Classes over-represented in training will be under-represented in testing
- **High variance**: Results depend heavily on the random split
- **Data inefficiency**: Only uses a portion of data for training

---

#### 1.2 Cross-Validation

##### Description

- Divide labeled data into **m disjoint subsets** of equal size (n/m)
- One segment used for testing, other (m-1) segments used for training
- **Repeat m times**, rotating which segment is used for testing
- **Average results** are reported

##### K-Fold Cross-Validation Illustration

```
Dataset divided into 5 folds:
┌─────┬─────┬─────┬─────┬─────┐
│ F1  │ F2  │ F3  │ F4  │ F5  │
└─────┴─────┴─────┴─────┴─────┘

Iteration 1: │TEST │TRAIN│TRAIN│TRAIN│TRAIN│ → Accuracy₁
Iteration 2: │TRAIN│TEST │TRAIN│TRAIN│TRAIN│ → Accuracy₂
Iteration 3: │TRAIN│TRAIN│TEST │TRAIN│TRAIN│ → Accuracy₃
Iteration 4: │TRAIN│TRAIN│TRAIN│TEST │TRAIN│ → Accuracy₄
Iteration 5: │TRAIN│TRAIN│TRAIN│TRAIN│TEST │ → Accuracy₅

Final Accuracy = Average(Accuracy₁, ..., Accuracy₅)
Variance = measures statistical confidence
```

##### Variants

###### Leave-One-Out Cross-Validation

- Special case: **m = n** (n-fold cross-validation)
- Each iteration: 1 record for testing, (n-1) records for training
- Most accurate but computationally expensive

###### Stratified Cross-Validation

- Splits folds so that **every fold has almost the same distribution of class labels** as in the complete dataset
- Leads to **less pessimistic estimates**
- Recommended when class distribution is imbalanced

##### Characteristics

- **Reliable**: Uses all data for both training and testing
- **Variance calculation**: Helps determine statistical confidence intervals of error
- **Model qualification**: Average performance indicates if model is good enough
- **More computation**: Requires training m models

---

#### 1.3 Bootstrap

##### Description

- Data is **sampled uniformly with replacement** to create training set
- A set of size n is sampled n times:
    - Each time: randomly select a record, copy it, return it back
- Results in n records that may contain **duplicates** and **miss some original records**

##### Mathematical Properties

- Probability record is missed in **one sampling**: 1 - 1/n
- Probability record is missed in **n samplings**: (1 - 1/n)ⁿ
- For large n: (1 - 1/n)ⁿ → **1/e ≈ 0.368**
- **Fraction included at least once**: 1 - 1/e ≈ **63.2%**

##### Illustration

```
Original Dataset (n=10):
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
         ↓
  [Sample with replacement]
         ↓
Bootstrap Sample:
[3, 7, 3, 1, 9, 2, 8, 7, 5, 4]
 ↑     ↑           ↑
Duplicates (≈36.8%)

Missing: [6, 10] (≈36.8%)
Unique: [1, 2, 3, 4, 5, 7, 8, 9] (≈63.2%)
```

##### Variants

###### Standard Bootstrap (Optimistic)

```
Training: Bootstrap sample (≈63.2% unique + 36.8% duplicates)
Testing: Original complete dataset
Problem: High overlap (63.2%) → overly optimistic accuracy
```

###### Leave-One-Out Bootstrap (Pessimistic)

```
For each record x:
  - Test x only on bootstrap samples that DON'T include x
  - Calculate accuracy for x
Overall accuracy = average over all records
Result: More pessimistic estimate
```

###### 0.632-Bootstrap (Compromise)

```
A = overall accuracy
Aₗ = leave-one-out bootstrap accuracy (pessimistic)
Aₜ = standard bootstrap accuracy (optimistic)

Final: A = 0.368 × Aₜ + 0.632 × Aₗ
```

- Balances optimistic and pessimistic estimates
- Weighted by the probabilities (36.8% / 63.2%)

##### Characteristics

- **Good for small datasets**: Makes efficient use of limited data
- **Variance estimation**: Can repeat k times to obtain confidence intervals
- **Bias issues**: Standard version is optimistic, leave-one-out is pessimistic
- **Complexity**: 0.632-bootstrap provides compromise but is more complex

---

#### Comparison Table

|Method|Training Data|Testing Data|Iterations|Bias|Variance|Computation|Best For|
|---|---|---|---|---|---|---|---|
|**Holdout**|60-75%|25-40%|1|Can be high|High|Low|Large datasets, quick validation|
|**Cross-Validation**|(m-1)/m|1/m|m|Low|Medium|Medium|Standard practice, balanced approach|
|**Leave-One-Out**|(n-1)/n|1/n|n|Very low|Low|Very high|Small datasets, maximum accuracy|
|**Bootstrap**|≈63.2% unique|100%|k repeats|Depends on variant|Medium|Medium|Small datasets, confidence intervals|
|**Stratified CV**|(m-1)/m|1/m|m|Very low|Low|Medium|Imbalanced classes|

---

#### When to Use Each Method

##### Use Holdout when:

- Dataset is large (>10,000 records)
- Quick validation is needed
- Computational resources are limited

##### Use Cross-Validation when:

- Standard validation approach needed
- Dataset is medium to large
- Want reliable estimate with variance
- **Use Stratified variant** if classes are imbalanced

##### Use Leave-One-Out when:

- Dataset is very small (<100 records)
- Maximum accuracy estimate is critical
- Computational time is not a constraint

##### Use Bootstrap when:

- Dataset is small
- Need confidence intervals
- Standard methods show high variance
- Use **0.632-bootstrap** for balanced estimate

---

#### Key Insights from Slides

1. **Variance matters**: Helps determine statistical confidence intervals of error
2. **Stratification helps**: Less pessimistic estimates when maintaining class distribution
3. **Trade-offs exist**: Computation vs. accuracy vs. reliability
4. **Choose based on data size**: Smaller data needs more sophisticated methods
5. **Average results**: Multiple iterations provide more reliable estimates than single split

### 2. Describe the different data preparation tasks, their goals, and few examples. The details of the methods are not required. It is enough to know the general idea

#### 2.1 Feature Extraction
**Goal:** Derive meaningful features from raw data to create feature vectors that can be used by algorithms.

**Examples:**
- Images: Color histograms, visual words
- Documents: Named-entity recognition (persons, organizations, locations), word count histograms, stop word removal

#### 2.2 Type Portability
**Goal:** Convert data from one type to another to unify structure and fit algorithm requirements.

**Examples:**
- Text to numeric (e.g., bag of words)
- Time series to discrete sequence or numeric
- Numeric to categorical (discretization): Age ranges like [0,5], [6,12], [13,18], etc.

#### 2.3 Data Cleaning
**Goal:** Handle dirty data by fixing incomplete, noisy, or inconsistent entries.

**Examples:**
- **Incomplete data:** Missing values like occupation=" "
- **Noisy data:** Errors like Salary="-10"
- **Inconsistent data:** Contradictions like Age="42" and Birthday="03/07/1997"

**Common approaches:**
- Delete entire records
- Impute missing values using mean, median, or similar records
- Binning and smoothing techniques
- Regression fitting

#### 2.4 Scaling and Normalization
**Goal:** Ensure features are on comparable scales so no single feature dominates distance calculations.

**Examples:**
- Normalization: Transform values to have mean=0 and standard deviation=1
- Scaling: Map values to range [0,1]

### 3. Describe without detail the concepts and methods of data cleaning, scaling, normalization, and distance measures. 

#### 3.1 Data Cleaning
**Concept:** Handle dirty data that is incomplete, noisy, or inconsistent.

**Methods:**
- Delete entire records with problems
- Impute/estimate missing values (using global constant, mean, similar records, interpolation, regression)
- Binning: Sort data into bins and smooth by bin mean, median, or boundary
- Kernel smoothing: Replace values with weighted averages of neighbors (e.g., kNN smoother)
- Regression: Fit data into a regression function to smooth noise

#### 3.2 Scaling and Normalization
**Concept:** Ensure features are on comparable scales to prevent some dimensions from dominating distance calculations.

**Methods:**
- **Normalization:** Replaces each value using the formula (value - mean) / standard deviation. Most values fall in range [-3, 3] assuming normal distribution.
- **Scaling:** Maps values to the range [0, 1]

#### 3.3 Distance Measures
**Concept:** Quantify similarity or distance between objects for data mining algorithms.

**Methods:**
- **Lp-norm:** Euclidean distance (p=2), Manhattan distance (p=1)
- **Generalized Minkowski:** Weighted distance where certain features can be more important
- **Edit Distance:** Measures similarity between strings by counting insertions, deletions, and replacements needed to transform one string to another
- **Other measures:** Frechet distance, histogram distance, cosine similarity, graph edit distance

### 4. Explain and apply the Lp-norm distance, and the Edit distance

#### 4.1 Lp-norm Distance

**Explanation:** The Lp-norm calculates distance between two points in multidimensional space using the formula:

distance = (Σ|xi - yi|^p)^(1/p)

**Common cases:**

- **Manhattan distance (p=1):** Sum of absolute differences
- **Euclidean distance (p=2):** Square root of sum of squared differences
- **Generalized Minkowski:** Can assign different weights to features based on their importance

**Example Application:** Calculate distance between points (1, 2) and (3, 4):

- **p=1 (Manhattan):** |1-3| + |2-4| = 2 + 2 = 4
- **p=2 (Euclidean):** √[(1-3)² + (2-4)²] = √[4 + 4] = √8 ≈ 2.83

#### 4.2 Edit Distance

**Explanation:** Edit distance measures similarity between two strings by counting the minimum number of operations (insertions, deletions, replacements) needed to transform one string into another.

**Example Application:** Transform "Mahmoud" to "Mohammed":

Operations needed:

- Replace 'a' with 'o'
- Insert 'a'
- Replace 'u' with 'm'
- Replace 'o' with 'e'

**Edit distance = 4** (with each operation costing 1)

**Note:** If replacements cost 2 (Levenshtein distance), the distance becomes 7

**Computation method:** Dynamic programming with O(i×j) time complexity, where i and j are the lengths of the two strings.
![[Pasted image 20251221100504.png]]