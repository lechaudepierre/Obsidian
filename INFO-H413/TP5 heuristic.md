# Heuristic Optimization - Exercise Sheet 5 Solutions

## Exercise 5.1

**Explain why it is possible that for a family of instances of a given combinatorial problem, the number of solutions increases exponentially, while the solution density decreases exponentially, as instance size increases.**

According to the slides, solution density is defined as #S₀/#S (number of solutions divided by search space size). As instance size increases:

- The search space size #S typically grows exponentially (e.g., for TSP with n vertices, search space size is (n-1)!/2)
- The number of solutions #S₀ can also increase exponentially, but at a potentially slower rate than the search space
- Even if both increase exponentially, if the search space grows faster than the number of solutions, the solution density (#S₀/#S) will decrease exponentially

This is mathematically possible because if #S₀ = O(aⁿ) and #S = O(bⁿ) where b > a, then the density = aⁿ/bⁿ = (a/b)ⁿ, which decreases exponentially as n increases.

## Exercise 5.2

**Prove that the neighborhood graph of a SAT instance under the 2-flip neighborhood in which neighboring assignments differ in the truth value of exactly two variables is disconnected. Which conclusion can you draw from this fact?**

For a SAT instance with n variables under 2-flip neighborhood:

**Proof of disconnectedness:**

- Consider two truth assignments that differ in an odd number of variables (e.g., they differ in 1, 3, 5, ... positions)
- To transform one assignment to another using only 2-flip moves (changing exactly 2 variables at a time), we need an even number of variable changes
- Since we can only change variables in steps of 2, we cannot reach an assignment that differs in an odd number of positions
- Therefore, the neighborhood graph has at least two disconnected components: one containing assignments differing by an even number of variables, and another containing assignments differing by an odd number of variables

**Conclusion:** This disconnectedness means that any SLS algorithm using 2-flip neighborhood cannot guarantee to find a solution if one exists, making the algorithm essentially incomplete. The algorithm might get trapped in one component while the solution lies in another disconnected component.

## Exercise 5.3

**Give an example for a landscape that has no local minimum other than the global optimum and is yet very hard to search for any standard SLS method.**

A classic example is a **needle-in-haystack landscape**:

- Search space: All bit strings of length n
- Global optimum: One specific bit string (e.g., all zeros: 000...0) with objective value 0
- All other positions: Have objective value equal to their Hamming distance from the global optimum

**Properties:**

- Only one local minimum exists (the global optimum)
- Every other position has at least one improving neighbor (can get closer to the global optimum)
- Despite having no local minima other than the global optimum, this landscape is extremely hard to search because:
    - The gradient information is very weak (many positions have the same objective value)
    - The probability of randomly finding the global optimum is exponentially small (1/2ⁿ)
    - Local search provides minimal guidance until very close to the optimum

## Exercise 5.4

**You are asked to design a new metaheuristic algorithm for a new optimization problem. You know that usually the problem instances show a high fitness-distance correlation. What implications does this knowledge have on the design of iterated local search algorithms?**

Based on the slides, high fitness-distance correlation (FDC) implies a "big valley" structure. This has several design implications for ILS:

**Intensification Strategy:**

- Use strong intensification mechanisms since high-quality solutions provide good guidance
- Employ effective local search procedures as they will be guided toward better solutions
- Consider greedy initialization rather than random initialization

**Perturbation Design:**

- Use relatively weak perturbations since the landscape provides good guidance
- Strong perturbations that move far from good solutions are counterproductive
- Perturbation should be just strong enough to escape local optima without destroying solution quality completely

**Acceptance Criterion:**

- Can use more aggressive acceptance criteria that favor improvement
- The landscape structure supports focusing search around high-quality regions

**Search Strategy:**

- Emphasize exploitation over exploration
- Multiple restarts are less necessary due to the guiding landscape structure

## Exercise 5.5

**Two possible approaches to the automatic configuration and tuning of metaheuristic algorithms are offline and online tuning approaches. Discuss the advantages and disadvantages of each one of these approaches.**

Based on the slides:

**Offline Configuration:** _Advantages:_

- Configure algorithm before deployment on training instances
- Can use extensive computational resources for configuration
- Results in stable, well-tuned algorithms for deployment
- Can use sophisticated configuration methods (irace, SMAC, ParamILS)

_Disadvantages:_

- Requires representative training instances
- May not adapt to changing problem characteristics
- Configuration effort must be invested upfront
- Performance depends on quality of training set

**Online Parameter Control:** _Advantages:_

- Adapts parameter settings while solving specific instances
- Can react to problem characteristics discovered during search
- No need for separate training phase
- Can handle dynamic or varying problem characteristics

_Disadvantages:_

- Limited to known crucial parameters
- Less computational resources available for parameter adaptation during solving
- Risk of poor performance during adaptation phase
- More complex to implement and debug

**Application Scenarios:**

- **Offline:** When problem characteristics are well-understood and stable; when extensive training time is available; for production environments requiring predictable performance
- **Online:** For highly variable problem instances; when training data is not available; for dynamic optimization problems

## Exercise 5.6

**Discuss possible ways of combining offline and online tuning approaches.**

Several combination strategies are possible:

**Hierarchical Approach:**

- Use offline configuration to set the main algorithmic structure and most parameters
- Use online control for a small subset of critical parameters that need dynamic adaptation

**Offline Configuration of Online Strategies:**

- Use offline techniques to configure the online parameter control strategies themselves
- Configure parameters like adaptation rates, triggers for parameter changes, etc.

**Multi-level Configuration:**

- Offline: Configure algorithm components and their parameter ranges
- Online: Fine-tune within the pre-configured ranges based on problem characteristics

**Adaptive Switching:**

- Use offline configuration to determine when to switch between different online strategies
- Configure portfolio approaches where different strategies are activated based on search progress

**Problem-specific Adaptation:**

- Use offline analysis to identify problem features that should trigger specific online adaptations
- Pre-configure different parameter settings for different detected problem types

## Exercise 5.7

**Automatic algorithm configuration techniques can be useful to support comparative, empirical studies of metaheuristic algorithms to avoid uneven tuning of algorithms. How would you take into account the fact that the number of parameters for algorithms can differ very much?**

**Approaches for Fair Comparison:**

**Equal Configuration Effort:**

- Spend approximately the same computational budget for tuning each algorithm
- Use same configuration time/evaluations for all algorithms regardless of parameter count
- Document and report the configuration effort invested

**Proportional Configuration Budget:**

- Allocate configuration budget proportional to algorithm complexity
- More parameters → more configuration time, but with diminishing returns

**Standardized Configuration Protocol:**

- Use the same automatic configuration tool (e.g., irace) for all algorithms
- Apply same termination criteria and statistical methods
- Use identical training sets and evaluation procedures

**Parameter Complexity Reporting:**

- Document the number and types of parameters for each algorithm
- Report parameter sensitivity analysis results
- Include robustness analysis for parameter variations

**Multi-stage Comparison:**

- First stage: Compare algorithms with default parameters
- Second stage: Compare with basic tuning (main parameters only)
- Third stage: Compare with full automatic configuration

**Handling Different Parameter Counts:**

- Ensure no algorithm is penalized for having fewer parameters
- Consider parameter-free or self-adaptive variants when available
- Report both peak performance and robustness measures

## Exercise 5.8

**Assume that a bi-objective problem is tackled by a weighted sum aggregation. Show that the obtained trade-off points lie on the convex hull of the Pareto-front in case an optimal solution for each weight vector is found.**

**Proof:**

For a bi-objective minimization problem with objectives f₁(x) and f₂(x), the weighted sum is: f_λ(x) = λf₁(x) + (1-λ)f₂(x), where λ ∈ [0,1]

**Key insight:** The weighted sum represents a linear scalarization, which geometrically corresponds to finding points where iso-cost lines (lines of constant weighted sum value) are tangent to the feasible region in objective space.

**Proof steps:**

1. Any optimal solution to the weighted sum problem minimizes the linear combination λf₁ + (1-λ)f₂
2. Geometrically, this corresponds to finding the point in the feasible region that has the smallest value when projected onto the direction vector (λ, 1-λ)
3. Such points must lie on the boundary of the feasible region in objective space
4. Due to the linear nature of the aggregation, only points on the convex hull can be optimal for some weight vector
5. Points in "concave regions" (not on convex hull) cannot be optimal for any positive weight combination, as there exist other points that dominate them in the weighted sum

**Conclusion:** The weighted sum method can only find Pareto-optimal solutions that lie on the convex hull of the Pareto front. Solutions in non-convex regions cannot be found using this approach.

## Exercise 5.9

**Assume that you use iterative improvement algorithms for each scalarization in the SAC search model and a multi-objective iterative improvement algorithm in the CWAC search model. What do you expect how properties of the final Pareto front affect the computation times of these algorithms?**

**SAC (Scalarized Acceptance Criterion) Model:**

- Uses multiple scalarizations, each solved by iterative improvement
- Computation time depends on:
    - **Number of solutions in Pareto front:** More solutions → may need more scalarizations → longer time
    - **Spread of solutions:** Wide spread → need more diverse weight vectors → more scalarizations
    - **Local optima landscape:** Each scalarization creates a single-objective landscape with its own local optima structure

**CWAC (Component-Wise Acceptance Criterion) Model:**

- Explores neighbors using component-wise comparison
- Computation time depends on:
    - **Density of Pareto front:** Dense front → more non-dominated neighbors → larger archives → longer filtering time
    - **Number of solutions:** Directly affects archive size and filtering computational cost
    - **Spread:** Affects exploration effort and archive management complexity

**Comparative expectations:**

- **Dense Pareto fronts:** CWAC may be slower due to large archive management overhead
- **Sparse Pareto fronts:** SAC may be slower if many scalarizations are needed to cover the sparse solutions
- **Highly spread fronts:** SAC needs many weight vectors, potentially slower
- **Poorly connected Pareto fronts:** CWAC may struggle to move between disconnected regions

## Exercise 5.10

**One may measure the correlation between objective functions in multi-objective problems by sampling solutions and measuring correlation between objective vectors. What impact do you expect from different observed correlations on properties of the Pareto front?**

For bi-objective minimization problems:

**High Positive Correlation (objectives move together):**

- **Pareto front properties:**
    - Few solutions on the Pareto front
    - Limited trade-off opportunities
    - Front tends to be short and concentrated
    - Solutions cluster in one region of objective space
- **Reason:** When objectives are positively correlated, improving one objective also tends to improve the other, reducing the trade-off space

**High Negative Correlation (objectives conflict strongly):**

- **Pareto front properties:**
    - Many solutions on the Pareto front
    - Wide spread across objective space
    - Strong trade-off opportunities
    - Front spans large regions of objective space
- **Reason:** Strong conflict between objectives creates rich trade-off opportunities

**Zero Correlation (objectives independent):**

- **Pareto front properties:**
    - Moderate number of solutions
    - Intermediate spread
    - Trade-off opportunities exist but are not extreme
    - Front shape depends on problem structure rather than objective interaction

**Implications for algorithm design:**

- **Positive correlation:** Focus on convergence rather than diversity
- **Negative correlation:** Emphasize diversity maintenance and wide exploration
- **Zero correlation:** Balanced approach between convergence and diversity

## Exercise 5.11

**Consider how unary performance measures for evaluating the performance of multi-objective optimizers could be used for the same purpose in dynamic optimization problems.**

**Adaptation of Multi-objective Measures to Dynamic Problems:**

**Hypervolume-based Measures:**

- **Dynamic Hypervolume:** Measure hypervolume of solution set over time
- Track how hypervolume changes after each environmental change
- Measure convergence speed: time to recover hypervolume after change
- Assess stability: variance in hypervolume across different periods

**Time-integrated Performance:**

- **Area Under Curve (AUC):** Integrate solution quality over entire run time
- **Average hypervolume over time:** Provides single measure combining quality and convergence speed
- **Weighted time periods:** Give more importance to recent performance

**Change-specific Measures:**

- **Recovery time:** Time needed to reach certain hypervolume threshold after change
- **Severity of performance drop:** How much performance degrades immediately after change
- **Adaptation efficiency:** Rate of performance improvement after change detection

**Stability Measures:**

- **Performance variance:** Measure consistency of hypervolume across time periods
- **Robustness:** Ability to maintain good performance despite changes

**Combined Measures:**

- Create composite measures that weight both absolute performance and dynamic adaptation capabilities
- Use multi-criteria evaluation where convergence speed and solution quality are both considered

## Exercise 5.12

**Consider how you could adapt the SLS methods that we have seen in the lectures towards solving dynamic optimization problems.**

Based on the slides' discussion of dynamic optimization problems:

**General Adaptation Strategies:**

**1. Memory-based Approaches:**

- **Explicit Memory:** Store good solutions or solution components from previous environments
- **For ILS:** Maintain archive of good solutions from different time periods
- **For ACO:** Use separate pheromone matrices for different detected environments
- **For Population-based methods:** Maintain multiple sub-populations adapted to different environments

**2. Diversity Enhancement:**

- **Random Immigrants:** Regularly inject random solutions to increase diversity
- **For Evolutionary Algorithms:** Replace worst individuals with random ones periodically
- **For ACO:** Add exploration mechanisms to counter premature convergence
- **Increased mutation rates** when changes are detected

**3. Parameter Adaptation:**

- **Dynamic Parameter Control:** Adjust parameters based on detected changes
- **Increase exploration** when changes detected, then gradually shift to exploitation
- **For SA:** Reset or increase temperature after changes
- **For Tabu Search:** Modify tabu tenure and aspiration criteria

**4. Change Detection and Response:**

- **Monitor solution quality** to detect environmental changes
- **Re-evaluation strategies:** Periodically re-evaluate solutions
- **Trigger mechanisms:** Activate specific responses when changes detected

**5. Specific Algorithm Adaptations:**

**Iterated Local Search:**

- Maintain archive of local optima from different periods
- Use stronger perturbations after change detection
- Implement adaptive perturbation strategies

**Ant Colony Optimization:**

- Use multiple pheromone layers for different environments
- Implement pheromone resetting or modification strategies
- Use environment-specific heuristic information

**Evolutionary Algorithms:**

- Use multi-population approaches
- Implement triggered restart mechanisms
- Use adaptive mutation and crossover rates

## Exercise 5.13

**Consider how you could adapt the SLS methods that we have seen in the lectures towards solving stochastic optimization problems.**

Based on the slides' discussion of stochastic optimization:

**Key Challenge:** Objective function evaluation requires computing expected values E[f(x,ω)]

**General Adaptation Strategies:**

**1. Sampling-based Evaluation:**

- **Monte Carlo sampling:** Use multiple samples to estimate expected objective value
- **Adaptive sampling:** Use more samples for promising solutions
- **Noise handling:** Implement statistical methods to handle evaluation noise

**2. Evaluation Budget Management:**

- **Variable sample sizes:** Use few samples for initial evaluation, more for promising solutions
- **Budget allocation:** Distribute limited evaluations efficiently across solutions
- **Sequential testing:** Use statistical tests to compare solutions

**3. Algorithm-Specific Adaptations:**

**Iterative Improvement:**

- **Statistical comparison:** Use statistical tests (t-test, Wilcoxon) to determine if improvements are significant
- **Confidence intervals:** Accept moves only if improvement confidence exceeds threshold
- **Robust local search:** Use multiple evaluations for each neighbor

**Simulated Annealing:**

- **Robust acceptance criterion:** Modify Metropolis criterion to account for evaluation noise
- **Statistical comparison in acceptance:** Compare solution distributions rather than point values
- **Adaptive sampling:** Use more samples at lower temperatures

**Tabu Search:**

- **Robust move evaluation:** Use statistical significance for move selection
- **Memory adaptation:** Modify tabu tenure based on evaluation confidence
- **Aspiration criteria adjustment:** Account for evaluation uncertainty

**Ant Colony Optimization:**

- **Robust pheromone updates:** Weight updates by solution quality confidence
- **Multiple evaluations:** Evaluate solutions multiple times before pheromone update
- **Stochastic construction:** Account for evaluation noise in construction decisions

**Population-based Methods:**

- **Statistical selection:** Use statistical tests for parent selection and survival
- **Robust ranking:** Rank individuals based on statistical confidence
- **Adaptive evaluation:** Use different sample sizes for different individuals

**4. Specific Techniques:**

**Sample Size Control:**

- Start with small samples, increase for promising solutions
- Use Optimal Computing Budget Allocation (OCBA) methods
- Implement sequential sampling strategies

**Statistical Enhancement:**

- Use variance reduction techniques
- Implement common random numbers for fair comparison
- Apply control variates when possible

**Multi-stage Approaches:**

- **Screening phase:** Use small samples to eliminate poor solutions
- **Refinement phase:** Use large samples for final solution evaluation
- **Adaptive allocation:** Dynamically adjust evaluation effort