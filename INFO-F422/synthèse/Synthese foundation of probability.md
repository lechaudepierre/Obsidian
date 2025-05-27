# Statistical Foundations of Machine Learning: Probability Foundations

**Course:** INFO-F-422  
**Instructor:** Gianluca Bontempi  
**Institution:** Machine Learning Group, Computer Science Department

## 1. Introduction: Why Probability and Machine Learning?

Probability theory provides a convenient framework for:

- **Representing variability and uncertainty** in data and models
- **Modeling dependencies** in multivariate settings
- **Representing data as realizations** of random experiments (probabilistic data generating processes)

**Key Insight:** Probability theory is an exact discipline developed from clearly defined axioms, and when applied to real problems, it works effectively.

**Warning:** Probability can be highly counterintuitive - human intuition often fails in probabilistic reasoning.

## 2. Random Experiments and Sample Spaces

### Random Experiment Framework

- **Variable**: Any property or descriptor (categorical or numerical) that can take multiple values
- **Random Experiment**: A compact way of modeling the disparate causes that lead to variability
- **Sample Space (Ω)**: The set of all possible outcomes
- **Outcome (ω)**: A single result of the random experiment

### Weather Model Example

Three categorical random variables:

1. **Sky condition**: {CLEAR, CLOUDY}
2. **Barometer trend**: {RISING, FALLING}
3. **Humidity**: {DRY, WET}

**Sample Space**: 8 possible outcomes (2×2×2 = 8 combinations)

## 3. Events and Set Theory

### Event Definition

- **Event**: A subset of experimental outcomes
- Any declarative statement using variables constitutes an event
- Events can be combined using set theory operations

### Set Theory Operations

- **Complement**: E^c = {ω ∈ Ω : ω ∉ E}
- **Union**: E₁ ∪ E₂ = {ω ∈ Ω : ω ∈ E₁ OR ω ∈ E₂}
- **Intersection**: E₁ ∩ E₂ = {ω ∈ Ω : ω ∈ E₁ AND ω ∈ E₂}
- **Mutually Exclusive**: E₁ ∩ E₂ = ∅
- **Partition**: Disjoint sets that cover the entire sample space

## 4. Axiomatic Definition of Probability

### Probability Axioms

Any function Prob{·} is a probability distribution if it satisfies:

1. **Non-negativity**: Prob{E} ≥ 0 for any event E
2. **Normalization**: Prob{Ω} = 1
3. **Additivity**: Prob{E₁ ∪ E₂} = Prob{E₁} + Prob{E₂} - Prob{E₁ ∩ E₂}

### Key Properties

- For mutually exclusive events: Prob{E₁ ∪ E₂} = Prob{E₁} + Prob{E₂}
- Prob{E} = Σ_{ω∈E} Prob{ω}

## 5. Independence

### Independent Events

Two events E₁ and E₂ are independent if: **Prob{E₁ ∩ E₂} = Prob{E₁} × Prob{E₂}**

Notation: E₁ ⊥⊥ E₂

### Key Results About Independence

- **Mutually exclusive events** (with non-zero probability) are **NOT independent**
- **Identical events** (with probability < 1) are **NOT independent**
- If E₁ and E₂ are independent, then their **complements are also independent**

### Independent Variables

Variables x and y are independent if: **Prob{x = x, y = y} = Prob{x = x} × Prob{y = y}** for all values x, y

**Important**: Independence means observing one variable does NOT affect the probability of the other.

## 6. Conditional Probability

### Definition

If Prob{E₁} > 0, then: **Prob{E₂|E₁} = Prob{E₁, E₂} / Prob{E₁}**

### Key Properties

- **Conditional probabilities are probabilities**: They satisfy all probability axioms
- **All probabilities are conditional**: Every probability statement has implicit conditioning
- **Non-monotonic**: Adding conditioning information doesn't necessarily increase or decrease probability

### Important Warning

**Direct ≠ Inverse conditional probability**

- Generally: Prob{E₂|E₁} ≠ Prob{E₁|E₂}
- Example: P(Italian|Football supporter) ≠ P(Football supporter|Italian)

## 7. Law of Total Probability

For mutually exclusive and exhaustive events E₁, E₂, ..., Eₖ:

**Prob{E} = Σᵢ Prob{E|Eᵢ} × Prob{Eᵢ}**

This decomposes marginal probability into weighted conditional probabilities.

## 8. Bayes' Theorem

For mutually exclusive and exhaustive events E₁, E₂, ..., Eₖ:

**Prob{Eᵢ|E} = [Prob{E|Eᵢ} × Prob{Eᵢ}] / Prob{E}**

**Expanded form:** **Prob{Eᵢ|E} = [Prob{E|Eᵢ} × Prob{Eᵢ}] / [Σⱼ Prob{E|Eⱼ} × Prob{Eⱼ}]**

Bayes' theorem allows us to compute "inverse" conditional probabilities.

## 9. Chain Rule

For any sequence of events E₁, E₂, ..., Eₙ:

**Prob{E₁, E₂, ..., Eₙ} = Prob{E₁} × Prob{E₂|E₁} × Prob{E₃|E₁,E₂} × ... × Prob{Eₙ|E₁,...,Eₙ₋₁}**

## 10. Conditional Independence

### Definition

Variables x and y are conditionally independent given z (x ⊥⊥ y | z) if: **P(x,y|z) = P(x|z) × P(y|z)** for all values

### Key Insights

- **Independence is not stable**: Independent variables can become dependent when conditioning on a third variable
- **Dependent variables can become independent** when conditioning appropriately
- **Conditional independence ≠ Independence**: These are different concepts

## 11. Random Numerical Variables

### Key Concepts

- **Random Variable**: A mapping from outcomes to real numbers
- **Probability Distribution**: Complete description of probabilistic behavior
- **Notation**: Bold for random variables (z), normal for values (z = 11)

### Discrete Random Variables

#### Probability Mass Function

For discrete r.v. z with range Z:

- **P_z(z) = Prob{z = z}**
- **Properties**: P_z(z) ≥ 0 and Σ_{z∈Z} P_z(z) = 1

#### Expected Value (Mean)

**E[z] = μ = Σ_{z∈Z} z × P_z(z)**

Key points:

- Weighted average of possible values
- Not necessarily the "most probable value"
- May not belong to the range Z

#### Variance

**Var[z] = σ² = E[(z - E[z])²] = E[z²] - (E[z])²**

Measures dispersion around the mean.

#### Other Moments

- **r-th moment**: μᵣ = E[zʳ]
- **Standard deviation**: σ = √Var[z]
- **Skewness**: γ = E[(z-μ)³]/σ³

### Continuous Random Variables

#### Distribution and Density Functions

- **Cumulative Distribution Function**: F_z(z) = Prob{z ≤ z}
- **Density Function**: p_z(z) = dF_z(z)/dz

#### Key Properties

- Individual values have probability zero
- **Prob{a ≤ z ≤ b} = ∫ᵃᵇ p_z(z)dz**
- **∫ p_z(z)dz = 1**

#### Moments for Continuous Variables

- **Mean**: μ = ∫ z p(z)dz
- **Variance**: σ² = ∫ (z-μ)² p(z)dz
- **r-th moment**: μᵣ = ∫ zʳ p(z)dz

## 12. Important Distributions

### Uniform Distribution U(a,b)

**p(z) = 1/(b-a)** for a ≤ z ≤ b, 0 otherwise

- **Mean**: (a+b)/2
- **Variance**: (b-a)²/12

### Normal Distribution N(μ,σ²)

**p(x) = (1/√(2πσ)) × exp(-(x-μ)²/(2σ²))**

#### Key Properties

- **Mean**: μ
- **Variance**: σ²
- **68-95-99.7 Rule**:
    - 68% within μ ± σ
    - 95% within μ ± 2σ
    - 99.7% within μ ± 3σ

#### Standard Normal N(0,1)

If z ~ N(0,1), then x = μ + σz ~ N(μ,σ²)

#### Linear Combinations

If x₁ ~ N(μ₁,σ₁²) and x₂ ~ N(μ₂,σ₂²) are independent: **x₁ + x₂ ~ N(μ₁ + μ₂, σ₁² + σ₂²)**

## 13. Transformation of Random Variables

### Law of the Unconscious Statistician (LOTUS)

For y = g(z):

- **E[y|z] = g(z)** (since g is deterministic)
- **E[y] = E[g(z)] = ∫ g(z) p_z(z)dz**

**Important**: Generally E[g(z)] ≠ g(E[z])

## 14. Monte Carlo Simulation

### Method

1. Generate S samples zᵢ ~ F_z
2. Compute g(zᵢ) for each sample
3. Estimate: **E[g(z)] ≈ (Σᵢ g(zᵢ))/S**

Useful when analytical computation is too complex.

## 15. Multivariate Distributions

### Joint, Marginal, and Conditional Relationships

#### From Joint to Marginal

**Prob{x = x} = Σ_y Prob{x = x, y = y}** (discrete) **p_x(x) = ∫ p_{x,y}(x,y) dy** (continuous)

#### From Joint to Conditional

**Prob{y = y|x = x} = Prob{x = x, y = y}/Prob{x = x}** (discrete) **p(y|x) = p_{x,y}(x,y)/p_x(x)** (continuous)

### Linear Combinations and Covariance

#### Expectation (Linear)

**E[ax + by] = aE[x] + bE[y]**

#### Variance (Not Linear)

**Var[ax + by] = a²Var[x] + b²Var[y] + 2ab×Cov[x,y]**

#### Covariance

**Cov[x,y] = E[(x-E[x])(y-E[y])] = E[xy] - E[x]E[y]**

#### Correlation Coefficient

**ρ(x,y) = Cov[x,y]/√(Var[x]×Var[y])**

Properties: -1 ≤ ρ(x,y) ≤ 1

### Independence vs. Correlation

- **Independence ⟹ Uncorrelation** (always true)
- **Correlation ⟹ Dependence** (always true)
- **Dependence ⟹ Correlation** (FALSE - nonlinear relationships)
- **Uncorrelation ⟹ Independence** (FALSE - except for jointly Gaussian)

## 16. Multivariate Normal Distribution

### Definition

**z ~ N(μ, Σ)** where:

- **μ**: n×1 mean vector
- **Σ**: n×n covariance matrix (symmetric, positive definite)

**p(z) = 1/((2π)^(n/2)√det(Σ)) × exp(-½(z-μ)ᵀΣ⁻¹(z-μ))**

### Properties

- **Mahalanobis Distance**: Δ = (z-μ)ᵀΣ⁻¹(z-μ)
- **Contours**: Constant probability surfaces are hyperellipsoids
- **Principal Axes**: Given by eigenvectors of Σ
- **Eigenvalues**: Give variances along principal directions

### Bivariate Normal

For z = [z₁, z₂]ᵀ with correlation ρ:

**p(z₁,z₂) = 1/(2πσ₁σ₂√(1-ρ²)) × exp(-1/(2(1-ρ²)) × [(z₁-μ₁)²/σ₁² - 2ρ(z₁-μ₁)(z₂-μ₂)/(σ₁σ₂) + (z₂-μ₂)²/σ₂²])**

### Conditional Distributions

In multivariate normal, all conditionals and marginals are normal.

For bivariate case:

- **μ₂|₁ = μ₂ + ρ(σ₂/σ₁)(z₁ - μ₁)**
- **σ²₂|₁ = σ₂²(1 - ρ²)**

## Key Takeaways

1. **Probability is fundamental** to machine learning for handling uncertainty
2. **Independence and conditional independence** are crucial concepts that behave differently
3. **Bayes' theorem** enables inverse probability calculations
4. **Normal distributions** are central to many ML algorithms
5. **Correlation ≠ Independence** - understanding the difference is critical
6. **Multivariate distributions** extend univariate concepts to multiple variables
7. **Monte Carlo methods** provide numerical solutions when analytical approaches fail

## Important Warnings and Common Mistakes

- Don't confuse direct and inverse conditional probabilities
- Independence doesn't imply conditional independence and vice versa
- Expected value of a function ≠ function of expected value
- Correlation only captures linear relationships
- Human intuition often fails in probability - rely on mathematical principles