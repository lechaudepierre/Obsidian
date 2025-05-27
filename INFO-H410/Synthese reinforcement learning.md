# Fundamentals of Reinforcement Learning: A Detailed Summary

This document summarizes the key concepts presented in "Fundamentals of Reinforcement Learning" by Yann-Michaël De Hauwere.

## 1. Introduction to Reinforcement Learning (RL)

Reinforcement Learning (RL) is a field of machine learning inspired by behaviorist psychology. It's about an **agent** learning to make a sequence of decisions by interacting with an **environment** to achieve a goal.

* **Psychological Basis:**
    * **Edward Thorndike's Law of Effect:** Behaviors followed by satisfaction become more likely, while those followed by discomfort become less likely.
    * **B.F. Skinner's Principle of Reinforcement:** Illustrated by the "Skinner Box," where animals are trained by providing feedback (rewards or punishments) for their actions.
* **Core Idea:** Learning by interacting with the environment and receiving numerical **reward signals**. The agent learns *what to do* (how to map situations to actions) to maximize this cumulative reward.

## 2. Why Reinforcement Learning? Applications

RL is particularly useful for:

* **Control Learning:**
    * **Robotics:** A robot learning to dock on a battery charger.
    * **Optimization:** Learning to choose actions to optimize factory output.
    * **Game Playing:** Learning to play games like Backgammon. The document mentions a Backgammon program trained by playing 1.5 million games against itself, achieving performance comparable to the best human players.

## 3. The Reinforcement Learning Setting

The RL process involves an **agent** interacting with an **environment** over a sequence of discrete time steps.

* At each time step $t$:
    1.  The agent observes the current **state** $s(t)$ of the environment.
    2.  The agent performs an **action** $a(t)$.
    3.  The environment transitions to a new state $s(t+1)$.
    4.  The agent receives a numerical **reward** $r(t+1)$ from the environment.

The goal is to learn a strategy to maximize the cumulative reward over time.

## 4. Key Features of RL

* **Learner is not told which action to take:** Learning occurs through a trial-and-error approach.
* **Possibility of delayed reward:** Actions may not result in immediate rewards; the agent might need to sacrifice short-term gains for greater long-term rewards.
    * *Example:* In chess, sacrificing a pawn (short-term loss) might lead to a better position and eventual win (long-term gain).
* **Need to balance exploration and exploitation:**
    * **Exploitation:** Making the best decision given current knowledge.
    * **Exploration:** Gathering more information by trying new actions that might not seem optimal initially but could lead to better overall rewards.
* **Partially observable states:** The agent might not have complete information about the environment's state.
* **Learning multiple tasks:** An agent might need to learn different tasks using the same sensors.
* **Position:** RL lies between supervised learning (where labeled data is provided) and unsupervised learning (where no labels are given).

## 5. Elements of RL

* **Time Steps:** Need not refer to fixed intervals of real time.
* **Actions ($a_t$):**
    * Can be low-level (e.g., voltage to motors).
    * Can be high-level (e.g., "go left," "go right").
    * Can be "mental" (e.g., shift focus of attention).
* **States ($s_t$):**
    * Can be low-level "sensations" (e.g., temperature, (x, y) coordinates).
    * Can be high-level abstractions or symbolic representations.
    * Can be subjective or internal (e.g., "surprised," "lost").
* **Environment:** The agent doesn't necessarily have prior knowledge of the environment's dynamics.
* **State Transitions:**
    * Changes to the internal state of the agent.
    * Changes in the environment due to the agent's action.
    * Can be non-deterministic (i.e., the same action in the same state might lead to different next states).
* **Rewards ($r_t$):** Numerical values representing goals or subgoals. Can also represent costs like duration.

## 6. The Agent's Policy ($\pi$)

The **policy** $\pi$ defines the agent's behavior. At time $t$, it's a mapping from states to action probabilities:
$\pi_t(s, a) = P(a_t = a | s_t = s)$
This is the probability of taking action $a$ when in state $s$. RL methods specify how the agent changes its policy based on its experiences to maximize long-run rewards.

## 7. The Objective: Discounted Return

The agent's goal is to maximize the **expected return**, which is the sum of discounted future rewards.
The **return** $R_t$ from time step $t$ is defined as:
$R_t = r_{t+1} + \gamma r_{t+2} + \gamma^2 r_{t+3} + ... = \sum_{k=0}^{\infty} \gamma^k r_{t+k+1}$

* $\gamma$ (gamma) is the **discount factor** ($0 \le \gamma \le 1$).
    * If $\gamma \approx 0$, the agent is "shortsighted" (prioritizes immediate rewards).
    * If $\gamma \approx 1$, the agent is "farsighted" (values future rewards more).

* **Examples:**
    * **Backgammon:** Reward = +100 for a win, -100 for a loss, 0 for all other states.
    * **Pole Balancing:** A continuing task where the goal is to keep a pole balanced on a cart.
        * Reward = -1 upon failure (pole falls).
        * Return = $-\gamma^k$ for failing after $k$ steps.
        * Maximizing return means avoiding failure for as long as possible.

## 8. Markov Decision Processes (MDPs)

Many RL problems can be formalized as MDPs.

* **Markov Property:** The future is independent of the past given the present. The current state $s_t$ captures all relevant information from the history.
    $P(s_{t+1}, r_{t+1} | s_t, a_t) = P(s_{t+1}, r_{t+1} | s_t, a_t, r_t, s_{t-1}, a_{t-1}, ..., r_1, s_0, a_0)$
* If an RL task has the Markov property and finite state and action spaces, it's a **finite MDP**.
* An MDP is defined by:
    * **State and Action Sets:** $S$ and $A$.
    * **Transition Function ($\mathcal{P}_{ss'}^{a}$):** The probability of transitioning from state $s$ to state $s'$ given action $a$.
        $\mathcal{P}_{ss'}^{a} = P(s_{t+1} = s' | s_t = s, a_t = a)$
    * **Reward Function ($\mathcal{R}_{ss'}^{a}$):** The expected immediate reward after transitioning from state $s$ to state $s'$ due to action $a$.
        $\mathcal{R}_{ss'}^{a} = E[r_{t+1} | s_t = s, a_t = a, s_{t+1} = s']$

## 9. Value Functions

Value functions estimate "how good" it is for an agent to be in a given state, or to take a given action in a given state.

* **State-Value Function ($V^{\pi}(s)$):** The expected return when starting in state $s$ and following policy $\pi$ thereafter.
    $V^{\pi}(s) = E_{\pi}[R_t | s_t = s] = E_{\pi}[\sum_{k=0}^{\infty} \gamma^k r_{t+k+1} | s_t = s]$
* **Action-Value Function ($Q^{\pi}(s,a)$):** The expected return when starting in state $s$, taking action $a$, and thereafter following policy $\pi$.
    $Q^{\pi}(s,a) = E_{\pi}[R_t | s_t = s, a_t = a]$
* **Optimal Policy ($\pi^*$):** A policy is optimal if its expected return is greater than or equal to that of any other policy for all states. The goal is to find $\pi^* = \text{argmax}_{\pi} V^{\pi}(s)$.
    * $V^*(s) = \max_{\pi} V^{\pi}(s)$
    * $Q^*(s,a) = \max_{\pi} Q^{\pi}(s,a)$

* **Example: Grid World**
    The document shows a grid world where an agent tries to reach a goal state 'G'.
    * Immediate rewards $r(s,a)$ are given for actions leading to certain states (e.g., +100 for entering G).
    * $Q(s,a)$ values represent the expected cumulative discounted reward for taking an action in a state.
    * $V^*(s)$ values represent the maximum expected cumulative discounted reward from each state.
    * An optimal policy shows the best action to take in each state. For example, if in a state adjacent to 'G', the optimal action is to move towards 'G'.

## 10. Bellman Equation

The Bellman equation expresses a recursive relationship for value functions. For $V^{\pi}(s)$:
$V^{\pi}(s) = \sum_{a \in A(s)} \pi(s,a) \sum_{s' \in S} \mathcal{P}_{ss'}^{a} [\mathcal{R}_{ss'}^{a} + \gamma V^{\pi}(s')]$
This equation relates the value of a state to the values of its successor states, averaged over all possible actions and next states.

## 11. Learning an Optimal Policy Online: Temporal Difference (TD) Methods

Often, the transition ($\mathcal{P}$) and reward ($\mathcal{R}$) functions are unknown. TD methods learn directly from raw experience without needing a model of the environment (they are **model-free**). They update predicted state values based on observed rewards and successor states.

## 12. Q-Learning

Q-learning is a prominent model-free, off-policy TD control algorithm. "Off-policy" means it can learn the optimal policy even if the actions are selected using a different, exploratory policy.

* **Q-function and Optimal Policy:**
    The optimal action-value function $Q^*(s,a)$ is related to the immediate reward and the optimal value of the next state:
    $Q^*(s,a) = r(s,a) + \gamma V^*(\delta(s,a))$, where $\delta(s,a)$ is the next state after taking action $a$ in state $s$.
    Since $V^*(s) = \max_{a'} Q^*(s,a')$, we can write:
    $Q^*(s_t, a_t) = r(s_t, a_t) + \gamma \max_{a'} Q^*(s_{t+1}, a')$
* **Training Rule to Learn Q (Iterative Update):**
    Let $\hat{Q}$ be the learner's current approximation of $Q^*$. The update rule is:
    $\hat{Q}(s,a) \leftarrow r + \gamma \max_{a'} \hat{Q}(s', a')$
    where $(s,a)$ is the current state-action pair, $r$ is the observed reward, and $s'$ is the next state.
* **Q-Learning Algorithm Update Rule:**
    $Q(s_t, a_t) \leftarrow Q(s_t, a_t) + \alpha [r_{t+1} + \gamma \max_{a} Q(s_{t+1}, a) - Q(s_t, a_t)]$
    * $s_t, a_t$: current state and action.
    * $r_{t+1}$: reward received after taking action $a_t$.
    * $s_{t+1}$: next state observed.
    * $\alpha$ (alpha): the **learning rate** ($0 < \alpha \le 1$), determining how much the new information overrides the old estimate.
    * $\gamma$ (gamma): the discount factor.
    * $\max_{a} Q(s_{t+1}, a)$: the estimate of the optimal future value from the next state $s_{t+1}$.
    The term $[r_{t+1} + \gamma \max_{a} Q(s_{t+1}, a) - Q(s_t, a_t)]$ is the **TD error**.
* **Q-Learning Algorithm Steps:**
    1.  Initialize $Q(s,a)$ arbitrarily (e.g., to zeros) for all state-action pairs.
    2.  Repeat (for each episode):
        a.  Initialize $s$ (starting state of the episode).
        b.  Repeat (for each step of the episode):
            i.  Choose action $a$ from state $s$ using a policy derived from $Q$ (e.g., $\epsilon$-greedy).
            ii. Take action $a$, observe reward $r$ and next state $s'$.
            iii. Update $Q(s,a)$ using the rule:
                $Q(s,a) \leftarrow Q(s,a) + \alpha [r + \gamma \max_{a'} Q(s', a') - Q(s,a)]$
            iv. $s \leftarrow s'$ (move to the next state).
        c.  Until $s$ is a terminal state.
* **Convergence:** Q-learning is proven to converge to the optimal policy $Q^*$ given sufficient updates for each state-action pair and a decreasing learning rate $\alpha$.

## 13. Action Selection: The Exploration-Exploitation Dilemma

To learn effectively, the agent must balance:

* **Exploitation:** Choosing actions that are known to yield high rewards based on current estimates of $Q(s,a)$.
* **Exploration:** Trying out new or seemingly suboptimal actions to discover potentially better rewards and improve estimates of $Q(s,a)$. Without exploration, the agent might get stuck in a local optimum.

Common action selection strategies:

* **$\epsilon$-greedy (Epsilon-greedy):**
    * With probability $1-\epsilon$: choose the action with the highest $Q$-value (exploit).
    * With probability $\epsilon$: choose a random action (explore).
    * $\epsilon$ is usually a small value, often decreased over time.
* **Boltzmann Exploration (Softmax):**
    Actions are chosen probabilistically based on their $Q$-values, using a temperature parameter $\tau$ (tau).
    $\pi_t(s,a) = \frac{e^{Q_t(s,a)/\tau}}{\sum_{a' \in A} e^{Q_t(s,a')/\tau}}$
    * High $\tau$: actions are nearly equiprobable (more exploration).
    * Low $\tau$ (approaching 0): higher probability for actions with higher $Q$-values (more exploitation).

## 14. Updating Q: An Example

Consider a state $s_1$ and action $a_{\text{right}}$ leading to state $s_2$.
Suppose $r=0$ for this transition, $\gamma=0.9$.
Initial $Q(s_1, a_{\text{right}})$ is, say, 72.
In state $s_2$, the possible actions have $Q$-values: $Q(s_2, a_1)=63, Q(s_2, a_2)=81, Q(s_2, a_3)=100$.
Then $\max_{a'} \hat{Q}(s_2, a') = 100$.
The updated $Q$-value for $(s_1, a_{\text{right}})$ would be (using the simpler form for illustration, assuming $\alpha=1$ for this step):
$\hat{Q}(s_1, a_{\text{right}}) \leftarrow r + \gamma \max_{a'} \hat{Q}(s_2, a')$
$\hat{Q}(s_1, a_{\text{right}}) \leftarrow 0 + 0.9 \times 100 = 90$.
The document notes that if rewards are non-negative, then $\hat{Q}_{n+1}(s,a) \ge \hat{Q}_n(s,a)$ and $0 \le \hat{Q}_n(s,a) \le Q(s,a)$ (assuming initialization to 0).

## 15. Convergence of Deterministic Q-Learning

Q-learning converges to the true $Q^*$ function if each state-action pair $(s,a)$ is visited infinitely often.
The proof sketch involves showing that the maximum error $\Delta_n = \max_{s,a} |\hat{Q}_n(s,a) - Q(s,a)|$ decreases with each update:
$|\hat{Q}_{n+1}(s,a) - Q(s,a)| \le \gamma \Delta_n$.
Since $\gamma < 1$, the error $\Delta_n$ converges to 0 as $n \rightarrow \infty$.

## 16. Extensions to Reinforcement Learning

* **Multi-step TD:** Instead of just the immediate reward $r_{t+1}$, use a sequence of $n$ consecutive rewards for the value update. This can sometimes speed up learning as it considers more future consequences directly.
* **Eligibility Traces:** A mechanism to assign credit or blame for rewards to multiple past state-action pairs. More recent pairs or more frequently visited pairs might get more credit. This helps propagate reward information more efficiently.
    * The diagram shows how an error $\delta_t$ at time $t$ can influence updates for states visited earlier ($s_{t-1}, s_{t-2}, ...$) via eligibility traces $e_t$.
* **Reward Shaping:** Incorporating domain knowledge by providing additional, intermediate rewards to the agent during an episode. This can guide the agent and help it learn faster.
    * If done carefully (e.g., using potential-based shaping functions), optimal policies can be preserved.
* **Function Approximation:**
    * The tabular representation of $Q(s,a)$ (a lookup table) becomes intractable for problems with large or continuous state and/or action spaces.
    * Function approximators (e.g., neural networks, linear functions) can be used to generalize $Q$-values (or $V$-values or policies) over these large spaces. Instead of storing a value for each $(s,a)$, the function learns to output an approximate value given $s$ and $a$ (or just $s$).

## 17. Conclusion

The document provides a foundational overview of Reinforcement Learning, covering its basic principles, the mathematical framework of MDPs, core algorithms like Q-learning, and important considerations such as the exploration-exploitation trade-off and extensions for more complex problems.
