# Resume: Optimality, Relaxation, and Bounds in Integer Programming

This document summarizes key concepts regarding optimality, relaxation, and bounds in the context of Integer Programming (IP) and Combinatorial Optimization Problems (COP), drawing from "02. Optimality, relaxation and bounds.pdf" and enriched by "Integer Programming" by Laurence A. Wolsey (Chapter 2, pp. 25-38).

## 1. Optimality and Relaxation

### 1.1. Proving Optimality
For an IP or COP, typically a maximization problem $z = \max\{c(x) : x \in X \subseteq \mathbb{Z}^n\}$, proving a given point $x^*$ is optimal involves finding lower and upper bounds such that they converge.
-   **Lower bound ($\underline{z}$)**: $\underline{z} \le z$
-   **Upper bound ($\overline{z}$)**: $\overline{z} \ge z$
Optimality is proven when $\underline{z} = z = \overline{z}$. Algorithms typically generate sequences:
-   Decreasing upper bounds: $\overline{z}_1 > \overline{z}_2 > \dots > \overline{z}_s \ge z$
-   Increasing lower bounds: $\underline{z}_1 < \underline{z}_2 < \dots < \underline{z}_t \le z$
A common **stopping criterion** for an algorithm is when the gap between the best upper bound and the best lower bound is sufficiently small: $\overline{z}_s - \underline{z}_t < \epsilon$, for a small $\epsilon > 0$.

### 1.2. Primal Bounds (Lower Bounds for Maximization)
-   Any feasible solution $x^* \in X$ provides a lower bound $\underline{z} = c(x^*)$ for a maximization problem (or an upper bound for minimization).
-   Finding feasible solutions can range from trivial to as difficult as solving the IP itself. These are often found using **heuristic algorithms**.

### 1.3. Dual Bounds (Upper Bounds for Maximization)
-   Finding upper bounds (or lower bounds for minimization) is often more challenging. These are termed **dual bounds**.
-   The primary method for obtaining dual bounds is through **relaxation**.
-   **Definition of Relaxation (Wolsey, p. 26; 02.pdf, p.2)**: A problem (RP) $z^{RP} = \max\{f(x) : x \in T \subseteq \mathbb{R}^n\}$ is a relaxation of (IP) $z = \max\{c(x) : x \in X \subseteq \mathbb{R}^n\}$ if:
    1.  $X \subseteq T$ (the feasible region of RP contains that of IP).
    2.  $f(x) \ge c(x)$ for all $x \in X$ (the objective function of RP is an upper bound to that of IP over the feasible region of IP).
-   **Proposition (Wolsey, p. 27; 02.pdf, p.2)**: If RP is a relaxation of IP, then $z^{RP} \ge z$.
    * *Proof*: If $x^*$ is an optimal solution of IP, then $x^* \in X \subseteq T$. Thus, $z = c(x^*) \le f(x^*)$. Since $x^* \in T$, $f(x^*)$ is a feasible solution value for RP, so $f(x^*) \le z^{RP}$. Therefore, $z \le z^{RP}$.

## 2. Linear Programming Relaxations

-   **Definition (Wolsey, p. 27; 02.pdf, p.2)**: For an integer program $z = \max\{c^Tx : x \in P \cap \mathbb{Z}^n\}$ with $P = \{x \in \mathbb{R}_+^n : Ax \le b\}$, the **Linear Programming (LP) relaxation** is $z^{LP} = \max\{c^Tx : x \in P\}$. This is a relaxation because $P \cap \mathbb{Z}^n \subseteq P$ and the objective function is identical.
-   **Better Formulations (Wolsey, p. 27; 02.pdf, p.2)**: If $P_1$ and $P_2$ are two formulations for $X \subseteq \mathbb{Z}^n$ and $P_1 \subset P_2$ ($P_1$ is a better or tighter formulation), then $z_1^{LP} \le z_2^{LP}$ for all $c$. Tighter formulations yield better (smaller for maximization) LP relaxation values.
-   **Properties of Relaxations (Wolsey, p. 28; 02.pdf, p.2)**:
    1.  If a relaxation RP is infeasible, then IP is infeasible.
    2.  If $x^*$ is an optimal solution to RP, and if $x^* \in X$ (i.e., $x^*$ is feasible for IP, e.g., integral for an IP) and $f(x^*) = c(x^*)$ (e.g., objectives are the same), then $x^*$ is an optimal solution to IP.
        * Often, if an LP relaxation yields an integral solution, that solution is optimal for the IP.

## 3. Combinatorial Relaxations

These relaxations replace the original problem with a simpler combinatorial problem whose feasible set is a superset of the original or whose objective is an upper bound.

### 3.1. The Traveling Salesman Problem (TSP)
-   **Assignment Problem Relaxation (Wolsey, p. 28; 02.pdf, p.2)**:
    $z^{TSP} = \min \{\sum c_{ij} : \text{T forms a tour}\} \ge z^{ASS} = \min \{\sum c_{ij} : \text{T forms an assignment}\}$.
    The assignment problem relaxes the subtour elimination constraints.

### 3.2. The Symmetric Traveling Salesman Problem (STSP)
-   **1-Tree Relaxation (Wolsey, p. 29; 02.pdf, p.3)**: A 1-tree consists of two edges incident to node 1, plus a tree on nodes $\{2, \dots, n\}$. Every tour is a 1-tree.
    $z^{STSP} \ge z^{1-tree}$.
    This relaxation is useful because minimum spanning trees (a component of 1-trees) are easy to find.

### 3.3. The Quadratic 0-1 Problem
-   Problem: $\max\{\sum_{i<j} q_{ij}x_i x_j - \sum_j p_j x_j : x \in \{0,1\}^n, x \ne 0\}$.
-   **Relaxation (Wolsey, p. 29; 02.pdf, p.3)**: Replace $q_{ij}x_i x_j$ with $0$ if $q_{ij} < 0$.
    $z^R = \max\{\sum_{i<j} \max(q_{ij}, 0)x_i x_j - \sum_j p_j x_j : x \in \{0,1\}^n, x \ne 0\}$. This can be solved as a series of maximum flow problems.

### 3.4. The Knapsack Problem
-   For an integer knapsack set $X = \{x \in \mathbb{Z}_+^n : \sum a_j x_j \le b\}$.
-   **Relaxation (Wolsey, p. 29; 02.pdf, p.3)**: $X' = \{x \in \mathbb{Z}_+^n : \sum \lfloor a_j \rfloor x_j \le \lfloor b \rfloor\}$.
    If the objective is $\max \sum c_j x_j$ with $c_j \ge 0$, then $\max_{x \in X} \sum c_j x_j \le \max_{x \in X'} \sum c_j x_j$ is not guaranteed if some $a_j$ are not integers. The relaxation is about the feasible set. If $a_j$ are real, this makes the coefficients integer.

## 4. Lagrangian Relaxation

This technique involves moving "difficult" constraints into the objective function with associated Lagrange multipliers (dual variables).
-   Given $z = \max\{c^Tx : Ax \le b, x \in X \subseteq \mathbb{Z}^n\}$.
-   **Lagrangian Subproblem (Wolsey, p. 30; 02.pdf, p.3)**: For $u \ge 0$, $z(u) = \max\{c^Tx + u^T(b - Ax) : x \in X\}$.
-   **Property (Wolsey, p. 30; 02.pdf, p.4)**: $z(u) \ge z$ for all $u \ge 0$.
    * *Proof*: Let $x^*$ be optimal for IP. Then $x^* \in X$ and $Ax^* \le b$. So $b - Ax^* \ge 0$. Since $u \ge 0$, $u^T(b - Ax^*) \ge 0$. Thus, $z = c^Tx^* \le c^Tx^* + u^T(b - Ax^*) \le \max_{x \in X}\{c^Tx + u^T(b - Ax)\} = z(u)$.
-   The best upper bound is found by solving the **Lagrangian Dual problem**: $w_{LD} = \min_{u \ge 0} z(u)$.

## 5. Duality

### 5.1. Weak and Strong Duality
-   **Definition (Wolsey, p. 30; 02.pdf, p.4)**:
    Two problems, (IP) $z = \max\{c(x) : x \in X\}$ and (D) $w = \min\{\omega(u) : u \in U\}$, form a **weak dual pair** if $c(x) \le \omega(u)$ for all $x \in X, u \in U$.
    They form a **strong dual pair** if $z = w$.
-   Any feasible solution to a dual problem D provides an upper bound for IP (if IP is max and D is min). This is an advantage over relaxation, as a relaxation must be solved to optimality to yield a bound.
-   **Proposition (Wolsey, p. 31; 02.pdf, p.4)**:
    1.  If D is unbounded (e.g., $w \to -\infty$ for min), IP is infeasible.
    2.  If $x^* \in X$ and $u^* \in U$ satisfy $c(x^*) = \omega(u^*)$ (no duality gap), then $x^*$ is optimal for IP and $u^*$ is optimal for D.

### 5.2. LP Duality as Weak Duality for IP
-   **Proposition (Wolsey, p. 31; 02.pdf, p.4)**: The IP $z = \max\{c^Tx : Ax \le b, x \in \mathbb{Z}_+^n\}$ and the LP dual $w^{LP} = \min\{u^Tb : u^TA \ge c^T, u \in \mathbb{R}_+^m\}$ form a weak dual pair. This is because $z \le z^{LP} = w^{LP}$.

### 5.3. Combinatorial Duals: Matching Example
-   **Maximum Cardinality Matching (Wolsey, p. 31; 02.pdf, p.4)**:
    (IP) $z = \max\{|M| : M \text{ is a matching in graph } G=(V,E)\}$.
-   **Minimum Cardinality Node Cover (Wolsey, p. 31; 02.pdf, p.4)**:
    (D) $w = \min\{|R| : R \text{ is a node cover in } G=(V,E)\}$.
-   These form a weak dual pair: $|M| \le |R|$.
    * *Proof*: Each edge in M must be covered by at least one node in R. Since edges in M are disjoint, R must contain at least one endpoint from each of the $|M|$ edges in M, and these endpoints must be distinct for distinct edges in M.
-   **LP Formulation & Duality (Wolsey, p. 32; 02.pdf, p.5)**:
    Let $A$ be the node-edge incidence matrix.
    Matching IP: $z = \max\{\mathbf{1}^Tx : Ax \le \mathbf{1}, x \in \mathbb{Z}_+^m\}$.
    Covering IP: $w = \min\{\mathbf{1}^Ty : y^TA \ge \mathbf{1}, y \in \mathbb{Z}_+^n\}$.
    Their LP relaxations $z^{LP}$ and $w^{LP}$ satisfy $z \le z^{LP} = w^{LP} \le w$.
-   **Strong duality ($z=w$) holds if G is bipartite (Konig's Theorem), but not for general graphs.** For a triangle graph, $z=1, w=2$, and $z^{LP}=w^{LP}=3/2$.

### 5.4. Superadditive Duality (Wolsey, p. 32)
A more general duality for IPs.
-   **Definition**: A function $F: \mathbb{R}^m \to \mathbb{R}$ is superadditive if $F(0)=0$ and $F(u) + F(v) \le F(u+v)$. It's nondecreasing if $u \le v \implies F(u) \le F(v)$.
-   **Theorem**: For $z = \max\{c^Tx : Ax \le b, x \in \mathbb{Z}_+^n\}$ (if feasible and LP relaxation has finite optimum):
    $z = \min\{F(b) : F(a_j) \ge c_j \text{ for all } j, F \in \mathcal{F}\}$, where $a_j$ is the $j$-th column of $A$, and $\mathcal{F}$ is the set of nondecreasing superadditive functions.
    This provides a strong duality theory for IPs, but finding or working with such functions $F$ is generally hard.

## 6. Linear Programming and Polyhedra (Wolsey, pp. 32-34)

This section from Wolsey provides foundational LP theory relevant to understanding relaxations and bounds.
-   **Farkas' Lemma**: $P = \{x \in \mathbb{R}_+^n : Ax \le b\} \ne \emptyset$ if and only if $u^Tb \ge 0$ for all $u \in \mathbb{R}_+^m$ such that $u^TA \ge 0$.
-   **Polyhedra Representation (Minkowski's Theorem)**: Any non-empty polyhedron $P$ can be represented as the sum of a convex combination of its extreme points and a non-negative combination of its extreme rays.
    $P = \{x : x = \sum \lambda_s x^s + \sum \mu_t v^t, \sum \lambda_s = 1, \lambda \ge 0, \mu \ge 0\}$.
-   **LP Duality Outcomes**:
    1.  If Primal (P) is feasible and unbounded ($cr > 0$ for some ray $r$), then Dual (U) is infeasible.
    2.  If Dual (U) is feasible and unbounded ($vb < 0$ for some ray $v$), then Primal (P) is infeasible.
    3.  If both P and U are feasible, then $z = w$, and the optima are achieved at extreme points.

## 7. Primal Bounds: Greedy and Local Search Heuristics (Wolsey, pp. 34-38; 02.pdf, p.5)

Heuristics are used to find good feasible solutions quickly, providing primal bounds.

### 7.1. Heuristics from Restrictions
-   **Definition (Wolsey, p. 34)**: (RE) $z^{RE} = \max\{f(x) : x \in T \subseteq \mathbb{R}^n\}$ is a restriction of IP if $T \subseteq X$ and $f(x) \le c(x)$ for $x \in T$.
-   If $x^{RE}$ is optimal for RE, then $c(x^{RE})$ is a lower bound for IP.
-   Example: For Capacitated Lot-Sizing (CLS), a restriction can be formed by setting $y_t = C_t x_t$ (produce at full capacity if producing).

### 7.2. Greedy Heuristics
-   Construct a solution step-by-step, making the locally optimal choice at each step.
-   **0-1 Knapsack (Wolsey, p. 35; 02.pdf, p.5)**: Order items by $c_j/a_j$ ratio and add items greedily until the knapsack is full.
-   **STSP (Wolsey, p. 36; 02.pdf, p.5)**: Order edges by non-decreasing cost. Add edges if they don't create a cycle or increase a node's degree beyond 2, until a tour is formed.

### 7.3. Local Search Heuristics
-   Start with an initial feasible solution (incumbent).
-   Define a **neighborhood** $N(x)$ of solutions "close" to $x$.
-   Iteratively search $N(x)$ for a better solution $x'$. If found, $x \leftarrow x'$, repeat.
-   If no better solution is in $N(x)$, $x$ is a **local optimum**.
-   **Uncapacitated Facility Location (UFL) (Wolsey, p. 37)**:
    * Neighborhood: Solutions obtained by opening one closed facility or closing one open facility.
    * Cost: $\sum_{i} \min_{j \in S} c_{ij} + \sum_{j \in S} f_j$ for open set $S$.
-   **Graph Equipartition (Wolsey, p. 37)**:
    * Problem: Partition $V$ into $S$ and $V \setminus S$ with $|S| = \lfloor n/2 \rfloor$ to minimize edges in the cut $\delta(S, V \setminus S)$.
    * Neighborhood: Solutions obtained by swapping one node from $S$ with one from $V \setminus S$.

These heuristics are crucial for finding initial primal bounds and can be very effective in practice, though they generally don't guarantee optimality.
