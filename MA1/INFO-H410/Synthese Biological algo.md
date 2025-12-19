# Summary: Genetic Algorithms and Ant Colony Optimisation

This document provides an overview of Genetic Algorithms (GAs) and Ant Colony Optimisation (ACO) as methods for solving complex optimisation problems.

## 1. Introduction to Optimisation

Optimisation is the process of finding an extremum (minimum or maximum) value for a function or a problem. These extrema can be:
* **Local:** The best solution within a limited neighborhood.
* **Global:** The absolute best solution across the entire search space.

Methods for optimisation in $R^n$ (real numbers) can be categorized as:
* **With Gradients (Derivatives):** Suitable when the search space is $R^n$ and derivatives can be computed. These methods use gradient information (first degree or higher) to guide the search.
* **Without Derivatives:** These methods explore the neighborhood of a point to find better solutions. Examples include hill climbing and local search. Genetic Algorithms fall into this category.

Global optimisation can often be achieved by running local optimisation methods with different initial conditions.

## 2. Combinatorial Optimisation Problems

Many real-world problems are combinatorial optimisation problems, where the goal is to find the best combination or arrangement from a discrete set of possibilities.
* **Deterministic algorithms** for these problems can be too slow as they explore a vast search space.
* **Metaheuristics** are employed to find satisfactory solutions rapidly.
* Examples include scheduling problems, packing problems, and ordering problems.

A classic example is the **Travelling Salesman Problem (TSP)**:
* Given $N$ cities and the distances between them.
* The goal is to find the shortest possible route that visits each city exactly once and returns to the origin city.
* TSP is an **NP-complete problem**, meaning the time required to find the optimal solution grows exponentially with the number of cities ($N! \sim N^{N+1/2}$). It serves as a common benchmark for optimisation algorithms.

## 3. Genetic Algorithms (GAs)

Genetic Algorithms are a part of **evolutionary computing**, inspired by Darwinian principles of natural selection and evolution.
* **1975:** John Holland introduced Genetic Algorithms.
* **1992:** John Koza introduced Genetic Programming (an extension of GAs).

The core idea is that **evolution itself is a form of optimisation**.

### 3.1. Key Components and Terminology
* **Population:** A set of candidate solutions (individuals).
* **Chromosome:** An individual solution, typically represented as a string of data.
* **Gene:** A part of a chromosome, representing a specific characteristic or parameter of the solution.
* **Allele:** The specific value a gene can take.
* **Fitness:** A measure of how good a solution (chromosome) is. It's determined by a **fitness function** that evaluates the solution against the problem's objective.
* **Reproduction:** The process of creating new individuals (offspring) from existing ones (parents). This involves two main genetic operators:
    * **Crossover (Recombination):** Combines genetic material from two parent chromosomes to create one or more offspring.
    * **Mutation:** Randomly alters one or more genes in a chromosome to introduce new genetic material and maintain diversity.

### 3.2. The Standard Genetic Algorithm
The typical flow of a GA is as follows:

1.  **Initialization:** Generate an initial random population of chromosomes.
2.  **Evaluation:** Calculate the fitness $f(x)$ for each chromosome $x$ in the current population.
3.  **Loop (Repeat until a stopping criterion is met):**
    * **Selection:** Select parent chromosomes from the current population based on their fitness. Fitter individuals have a higher chance of being selected.
    * **Crossover:** Apply the crossover operator to selected pairs of parents with a certain probability (crossover probability) to create offspring.
    * **Mutation:** Apply the mutation operator to the offspring (and sometimes to the entire population) with a certain probability (mutation probability).
    * **Replacement (New Population):** Form a new population by replacing some or all of the old individuals with the newly created offspring (often, the best individuals are kept).
4.  **Termination:** When a stopping criterion is met (e.g., a maximum number of generations, a satisfactory fitness level reached, or no improvement for a certain number of generations), the algorithm stops. The best solution found in the current population is returned.

### 3.3. Chromosome Encoding
The way solutions are represented as chromosomes is crucial and can be influenced by the problem.

* **Binary Encoding:**
    * Chromosomes are strings of bits (0s and 1s). This is the most common encoding.
    * **Example: Knapsack Problem.** Given items with values and sizes, and a knapsack with a given capacity. Select items to maximize total value without exceeding capacity. Each bit can represent whether an item is included (1) or not (0).
        * Chromosome A: `101100101100101011100101`
        * Chromosome B: `111111100000110000011111`

* **Permutation Encoding:**
    * Chromosomes are strings of numbers representing a sequence or order.
    * **Example: Travelling Salesman Problem (TSP).** A chromosome represents the order in which cities are visited.
        * Chromosome A: `1 5 3 2 6 4 7 9 8` (Visit city 1, then 5, then 3, etc.)
        * Chromosome B: `8 5 6 7 2 3 1 4 9`

* **Value Encoding:**
    * Chromosomes are strings of values, which can be numbers (integers, real numbers), characters, or more complex objects.
    * **Example: Finding weights for a neural network.** Real values in the chromosome represent the connection weights.
        * Chromosome A: `1.2324 5.3243 0.4556 2.3293 2.4545`
        * Chromosome B (characters): `ABDJEIFJDHDIERJFDLDFLFEGT`
        * Chromosome C (commands): `(back), (back), (right), (forward), (left)`

* **Tree Encoding:**
    * Chromosomes are represented as trees of objects, like functions or commands. This is used in **Genetic Programming**.
    * **Example: Finding a function from given input/output values.** The tree represents a mathematical expression or a program.
        * Example function: `(+ x (/ 5 y))` which translates to $x + \frac{5}{y}$.
        * Example program: `(do_until step wall)`

### 3.4. Crossover (Recombination)
Crossover combines parts of two parent chromosomes.

* **General Example (Single Point for Binary):**
    * Parent 1 (C1): `1011 | 10001`
    * Parent 2 (C2): `0110 | 11100`
    * (Crossover point after 4th bit)
    * Offspring 1 (D1): `1011 | 11100`
    * Offspring 2 (D2): `0110 | 10001`
    * Many variants exist, including multi-point crossover.

* **Crossover for Binary Encoding:**
    * **Single Point Crossover:** As above.
    * **Two Point Crossover:** Two points are chosen, and the segment between them is swapped.
        * Example: `110|01|011` and `100|11|111` $\rightarrow$ `110|11|011`
    * **Uniform Crossover:** For each gene, it's decided (e.g., by a coin flip) which parent contributes the allele.
        * Example: `11001011` and `10011111` $\rightarrow$ `10001111` (if parent 2 chosen for bits 2, 6, 7)
    * **Difference Operators:** e.g., Bitwise AND: `11001011` AND `10011111` $\rightarrow$ `10001011`

* **Crossover for Permutation Encoding:**
    * **Single Point Crossover (can be problematic, often needs repair):**
        * Parent 1: `(1 2 3 | 4 5 6 7 8 9)`
        * Parent 2: `(4 5 3 | 6 8 9 7 2 1)`
        * Offspring (naive): `(1 2 3 | 6 8 9 7 2 1)` - This often results in invalid permutations (repeated or missing elements). Specialized permutation crossover operators (like PMX, CX, OX) are used. The document gives an example `(1 2 3 4 5 | 9 7 6 8)` which seems to imply a simpler cut and splice but doesn't detail repair.

* **Crossover for Tree Encoding:**
    * Two crossover points (one in each parent tree) are selected, and the subtrees rooted at these points are swapped.
    * Example:
        * Parent 1: `(* (- 2 x) (+ y 1))`
        * Parent 2: `(+ 4 (* x x))`
        * If crossover points are `(+ y 1)` in Parent 1 and `(* x x)` in Parent 2:
        * Offspring 1: `(* (- 2 x) (* x x))`
        * Offspring 2: `(+ 4 (+ y 1))` (The document shows `(+ 4 (* (+y 1) x))` which implies one subtree replaced a leaf node in the other).

### 3.5. Mutation
Mutation introduces random changes to chromosomes.

* **General Example (Binary):**
    * Original (D1): `101111100`
    * Mutated (M1): `100111100` (3rd bit flipped)

* **Mutation for Binary Encoding:**
    * **Bit Inversion:** Flip a randomly selected bit (0 to 1, 1 to 0).
        * Example: `101111100` $\rightarrow$ `111111100` (if 2nd bit is mutated)

* **Mutation for Permutation Encoding:**
    * **Order Changing (Swap Mutation):** Two positions are randomly selected, and their values are swapped.
        * Example: `(1 2 3 4 5 6 8 9 7)` $\rightarrow$ `(1 8 3 4 5 6 2 9 7)` (if positions 2 and 7 are swapped)

* **Mutation for Value Encoding:**
    * Add or subtract a small random number, or replace with a new random value.
        * Example: `(1.29 5.68 2.86 4.11 5.55)` $\rightarrow$ `(1.29 5.68 2.73 4.22 5.55)` (adjusting 3rd and 4th values)

* **Mutation for Tree Encoding:**
    * Change a node (e.g., an operator or a terminal) or replace an entire subtree with a randomly generated one.

### 3.6. Selection Methods
Selection determines which individuals from the current population will be parents for the next generation.

* **Roulette Wheel Selection (Fitness Proportional Selection):**
    * Each individual is assigned a slice of a roulette wheel, with the size of the slice proportional to its fitness.
    * The wheel is "spun" multiple times; individuals whose slices are selected become parents.
    * Higher fitness individuals have a higher probability of being selected.
    * Can lead to premature convergence if one individual is vastly fitter than others.

* **Rank Selection:**
    * Individuals are first sorted (ranked) according to their fitness.
    * Selection probability is then based on rank, not raw fitness. This prevents very fit individuals from dominating too quickly.
    * For example, the best individual gets rank $N$, the worst gets rank 1, and selection probability is proportional to rank.

* **Tournament Selection:**
    * A small group of $k$ individuals is randomly selected from the population.
    * These $k$ individuals "compete," and the one with the best fitness is selected as a parent.
    * This process is repeated to select multiple parents. The size $k$ is the tournament size.

* **Steady-State Selection:**
    * In each generation, only a few individuals (often the worst) are replaced by new offspring. The majority of the population carries over.

### 3.7. Elitism
* A strategy where the best individual (or a few best individuals) from the current population is directly copied to the next generation without undergoing crossover or mutation.
* This ensures that the best solution found so far is never lost.

### 3.8. Parameters
The performance of a GA is sensitive to several parameters:
* **Crossover Probability:** The likelihood that crossover will occur between two selected parents (e.g., 0.6 - 0.9).
* **Mutation Probability:** The likelihood that a gene will be mutated (e.g., 0.001 - 0.05).
* **Population Size:** The number of individuals in the population (e.g., 50 - 500).

## 4. Ant Colony Optimisation (ACO)

ACO is inspired by the foraging behavior of ants, particularly how they find the shortest paths between their nest and food sources.

### 4.1. Biological Inspiration
* Ants deposit **pheromones** on the ground as they travel.
* Other ants are attracted to pheromones and tend to follow paths with stronger pheromone concentrations.
* Shorter paths are traversed more quickly, leading to a faster accumulation of pheromones on them. This positive feedback mechanism helps the colony converge on the shortest path.
* **Adaptivity:** If a path is blocked, ants explore alternatives, and new pheromone trails are established.

**The Double Bridge Experiment (Goss et al., 1989, Deneubourg et al., 1990):**
* Demonstrates this behavior. Ants are presented with two paths of different lengths between their nest and a food source.
* Initially, ants explore both paths roughly equally.
* Ants on the shorter path return sooner and make more trips, reinforcing the pheromone on that path more quickly.
* Over time, most ants converge on using the shorter path.

### 4.2. Key Concepts in Ant Behavior for ACO
* **Navigation:** Initially random, but increasingly guided by pheromone trails (representing accumulated search experience).
* **Recruitment (Communication):** Indirect communication through the environment (stigmergy). Ants don't communicate directly but influence each other's behavior by modifying the environment (depositing pheromones).

### 4.3. Ant System Applied to the TSP
The **Ant System (AS)**, introduced by Dorigo, Maniezzo, and Colorni in 1991, was the first ACO algorithm and was applied to the TSP.
* **Pheromone Trail Depositing:** Artificial ants build solutions (tours) and deposit pheromones on the edges (paths between cities) they traverse.
* **Memory:** The pheromone trails serve as a distributed, numerical memory of the quality of paths found so far.
* **Probabilistic Rule to Choose Path:** Ants probabilistically choose the next city to visit based on pheromone intensity and heuristic information (e.g., distance).

### 4.4. Ant Algorithms in Computer Science
ACO algorithms typically represent the optimisation problem as a graph. Ants (computational agents) iteratively construct solutions.

**Pheromone Update:**
The pheromone level $\tau_{ij}$ on an edge $(i,j)$ is updated at each iteration:
$\tau_{ij}(t+1) = (1-\rho) \cdot \tau_{ij}(t) + \sum_{k=1}^{m} \Delta\tau_{ij}^{k}(t)$
Where:
* $\tau_{ij}(t)$ is the amount of pheromone on edge $(i,j)$ at time $t$.
* $\rho$ is the **pheromone evaporation rate** ($0 < \rho < 1$). This allows the algorithm to "forget" bad choices and explore new paths.
* $m$ is the number of ants.
* $\Delta\tau_{ij}^{k}(t)$ is the amount of pheromone deposited on edge $(i,j)$ by ant $k$ at time $t$. This is typically:
    $\Delta\tau_{ij}^{k}(t) = \begin{cases} 1/L^{k}(t) & \text{if edge (i, j) is used by ant k in its tour} \\ 0 & \text{otherwise} \end{cases}$
    * $L^{k}(t)$ is the length of the tour found by ant $k$. Shorter tours (better solutions) result in more pheromone deposition.

**Probability of Selecting Next Node:**
The probability $p_{ij}^{k}(t)$ that ant $k$ currently at node $i$ chooses to move to node $j$ (where $j \in \mathcal{N}_{i}^{k}$, the set of feasible neighbor nodes) is given by:
$$p_{ij}^{k}(t) = \frac{[\tau_{ij}(t)]^{\alpha} \cdot [\eta_{ij}]^{\beta}}{\sum_{l \in \mathcal{N}_{i}^{k}} [\tau_{il}(t)]^{\alpha} \cdot [\eta_{il}]^{\beta}}$$
Where:
* $\tau_{ij}(t)$ is the pheromone intensity on edge $(i,j)$.
* $\eta_{ij}$ is **heuristic information** (a priori desirability of moving from $i$ to $j$). For TSP, $\eta_{ij} = 1/d_{ij}$ (where $d_{ij}$ is the distance between city $i$ and city $j$).
* $\alpha$ and $\beta$ are parameters that control the relative influence of the pheromone trail versus the heuristic information.

### 4.5. General Ant Algorithm Flow
1.  **Initialization:** Initialize pheromone trails (e.g., to a small constant value).
2.  **Loop (For all iterations):**
    * **For each ant:**
        * **Construct Solution:** Probabilistically choose and perform actions (e.g., select the next node to visit) based on pheromone levels and heuristic information until a complete solution is built.
    * **Update Pheromones:**
        * Apply pheromone evaporation.
        * Ants deposit pheromones on the components (edges) of the solutions they constructed, typically proportional to the quality of their solutions.
3.  **Termination:** Stop when a termination criterion is met (e.g., maximum iterations, solution convergence).

The document includes graphs showing the evolution of the best tour length and trail distribution for an Ant System applied to a TSP instance, illustrating the convergence towards good solutions.

