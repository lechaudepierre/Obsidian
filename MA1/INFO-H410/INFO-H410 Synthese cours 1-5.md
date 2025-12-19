# AI Knowledge Resume

## Summary

A comprehensive understanding of Artificial Intelligence principles, methodologies, and applications, derived from detailed course material. Key areas of knowledge include classical problem-solving and search techniques, game theory, logical reasoning and theorem proving, and modern machine learning approaches including neural networks, deep learning, and reinforcement learning. Familiar with the theoretical underpinnings, practical applications, and philosophical considerations within the field of AI.

---

## Core AI Concepts & Techniques

### 1. Problem Solving & Search (Good Old-Fashioned AI - GOFAI)

* **Problem Formulation:**
    * **States:** Representations of problem configurations.
    * **Initial State:** The starting point of the problem.
    * **Operators/Actions:** Transitions between states.
    * **Goal Test:** Determines if a state is a solution.
    * **State Space:** The set of all reachable states.
    * **Path Cost:** Assigns a cost to a sequence of actions.
    * **Abstraction:** Simplifying reality to create a manageable problem definition.
* **Uninformed Search Strategies (Blind Search):**
    * **Breadth-First Search (BFS):**
        * Explores level by level.
        * Complete and optimal (if step costs are uniform).
        * Time/Space Complexity: $O(b^d)$.
    * **Depth-First Search (DFS):**
        * Explores as deep as possible along each branch.
        * Not complete (can fail in infinite spaces or loops).
        * Time Complexity: $O(b^m)$, Space Complexity: $O(bm)$ (more memory efficient than BFS).
        * Not optimal.
    * **Uniform-Cost Search (UCS) / Best-First Search:**
        * Expands the node with the lowest path cost $g(n)$.
        * Complete and optimal (if step costs > 0).
        * Time/Space complexity related to nodes with path cost less than optimal.
    * **Iterative Deepening Search (IDS):**
        * Combines benefits of DFS (space) and BFS (completeness, optimality for unit costs).
        * Performs DFS with increasing depth limits.
        * Time Complexity: $O(b^d)$, Space Complexity: $O(bd)$.
* **Informed Search Strategies (Heuristic Search):**
    * Utilize problem-specific knowledge (heuristics) to guide the search.
    * **Heuristic Function ($h(n)$):** Estimates cost from node $n$ to the goal.
    * **Greedy Best-First Search:**
        * Expands the node that appears closest to the goal (minimizes $h(n)$).
        * Not optimal or complete. Can get stuck in local optima.
    * **A\* Search:**
        * Evaluation function: $f(n) = g(n) + h(n)$ (cost so far + estimated cost to goal).
        * Complete and optimal if $h(n)$ is **admissible** (never overestimates the true cost) and consistent.
        * Expands the fewest nodes for a given heuristic.
        * Effective Branching Factor ($b^*$): Measures heuristic quality.
    * **Admissible Heuristics Examples (8-Puzzle):**
        * $h_1$: Number of misplaced tiles.
        * $h_2$: Sum of Manhattan distances of tiles from their goal positions ($h_2$ is generally better).
* **Local Search Algorithms:**
    * Operate on a single current state, trying to improve it. Useful for optimization problems where the path is irrelevant.
    * Use little memory.
    * **Hill-Climbing Search:**
        * Moves towards increasing value (uphill).
        * Terminates at local maxima/minima ("foothills," "plateaux," "ridges").
        * Variants: Stochastic hill-climbing, first-choice hill-climbing, random-restart hill-climbing.
    * **Simulated Annealing:**
        * Escapes local optima by allowing some "bad" moves, controlled by a "temperature" parameter that decreases over time.
        * Probability of accepting a worse move: $e^{\Delta E/T}$.
        * Can find global optimum with high probability if cooled slowly.
    * **Local Beam Search:**
        * Keeps track of $k$ states.
        * Generates successors for all $k$ states and selects the best $k$ successors.
        * Risk of lacking diversity.
* **Constraint Satisfaction Problems (CSPs):**
    * Finding a configuration that satisfies given constraints (e.g., n-Queens problem).

### 2. Game Theory & Adversarial Search

* For multi-agent environments where agents have conflicting goals.
* Focus on deterministic, perfect information, zero-sum games.
* **Game Formulation:** Initial state, successor function, terminal test, utility function (payoff).
* **MINIMAX Algorithm:**
    * Assumes optimal play from both MAX (player) and MIN (opponent).
    * MAX tries to maximize the utility; MIN tries to minimize it.
    * Calculates the minimax value for each node recursively.
    * Complexity: Time $O(b^m)$, Space $O(bm)$ or $O(m)$ (for DFS).
* **Alpha-Beta Pruning:**
    * Optimizes MINIMAX by pruning branches that won't influence the final decision.
    * Maintains two values:
        * $\alpha$: Best value found so far for MAX along the path.
        * $\beta$: Best value found so far for MIN along the path.
    * Prunes if a node's value is worse than already available choices ($\text{value} \ge \beta$ for MAX node, $\text{value} \le \alpha$ for MIN node).
    * Effectiveness depends on move ordering. With perfect ordering, time complexity can be reduced to $O(b^{m/2})$.
* **Heuristic Evaluation Functions (for games):**
    * Used when the search cannot reach terminal states (due to time/resource limits).
    * Estimates the utility of a game position.
    * Applied at a cutoff depth.
    * Important for quiescent states (stable evaluations).
* **Games with Chance:**
    * Incorporate chance nodes (e.g., dice rolls in Backgammon).
    * **Expected Minimax Value:** Calculates the expected utility by considering probabilities of chance outcomes.
        * For a chance node: $\sum_{s \in \text{successors}(n)} P(s) \times \text{EXPECTEDMINIMAX-VALUE}(s)$.

### 3. Knowledge Representation & Reasoning

* **Logic:**
    * **Propositional Logic:** Deals with propositions (true/false statements).
    * **First-Order Predicate Calculus (FOPC):** More expressive, with variables, quantifiers ($\forall, \exists$), predicates, and functions.
* **Inference Rules & Theorem Proving:**
    * **Modus Ponens:** If $P \rightarrow Q$ and $P$ are true, then $Q$ is true. $(\{P \rightarrow Q, P\} \models Q)$.
    * **Modus Tollens:** If $P \rightarrow Q$ is true and $\neg Q$ is true, then $\neg P$ is true. $(\{P \rightarrow Q, \neg Q\} \models \neg P)$.
    * **Resolution:** A single, sound, and complete inference rule for FOPC.
        * **Unit Resolution:** $\{P \lor Q, \neg Q\} \models P$.
        * **Generalized Resolution:** $\{P \lor Q, R \lor \neg Q\} \models P \lor R$.
    * **Resolution Refutation:**
        1.  Convert all sentences in the Knowledge Base (KB) and the *negation* of the sentence to be proved ($\neg\alpha$) into **Clausal Normal Form (CNF)**.
        2.  Repeatedly apply the resolution rule.
        3.  If an empty clause (contradiction) is derived, then the original sentence $\alpha$ is entailed by KB.
    * **Conjunctive Normal Form (CNF):** A conjunction of disjunctions of literals.
        * Conversion steps: Eliminate implications, move negations inward (De Morgan's laws), standardize variables, Skolemize (replace existentially quantified variables with Skolem constants/functions), drop universal quantifiers, distribute $\lor$ over $\land$.
    * **Unification:** The process of finding substitutions for variables to make two logical expressions identical. Essential for applying resolution in FOPC.
* **Knowledge Representation Schemes:**
    * **Scripts:** Frame-like structures representing stereotyped sequences of events (e.g., Restaurant Script describing typical actions and roles).
    * **Rule-Based Systems:** Knowledge encoded as IF-THEN rules (e.g., Car Diagnosis system).

### 4. Machine Learning & Modern AI

* **Fundamental Mechanisms:** Search, Optimization, and Learning.
* **Learning Paradigms:**
    * **Supervised Learning:** Learning from labeled data (e.g., classification, regression).
    * **Unsupervised Learning:** Finding patterns in unlabeled data (e.g., clustering).
    * **Reinforcement Learning (RL):** Agent learns by interacting with an environment and receiving rewards or penalties.
* **Neural Networks (NNs) & Deep Learning (DL):**
    * Inspired by biological neural networks.
    * **Perceptron:** Simplest NN, a linear classifier (Rosenblatt).
    * **Multi-Layer Networks:** Networks with hidden layers, capable of learning complex non-linear functions.
    * **Deep Learning:** NNs with many layers (deep architectures).
        * Automatic feature extraction from raw data (contrasted with traditional ML requiring manual feature engineering).
        * Applications: Image recognition (Cat/Dog classification), speech recognition, Natural Language Processing (NLP).
    * **Transformers:** A specific DL architecture, highly successful in NLP.
        * Core components: Self-attention, multi-head attention, positional encoding, encoder-decoder stacks.
        * Example: Basis for models like ChatGPT.
        * Represents words/tokens as vectors in high-dimensional space.
* **Reinforcement Learning (RL):**
    * Agent learns a policy (mapping from states to actions) to maximize cumulative reward.
    * **Q-Learning:** A model-free RL algorithm that learns the value of taking an action in a particular state (Q-value).
        * $Q(s,a) \leftarrow Q(s,a) + \alpha [R(s,a) + \gamma \max_{a'} Q(s',a') - Q(s,a)]$
        * $\alpha$: learning rate, $\gamma$: discount factor.
    * Applications: Games (Snake, AlphaGo partially, TD-Gammon), robotics, control systems.
* **Evolutionary Computation:**
    * **Genetic Algorithms (GAs):** Optimization algorithms inspired by natural selection.
        * Maintain a population of candidate solutions (chromosomes).
        * Use operators like selection, crossover (recombination), and mutation.
        * Evaluate solutions using a fitness function.
        * Applications: Optimization problems (e.g., 15-puzzle, pathfinding).
    * **Ant Colony Optimization (ACO):** Inspired by the foraging behavior of ants, using pheromone trails to find optimal paths.
* **Decision Trees:**
    * Tree-like model where each internal node represents a test on an attribute, each branch a test outcome, and each leaf node a class label.
    * Example Application: Credit scoring.
* **Big Data in AI:** The availability of massive datasets has been a key driver for the success of modern AI, especially Deep Learning.
* **Hybrid Approaches:** Combining different AI techniques (e.g., learning heuristics for A\*, using GAs to optimize NN parameters).

### 5. Philosophical & Cognitive Aspects of AI

* **Definitions of AI:**
    * Building intelligent entities.
    * Getting computers to do tasks requiring human intelligence.
    * Systems that think like humans, act like humans, think rationally, act rationally.
* **Goals of AI:**
    * To understand human intelligence better.
    * To create useful "smart" programs.
* **Historical Perspectives:**
    * **GOFAI (Symbolic AI):** Focus on logic, symbolic manipulation, explicit knowledge representation.
    * **Connectionism (Sub-symbolic AI):** Focus on NNs, distributed representations, learning from data.
    * Key figures/systems: Eliza (early NLP), Watson (Jeopardy), AlphaGo, ChatGPT.
* **Critiques and Challenges:**
    * **Searle's Chinese Room Argument:** Questions whether symbol manipulation truly constitutes understanding.
    * **The "Black Box" Problem of Deep Learning:** Difficulty in interpreting the internal workings and decisions of complex DL models.
    * Limitations of GOFAI in handling complex, real-world perception and ambiguity.
* **Cognitive Science Links:**
    * **Two Cognitive Systems (Kahneman):**
        * **System 1:** Fast, automatic, unconscious, intuitive (like animal intelligence or learned NN responses).
        * **System 2:** Slow, deliberate, conscious, effortful (like GOFAI reasoning).
    * Human intelligence involves both embodied, automatic skills and conscious reasoning.

---

## Key AI Problems & Examples Studied

* **Pathfinding/Route Planning:**
    * Romania map problem (illustrating BFS, DFS, UCS, A\*).
    * Dijkstra's algorithm.
* **Puzzles:**
    * 8-Puzzle / 15-Puzzle (illustrating heuristic search, GA, Q-Learning).
    * N-Queens Problem (illustrating local search, CSPs).
    * Water Jug Problem (illustrating basic problem formulation and state space search).
* **Games:**
    * Tic-Tac-Toe (illustrating MINIMAX).
    * Connect-4 (MINIMAX, learning heuristics).
    * Go (AlphaGo/AlphaZero - combination of Deep Learning, Monte Carlo Tree Search, Reinforcement Learning).
    * Snake, Tetris (illustrating RL, NN+GA).
* **Real-World Applications:**
    * **Autonomous Driving:** NAVLAB (using NNs for vision and control).
    * **Natural Language Processing (NLP):**
        * Machine Translation.
        * Chatbots (Eliza, ChatGPT using Transformers).
    * **Diagnosis Systems:** Car problem diagnosis (rule-based).
    * **Credit Scoring:** Using decision trees/ML models.
    * **Image Recognition:** Cat vs. Dog classification (NNs, DL).
    * **Robotics & Autonomous Agents.**

---

## Methodologies & Perspectives

* **Three Fundamental Mechanisms of AI:** Search, Optimization, and Learning are core to most AI solutions.
* **Interdisciplinary Nature:** AI draws from Computer Science, Psychology, Philosophy, Linguistics, Biology.
* **Evolution of AI:** Shift from purely symbolic approaches (GOFAI) to data-driven learning methods, with current trends focusing on Deep Learning and Large Language Models.
* **Hybrid Systems:** The power of combining different AI techniques to solve complex problems.
* **Ethical Considerations & Trust:** Growing importance of building AI we can trust, understanding biases, and ensuring responsible development (implied by "black box" discussion and "Rebooting AI").

