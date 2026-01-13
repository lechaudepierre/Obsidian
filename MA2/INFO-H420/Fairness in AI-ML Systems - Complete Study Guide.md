## 1. Introduction: Public Perception of AI Systems

### Survey Results (Pegasystems, 5,000 consumers)
- **34%** say they interact with AI systems
- **Trust Issues:**
  - Only 9% very comfortable interacting with AI
  - Machines not considered trustworthy
- **Bias Concerns:**
  - 53% believe systems can show bias in decisions
- **Moral Capabilities:**
  - 56% don't believe it's possible to develop morally-behaving systems
- **Comfort Levels:** 35% comfortable, 28% uncomfortable, 37% neither

---

## 2. Responsible Data Science

### Definition
Practice of using data and data-driven techniques in ways that are:
- **Ethical**
- **Transparent**
- **Respectful** of individual and societal rights

### Key Concepts
1. **Data Privacy & Security:** Protect sensitive individual data
2. **Transparency:** Clear about data sources, methods, and limitations
3. **Ethical Principles:** Exhibit desirable ethical standards
4. **Human Control:** Allow people to understand and control systems

---

## 3. Responsible AI/ML Landscape

### Core Principles (from 84 AI ethics guidelines review)

| Principle | Frequency | Key Concepts |
|-----------|-----------|--------------|
| **Transparency** | 73/84 | Explainability, interpretability, communication |
| **Justice & Fairness** | 68/84 | Equality, equity, non-bias, non-discrimination, diversity |
| **Non-maleficence** | 60/84 | Security, safety, harm prevention |
| **Responsibility** | 60/84 | Accountability, liability |
| **Privacy** | 47/84 | Personal/private information protection |
| **Beneficence** | 41/84 | Well-being, social good |
| **Freedom & Autonomy** | 34/84 | Consent, choice, self-determination |
| **Trust** | 28/84 | Trust as the "end goal" |

### Key Areas
- **Fairness, Accountability, Transparency, Ethics** (FATE)
- Dedicated conferences: ACM FAccT, AIES
- Academic courses at Berkeley, Cornell, Princeton
- EU regulations: AI Act, Ethics Guidelines for Trustworthy AI
- AI Incident Database tracking >2000 reports of AI harms

---

## 4. Case Study: COMPAS Risk Assessment

### Background
- **System:** COMPAS by Northpointe
- **Purpose:** Estimate likelihood of criminal reoffending
- **Use:** Guides US judges' decisions on sentences and bail amounts

### ProPublica Study Findings
- **Dataset:** 10,000+ offenders analyzed
- **Overall Performance:** Same recall (true positive rate) for Black and White defendants

### Discrimination Evidence

**False Positives (Higher Risk but Didn't Reoffend):**
- Blacks **almost twice as likely** as Whites to be incorrectly labeled higher risk

**False Negatives (Lower Risk but Did Reoffend):**
- Blacks **much less likely** than Whites to be incorrectly labeled lower risk

### Key Issues Identified
1. **Fairness:** System discriminates against racial groups
2. **Transparency:** Algorithm operations unknown
3. **Accountability:** Unclear who is responsible

---

## 5. What is Fairness?

### Dictionary Definition
> "The state of being free from bias or injustice"

### Political Science Perspective

**Distributive Justice:** Fair allocation of resources among diverse community members

From John Rawls' "A Theory of Justice":
- **Justice as fairness**
- Social cooperation should be fair to all citizens as free and equal

#### Types of Fair Allocation
1. **Equality of Outcome:** Each person receives same amount
2. **Equality of Opportunity:** Equal grounds for competing for resources
3. **Social Welfare:** What benefits society most

### Legal Systems Perspective: Non-Discrimination

#### 1. Disparate Treatment
- Intentional discrimination on protected groups
- Not "color-blind"
- Example: Only African Americans required to take pre-employment test

#### 2. Disparate Impact
- Disproportionate impact on protected groups
- Example: All tested, but only African Americans eliminated

#### 3. Affirmative Action
- Support historically disadvantaged groups
- Quota systems
- Example: Address gender imbalance in STEM

---

## 6. Algorithmic Fairness

### Core Definition
> **Machine-made decisions should NOT discriminate against individuals**

### Types of Fairness

#### Group Fairness
- Protected group (defined by sensitive attribute like race/gender) should be treated similarly to non-protected groups
- Typically: protected (disadvantaged) vs. non-protected (advantaged)

#### Individual Fairness
- Specific individual should be treated similarly to similar other individuals

---

## 7. Fairness in Classification

### Classification Setup
- **Task:** Binary classification (positive or negative classes)
  - Example: Accept or reject loan application
- **Groups:** Protected vs. non-protected
- **Mechanism:** Score predicts likelihood of positive class
  - Score threshold determines classification outcome
  - Above threshold → positive, below → negative

### Key Metrics
- **True Positive (TP):** Correctly predicted positive
- **True Negative (TN):** Correctly predicted negative
- **False Positive (FP):** Incorrectly predicted positive
- **False Negative (FN):** Incorrectly predicted negative
- **Recall (TPR):** TP / (TP + FN) - sensitivity
- **Accuracy:** (TP + TN) / Total

### Main Fairness Notions

#### 1. Equal Treatment (Color-Blindness/Fairness by Unawareness)
- **Definition:** Same threshold for all groups
- **Problem:** May lead to unequal impact across groups

#### 2. Group/Demographic/Statistical Parity
- **Definition:** Same positive rate across groups
- **Formula:** P(positive | protected) = P(positive | non-protected)
- **Problem:** May not account for actual qualifications

#### 3. Equal Opportunity
- **Definition:** Same true positive rate (recall) across groups
- **Formula:** P(positive | actually positive, protected) = P(positive | actually positive, non-protected)
- **Focus:** Fair for those who actually deserve positive outcome

### Important Note
> **You cannot have it all!** Various impossibility theorems show these fairness definitions are mutually exclusive.

---

## 8. Fairness in Ranking

### Ranking Context
- **Task:** Rank objects (usually people) based on goodness/merit score
  - Example: Candidates for job position
- **Key Difference from Classification:** No threshold, just list length (cutoff)
- **Solution:** Must change scoring function to achieve fairness

### Group Parity in Ranking
- **Basic:** Top-N should have equal representation from each group
- **Stronger:** Every prefix of ranked list should maintain group parity
- **General Form:** Achieve target ratio/mix (e.g., 40% blue, 60% orange)

### Exposure in Rankings

#### Concept
- Rankings **expose** items to users
- **Position Bias:** Higher positions get more exposure
- Exposure depends **only on ranking position**, not on relevance or utility

#### Fairness of Exposure
- **Principle:** Exposure should be proportional to merit
  - If item is x times better → should receive x times more exposure
- **Challenge:** Almost never fully achievable
  - Exposure is fixed (determined by position)
  - Merit depends on variable ranking scores

#### Solutions
1. **Amortized Fairness:** Be fair in long-term
2. **Probabilistic Rankings:** Fair probabilistically

### Optimization Approaches
Trade-off between utility and fairness:
- **maxU:** Maximize utility subject to fairness constraint
- **maxF:** Maximize fairness subject to utility constraint
- **U+F:** Maximize combination of utility and fairness

---

## 9. Fairness in Recommender Systems

### Stakeholder Framework
> Recommenders = match **users** with **items**

**Three Stakeholder Groups:**
1. **Consumers** (end users, buyers)
2. **Producers** (item providers, creators, sellers)
3. **System Provider**

Multi-sided market with possibly conflicting interests

### Fairness Framework: Three Questions

#### 1. Fair for Whom? (Protected Groups)
- **Consumers:** End users, buyers
- **Producers:** Item providers, creators, sellers

#### 2. Distribution of What Resource?
- **Accuracy:** Effectiveness, utility, satisfaction, quality of service
  - Items are good matches for users
  - Users are good matches for items
- **Exposure:** Attention, visibility
  - What users see
  - To whom items are recommended

#### 3. When is Distribution Fair?
- Specify **optimal state** of fairness
- Define **(un)fairness measure** to quantify distance from optimal

### Taxonomy of Fairness Definitions

| For Whom? | Accuracy | Exposure |
|-----------|----------|----------|
| **Consumer** | - Prediction accuracy<br>- Ranking accuracy | - Equal exposure to desired items |
| **Producer** | - Ranking accuracy | - Exposure coverage<br>- Equal exposure<br>- Calibrated exposure |

### Accuracy Metrics

#### View 1: Matching (Regression Task)
- **Metrics:** Mean Absolute Error (MAE), Root Mean Square Error (RMSE)
- Measures prediction quality of ratings

#### View 2: Ranking (Ranking Task)
- **Metrics:** Reciprocal Rank (RR), Discounted Cumulative Gain (DCG)
- Measures ranking quality

### Example Fairness Definitions

#### Consumer Accuracy Fairness
> "Males and females should experience the same quality of service"

**Operationalization:**
1. Group users by gender
2. Measure total accuracy per gender
3. Assess if distribution is fair

#### Producer Accuracy Fairness
> "When recommending products, all brands should have similar accuracy"

**Operationalization:**
1. Group items by brand
2. Measure total accuracy per brand
3. Assess if distribution is fair

#### Consumer Exposure Fairness
> "When recommending jobs, males and females should see same number of executive openings"

**Operationalization:**
1. Group users by gender
2. Measure total exposure per gender to desired items
3. Assess if distribution is fair

#### Producer Exposure Fairness
> "When recommending products, all brands should be fairly exposed"

**Operationalization:**
1. Group items by producer
2. Measure total exposure per producer
3. Assess if distribution is fair

---

## 10. How to Achieve Fairness

### Three Types of Intervention
```
Data → Task → Outcome
  ↓      ↓        ↓
Pre-   In-    Post-
processing  processing
```

#### 1. Pre-processing
- Modify **data** before training
- Remove bias from training data

#### 2. In-processing
- Modify **algorithm/task** during training
- Add fairness constraints to learning objective

#### 3. Post-processing
- Modify **outcome** after prediction
- Adjust predictions/rankings to satisfy fairness

### Post-Processing for Group Parity in Ranking

**Step 1:** Determine minimum representation from protected group in each prefix
- Example: At least ⌊40%N⌋ orange in every top-N

**Step 2:** Create two sublists (by group), sorted on score

**Step 3:** Build ranked list incrementally
- At each rank: Choose **best** from either group
- **Unless** must choose from protected group (to ensure minimum representation)
- Ensures fairness while maximizing utility

### Post-Processing for Fair Exposure in Ranking

**Goal:** Change item positions to improve fairness

**Trade-off:** Balance between fairness and accuracy/utility

**Reranking Objectives:**
- Original list optimizes internal utility measure (relevance, CTR)
- Reranking must trade off utility vs. fairness

**Possibilities:**
- **maxU:** Maximize utility given fairness constraint
- **maxF:** Maximize fairness given utility constraint
- **U+F:** Maximize combination of utility and fairness

---

## 11. Other Fairness Challenges

### Continuous Protected Attributes

**Problem:** Standard definitions compare protected vs. non-protected groups
- What about **continuous** attributes? (age, income, location)

**Issues:**
1. **Groups not pre-defined:** Must define groups somehow
2. **Gerrymandering Risk:** Purposefully set group boundaries to hide discrimination

### Location Fairness

**Goal:** Outcomes should be independent of location

**Example:** Loan application accepts/rejects per location
- Visual inspection of geographic distribution
- Detect spatial clustering of unfair outcomes

**Challenges:**
- Defining "fairness" across continuous geographic space
- Avoiding artificial boundary effects

### Discovering Unfairness via Explanations

**Problem:** Previous definitions require groups known in advance

**Alternative Approach:** **Counterfactual Explanations**

**Example:**
- Person x does **not** receive loan
  - x = {Race=Caucasian, Gender=Female, Income=Low}
- Question: What are **minimal changes** to receive loan?
- Answer: {Gender=Female} → {Gender=Male}
- **Conclusion:** Shows discrimination based on gender!

**Advantage:** Can discover previously unknown discrimination patterns

---

## Key Takeaways

### Main Concepts
1. **Fairness is multifaceted:** No single definition fits all contexts
2. **Trade-offs exist:** Cannot simultaneously satisfy all fairness criteria
3. **Context matters:** Different tasks (classification, ranking, recommendation) need different approaches
4. **Stakeholders matter:** Must consider both consumers and producers
5. **Multiple resources:** Fairness applies to accuracy AND exposure

### Practical Implications
- Fairness must be explicitly designed into systems
- Post-processing can improve fairness with minimal accuracy loss
- Continuous monitoring needed to detect emerging bias
- Transparency and accountability are essential companions to fairness

### Open Challenges
- Handling continuous/intersectional protected attributes
- Discovering unknown sources of discrimination
- Balancing fairness with other system objectives
- Long-term vs. short-term fairness considerations

---

## Quick Reference Tables

### Fairness Notions in Classification

| Notion | Definition | Formula |
|--------|------------|---------|
| Equal Treatment | Same threshold for all | threshold_protected = threshold_non-protected |
| Group Parity | Same positive rate | P(Ŷ=1 \| protected) = P(Ŷ=1 \| non-protected) |
| Equal Opportunity | Same true positive rate | P(Ŷ=1 \| Y=1, protected) = P(Ŷ=1 \| Y=1, non-protected) |

### Recommender System Fairness Matrix

|  | Accuracy | Exposure |
|--|----------|----------|
| **Consumer** | Equal recommendation quality across user groups | Equal access to desired items |
| **Producer** | Equal ranking quality for items | Equal visibility/attention |

### Intervention Strategies

| Type | Stage | Example |
|------|-------|---------|
| Pre-processing | Before training | Remove bias from data, re-sampling |
| In-processing | During training | Add fairness constraints to loss function |
| Post-processing | After prediction | Adjust thresholds, re-rank results |

---

## References & Further Reading

### Key Papers Mentioned
- [2019 Nature Machine Intelligence] A. Jobin et al. - "Artificial Intelligence: the global landscape of ethics guidelines"
- [2018 KDD] A. Singh, T. Joachims - "Fairness of Exposure in Rankings"
- [2018 SIGIR] A. Biega et al. - "Equity of Attention: Amortizing Individual Fairness in Rankings"
- ProPublica COMPAS Analysis: https://www.propublica.org/article/machine-bias-risk-assessments-in-criminal-sentencing

### Resources
- AI Incident Database: https://incidentdatabase.ai
- EU Ethics Guidelines for Trustworthy AI
- EU AI Act
- ACM FAccT Conference
- AIES Conference

---

## Study Questions

1. What are the main differences between group fairness and individual fairness?
2. Why can't we satisfy all fairness definitions simultaneously in classification?
3. How does fairness in ranking differ from fairness in classification?
4. What is the difference between accuracy fairness and exposure fairness in recommender systems?
5. What are the three types of intervention for achieving fairness, and when would you use each?
6. How do counterfactual explanations help discover discrimination?
7. What challenges arise when dealing with continuous protected attributes?
8. How does the COMPAS case study illustrate different types of algorithmic bias?

---

*Course: INFO-H420 - Management of Data Science and Business Workflows*  
*Instructor: Dimitris Sacharidis*  
*Topic: Part II - Fairness*