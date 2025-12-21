![[Capture d’écran 2025-12-19 à 11.15.52.png]]
### 1. Explain the steps of supervised learning

#### 1.1 Training Phase

- Start with a **training set** - a collection of labeled records where each record has:
    - Multiple **attributes** (features/inputs)
    - One **class attribute** (the target/output label)
- **Build a model** that learns the relationship between attributes and the class (e.g., using algorithms like ID3, CART, C4.5 for decision trees)
#### 1.2 Prediction Phase
- Apply the trained model to **previously unseen records**
- The model assigns a class label to new records based on their attribute values
- Goal: achieve maximum prediction accuracy
#### 1.3 Model Validation
- Evaluate model performance using the **test set** (data not used in training)
- Common validation strategies include:
    - **Holdout**: Split data into training (60-75%) and testing sets
    - **Cross-validation**: Divide data into k folds, train on k-1 folds, test on the remaining fold, repeat k times
    - **Bootstrap**: Sample data with replacement to create training sets
#### 1.4 Model Selection
- Compare validation results across different models
- Consider accuracy averages and variance
- Select the most appropriate model for the problem
---
**Key Goal**: Previously unseen records should be assigned a class as accurately as possible.

### 2. Describe classification as one the DM tasks

Not much things to add regarding the previous section

### 3. Explain/illustrate the concept of Entropy, Gain, Gain-ratio

#### 3.1 Entropy

##### Concept

- **Entropy** is a measure from Information Theory developed by Shannon (1948)
- Measures the **uncertainty** about a source of messages
- The more uncertain a receiver is, the more information needed to know what message has been sent

##### Key Properties

- **Minimum entropy (0)**: Source always sends the same message (no uncertainty)
- **Maximum entropy**: Source sends n messages with equal probability (maximum uncertainty)
- To identify a message from n equally-likely options, you need **log₂(n)** yes/no questions (bits of information)

##### Formula

For a source S that produces k messages with probabilities (p₁, p₂, ..., pₖ):

```
Entropy(S) = -Σ(pᵢ × log₂(pᵢ))  for i = 1 to k
```

##### Example

If class attribute is 'play' with 5 "No" and 9 "Yes" out of 14 instances:

```
Entropy(5/14, 9/14) = -((5/14) × log₂(5/14) + (9/14) × log₂(9/14))
                     = 0.940
```

---

#### 3.2 Entropy-Split

##### Concept

When splitting a dataset into **r groups** (r-way split at a decision tree node):

- Calculate the entropy within each group
- Take the **weighted average** over all groups

##### Formula

```
Entropy-Split(attribute) = Σ(|Sⱼ|/|S| × Entropy(Sⱼ))  for j = 1 to r
```

Where:

- |Sⱼ| = size of group j
- |S| = total size of dataset

##### Example

For attribute "humidity" with values {high, normal}:

```
Entropy-Split(humidity) = (7/14) × E(humidity=high) + (7/14) × E(humidity=normal)
                        = (7/14) × (-(4/7)log₂(4/7) - (3/7)log₂(3/7))
                          + (7/14) × (-(1/7)log₂(1/7) - (6/7)log₂(6/7))
                        = 0.788
```

---

#### 3.3 Information Gain

##### Concept

- Measures **how much information** we gain about the output by knowing an input attribute
- Represents the **reduction in entropy** after splitting on an attribute
- Used in ID3 algorithm to select the best split attribute

##### Formula

```
Gain(attribute) = Entropy(S) - Entropy-Split(attribute)
```

##### Interpretation

- **Large Gain values**: More desirable (more information gained)
- **Small Gain values**: Less useful for classification

##### Example

For attribute "temperature":

```
Gain(temperature) = E(S) - Entropy-Split(temperature)
                  = 0.940 - 0.911
                  = 0.029
```

---

#### 3.4 Gain Ratio

##### Problem with Gain

- Attributes with **many unique values** (like ID numbers) have very high gain
- This leads to **useless decision trees** with overfitting
- Need a way to penalize attributes with too many values

##### Solution: Gain Ratio

Proposed by Quinlan (1986):

```
GainRatio(X, S) = Gain(X) / Entropy(S when X is the label attribute)
```

##### Properties

- **Proportional to Gain**: Still favors attributes with higher information gain
- **Inversely proportional to attribute entropy**: Discourages attributes with many values
- Balances informativeness with simplicity

##### When to Use

- Use **Gain Ratio** instead of pure Gain when:
    - Dataset contains attributes with many distinct values
    - Risk of overfitting due to high-cardinality attributes
    - Need to avoid selecting unique identifiers as split attributes

---

#### Summary Comparison

|Metric|Purpose|Formula|Best Value|
|---|---|---|---|
|**Entropy**|Measure uncertainty|-Σ(pᵢ × log₂(pᵢ))|Lower is less uncertain|
|**Entropy-Split**|Weighted entropy after split|Σ(weightⱼ × Entropy(groupⱼ))|Lower is better split|
|**Gain**|Information gained by split|Entropy(before) - Entropy(after)|Higher is better|
|**Gain Ratio**|Normalized gain|Gain / Entropy(attribute)|Higher is better|

#### Usage in ID3 Algorithm

1. Calculate **Gain** (or **Gain Ratio**) for all attributes
2. Select attribute with **highest value**
3. Split dataset on that attribute
4. Recursively repeat for each branch
5. Stop when all instances in a node have the same class

### 4. Explain and apply the ID3 algorithm on a given dataset
![[Capture d’écran 2025-12-19 à 11.32.34.png | 400]]
##### Overview

- **Invented by**: J. Ross Quinlan (1979)
- **Based on**: Information Theory by Shannon (1948)
- **Approach**: Top-down, greedy, no backtracking
- **Selection Criterion**: Information Gain

##### Algorithm Steps

1. Calculate the **Entropy** of the target attribute (class)
2. For each attribute:
    - Calculate **Entropy-Split** (weighted entropy after split)
    - Calculate **Gain** = Entropy(class) - Entropy-Split(attribute)
3. Select the attribute with the **highest Gain**
4. Create a node with that attribute
5. **Recursively** repeat for each branch until:
    - All instances have the same class (pure node), OR
    - No more attributes to split on

---

#### Application on Tennis Dataset

##### Dataset Summary

- **Total instances**: 14
- **Attributes**: outlook, temperature, humidity, windy
- **Class**: play (Yes/No)
- **Class distribution**: Yes = 9, No = 5

---

#### Step 1: Calculate Initial Entropy

##### Class Distribution

- Play = Yes: 9 instances
- Play = No: 5 instances

##### Calculation

```
E(S) = -(9/14) × log₂(9/14) - (5/14) × log₂(5/14)
     = -(9/14) × (-0.637) - (5/14) × (-1.485)
     = 0.410 + 0.530
     = 0.940
```

---

#### Step 2: Calculate Gain for Each Attribute

##### Attribute 1: OUTLOOK

###### Partitions

- **sunny**: 5 instances (Yes=2, No=3)
- **overcast**: 4 instances (Yes=4, No=0)
- **rainy**: 5 instances (Yes=3, No=2)

###### Entropy Calculations

```
E(outlook=sunny) = -(2/5) × log₂(2/5) - (3/5) × log₂(3/5)
                 = 0.971

E(outlook=overcast) = -(4/4) × log₂(4/4) - (0/4) × log₂(0/4)
                    = 0  (pure node!)

E(outlook=rainy) = -(3/5) × log₂(3/5) - (2/5) × log₂(2/5)
                 = 0.971
```

###### Entropy-Split

```
Entropy-Split(outlook) = (5/14) × 0.971 + (4/14) × 0 + (5/14) × 0.971
                       = 0.347 + 0 + 0.347
                       = 0.694
```

###### Gain

```
Gain(outlook) = 0.940 - 0.694 = 0.246
```

---

##### Attribute 2: TEMPERATURE

###### Partitions

- **hot**: 4 instances (Yes=2, No=2)
- **mild**: 6 instances (Yes=4, No=2)
- **cool**: 4 instances (Yes=3, No=1)

###### Entropy Calculations

```
E(temperature=hot) = -(2/4) × log₂(2/4) - (2/4) × log₂(2/4)
                   = 1.0

E(temperature=mild) = -(4/6) × log₂(4/6) - (2/6) × log₂(2/6)
                    = 0.918

E(temperature=cool) = -(3/4) × log₂(3/4) - (1/4) × log₂(1/4)
                    = 0.811
```

###### Entropy-Split

```
Entropy-Split(temperature) = (4/14) × 1.0 + (6/14) × 0.918 + (4/14) × 0.811
                           = 0.286 + 0.393 + 0.232
                           = 0.911
```

###### Gain

```
Gain(temperature) = 0.940 - 0.911 = 0.029
```

---

##### Attribute 3: HUMIDITY

###### Partitions

- **high**: 7 instances (Yes=3, No=4)
- **normal**: 7 instances (Yes=6, No=1)

###### Entropy Calculations

```
E(humidity=high) = -(3/7) × log₂(3/7) - (4/7) × log₂(4/7)
                 = 0.985

E(humidity=normal) = -(6/7) × log₂(6/7) - (1/7) × log₂(1/7)
                   = 0.592
```

###### Entropy-Split

```
Entropy-Split(humidity) = (7/14) × 0.985 + (7/14) × 0.592
                        = 0.493 + 0.296
                        = 0.789
```

###### Gain

```
Gain(humidity) = 0.940 - 0.789 = 0.151
```

---

##### Attribute 4: WINDY

###### Partitions

- **false**: 8 instances (Yes=6, No=2)
- **true**: 6 instances (Yes=3, No=3)

###### Entropy Calculations

```
E(windy=false) = -(6/8) × log₂(6/8) - (2/8) × log₂(2/8)
               = 0.811

E(windy=true) = -(3/6) × log₂(3/6) - (3/6) × log₂(3/6)
              = 1.0
```

###### Entropy-Split

```
Entropy-Split(windy) = (8/14) × 0.811 + (6/14) × 1.0
                     = 0.463 + 0.429
                     = 0.892
```

###### Gain

```
Gain(windy) = 0.940 - 0.892 = 0.048
```

---

#### Step 3: Select Best Attribute (Root Node)

##### Gain Summary

|Attribute|Gain|
|---|---|
|**outlook**|**0.246** ⭐|
|temperature|0.029|
|humidity|0.151|
|windy|0.048|

**Winner**: **outlook** (highest gain)

---

#### Step 4: Build Tree Recursively

##### Root Node: OUTLOOK

```
                    outlook
                   /    |    \
              sunny  overcast  rainy
```

###### Branch: outlook = overcast

- All 4 instances are "Yes"
- **Leaf node**: Play = **Yes**

###### Branch: outlook = sunny (5 instances: Yes=2, No=3)

Need to split further. Calculate gain for remaining attributes:

**Remaining data (sunny only)**:

- 5 instances (2 Yes, 3 No)
- E(sunny) = 0.971

Calculate gains for temperature, humidity, windy on this subset:

For **humidity**:

- high: 3 instances (0 Yes, 3 No) → E = 0
- normal: 2 instances (2 Yes, 0 No) → E = 0
- Gain(humidity) = 0.971 - 0 = **0.971** ⭐

**Best split**: humidity

```
outlook = sunny
    → humidity
        → high: Play = No
        → normal: Play = Yes
```

###### Branch: outlook = rainy (5 instances: Yes=3, No=2)

Need to split further.

**Remaining data (rainy only)**:

- 5 instances (3 Yes, 2 No)

For **windy**:

- false: 3 instances (3 Yes, 0 No) → E = 0
- true: 2 instances (0 Yes, 2 No) → E = 0
- Gain(windy) = 0.971 - 0 = **0.971** ⭐

**Best split**: windy

```
outlook = rainy
    → windy
        → false: Play = Yes
        → true: Play = No
```

---

#### Final Decision Tree

```
                        outlook
                    /      |      \
                sunny  overcast   rainy
                  |       |         |
              humidity   Yes     windy
               /    \            /    \
           high  normal      false  true
            |       |          |      |
           No      Yes        Yes    No
```

---

#### Decision Rules

From the tree, we can extract rules:

1. **IF outlook = overcast THEN play = Yes**
2. **IF outlook = sunny AND humidity = high THEN play = No**
3. **IF outlook = sunny AND humidity = normal THEN play = Yes**
4. **IF outlook = rainy AND windy = false THEN play = Yes**
5. **IF outlook = rainy AND windy = true THEN play = No**

---
