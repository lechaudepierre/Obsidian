This document provides an overview of Classification in machine learning.

## Classification Problem

The classification problem involves an input $x \in \mathbb{R}^n$ and a categorical output variable $y$ that takes values in a finite set $\{c_1, ..., c_K\}$, where $K$ is the number of classes.
Examples include:
* Input $x$ is the month, and output $y$ takes $K=2$ values {RAIN, NO.RAIN}.
* Input $x$ represents an email's features, and output $y$ is {SPAM, NO.SPAM}.

---
## Separability and Noise

* **Separable Classes:** For any given input $x$, the output $y$ always takes the same class value. Mathematically, $\forall x \in \mathbb{R}^n \exists c_k : Prob\{y=c_k|x\}=1$. This is also known as a noiseless or degenerate situation.
* **Non-Separable Classes:** For at least one input $x$, realizations of $y$ can take different class values. Mathematically, $\exists x \in \mathbb{R}^n : \forall c_k, Prob\{y=c_k|x\}<1$.

---
## Stochastic Non-Separable Setting

To model non-separable tasks, a stochastic setting is considered, meaning data are noisy and follow a probabilistic distribution. Given an input $x$, $y$ does not always take the same value but follows a conditional probability distribution:
$\sum_{k=1}^{K} Prob\{y=c_k|x\} = 1$

---
## Binary Classification Problem ($K=2$)

This is a problem where the output class $y$ can take only two values, often simplified to $y \in \{c_1=0, c_2=1\}$.
Let $p_0 = Prob\{y=0\}$ and $p_1 = Prob\{y=1\}$, with $p_0 + p_1 = 1$.
For such a binary variable:
* Expected value: $E[y] = 0 \cdot p_0 + 1 \cdot p_1 = p_1$
* Variance: $Var[y] = E[(y-E[y])^2] = p_0(0-p_1)^2 + p_1(1-p_1)^2 = p_1(1-p_1)$
* Entropy: $H[y] = E[-\log p(y)] = -p_0 \log p_0 - p_1 \log p_1$
The variance and entropy of a binary random variable attain their maximum value when $p_0 = p_1 = 1/2$.

---
## Degree of Non-Separability

For non-separable classes, the degree of non-separability for a given input $x$ can be quantified using measures based on the conditional probabilities $Prob\{y=1|x\} = p_1(x)$ and $Prob\{y=0|x\} = p_0(x)$:
* **Conditional Variance:** $Var[y|x] = p_1(x)(1-p_1(x))$
* **Conditional Entropy:** $H[y|x] = -p_0(x)\log p_0(x) - p_1(x)\log p_1(x)$
Both these measures reach their maximum when $p_0(x) = p_1(x) = 1/2$, indicating the highest uncertainty or non-separability at input $x$.

---
## Classification Probabilistic Setting

* $x \in \mathbb{R}^n$: Real-valued input vector.
* $y$: Categorical random output variable in $\{c_1, ..., c_K\}$.
* $Prob\{y=c_k|x\}$: Probability that the output belongs to the $k$-th class given input $x$. It holds that $\sum_{k=1}^{K}Prob\{y=c_k|x\}=1$.
* $\hat{c}(x)$: The predicted output class for input $x$, taking a value in $\{c_1, ..., c_K\}$.
* $L_{(K \times K)}$: A loss matrix, where $L_{jk} = L(c_j, c_k)$ represents the cost incurred when the predicted class is $\hat{c}(x)=c_j$ and the true class is $y=c_k$. The diagonal elements $L_{kk}$ are typically 0, and off-diagonal elements are non-negative.

### Average Cost of Classification
For a given input $x$ and a prediction $\hat{c}(x)$, the average cost (or expected loss) of this classification is:
$\sum_{k=1}^{K} L_{(\hat{c}(x),c_k)} Prob\{y=c_k|x\}$
The values in the loss matrix can have significant implications, for example, in safety-critical applications like self-driving cars, where the cost of misclassifying a pedestrian as a non-obstacle (or vice-versa) involves ethical considerations.

---
## Bayes Classifier

An optimal classification $\hat{c}(x)$ is one that minimizes the expected cost. The **Bayes classifier** $c^*(x)$ achieves this for every input $x$:
$c^*(x) = \arg\min_{c_j \in \{c_1, ..., c_K\}} \sum_{k=1}^{K} L_{j,k} Prob\{y=c_k|x\} = \arg\min_{c_j \in \{c_1, ..., c_K\}} E_y[L(c_j,y)|x]$
This means the Bayes classifier selects the class $c_j$ that minimizes the expected cost, given the input $x$.

### The 0-1 Loss Case
If a **0-1 loss function** is used (loss is 0 for correct classification, 1 for any misclassification), the Bayes classifier simplifies to selecting the class with the highest posterior probability:
$c^*(x) = \arg\min_{c_j \in \{c_1, ..., c_K\}} \sum_{k \neq j} Prob\{y=c_k|x\}$
$c^*(x) = \arg\min_{c_j \in \{c_1, ..., c_K\}} (1 - Prob\{y=c_j|x\})$
$c^*(x) = \arg\max_{c_j \in \{c_1, ..., c_K\}} Prob\{y=c_j|x\}$
This is known as selecting the **maximum a posteriori (MAP)** class.

---
## Bayes' Theorem for Classification

Bayes' theorem provides a way to calculate the posterior probability $Prob\{y=c_k|x\}$ using prior probabilities $Prob\{y=c_k\}$ and class-conditional probabilities $Prob\{x|y=c_k\}$ (or densities $p_x(x|y=c_k)$ for continuous $x$).

* **For continuous inputs $x$**:
    $Prob\{y=c_k|x\} = \frac{p_x(x|y=c_k)Prob\{y=c_k\}}{\sum_{j=1}^{K}p_x(x|y=c_j)Prob\{y=c_j\}}$
    Here, $p_x(x|y=c_k)$ is the class-conditional probability density function of $x$ given class $c_k$. Knowing $Prob\{y=c_k|x\}$ and the prior density $p_x(x)$, we can derive $p_x(x|y=c_k)$ using:
    $p_x(x|y=c_k) = \frac{Prob\{y=c_k|x\}p_x(x)}{Prob\{y=c_k\}}$

* **For discrete inputs $x$**:
    $Prob\{y=c_k|x\} = \frac{Prob\{x|y=c_k\}Prob\{y=c_k\}}{\sum_{j=1}^{K}Prob\{x|y=c_j\}Prob\{y=c_j\}}$
    Similarly, $Prob\{x|y=c_k\} = \frac{Prob\{y=c_k|x\}Prob\{x\}}{Prob\{y=c_k\}}$.

---
## Classification Strategies (When $Prob\{y=c_k|x\}$ is Unknown)

Optimal classification is possible only if $Prob\{y=c_k|x\}$ is known for all $k$. If not, several strategies are used:

1.  **Direct Estimation via Regression Techniques (primarily for $K=2$)**:
    If classes are denoted by $y=0$ and $y=1$, then $E[y|x] = 1 \cdot Prob\{y=1|x\} + 0 \cdot Prob\{y=0|x\} = Prob\{y=1|x\}$. The classification problem can be framed as a regression problem where the output is to be predicted in $\{0,1\}$.
    * **Limitations**:
        * Regression approaches often rely on least-squares, implicitly assuming conditional Gaussian distributions for the errors, which is unsuitable for binary 0/1 outputs.
        * Predictions can fall outside the $[0,1]$ interval, making them hard to interpret as probabilities.
        * Solutions can be highly sensitive to input distributions, giving too much weight to input points far from separating boundaries or to outliers.

2.  **Direct Estimation via Cross-Entropy**:
    Model the conditional distribution $Prob\{y=c_k|x\}$ with a set of $K$ functions $\hat{P}_k(x, \alpha)$, $k=1,...,K$, satisfying $\hat{P}_k(x, \alpha) > 0$ and $\sum_{k=1}^{K} \hat{P}_k(x, \alpha) = 1$.
    Parameters $\alpha$ are estimated by Maximum Likelihood or, equivalently, by minimizing the negative log-likelihood (cross-entropy):
    $J(\alpha) = -\log \prod_{i=1}^{N} Prob\{y=y_i|x_i\} = -\sum_{i=1}^{N} \log \hat{P}_{y_i}(x_i, \alpha)$
    * **Logistic Regression (for two-class tasks, $c_1=1, c_2=0$)**:
        The posterior probabilities are modeled as:
        $\hat{P}_1(x, \alpha) = \frac{\exp(x^T\alpha)}{1+\exp(x^T\alpha)}$ (probability of class 1)
        $\hat{P}_0(x, \alpha) = \frac{1}{1+\exp(x^T\alpha)}$ (probability of class 0, since $\hat{P}_2$ used in slides)
        This implies the log-odds (logit) transformation is linear:
        $\log\frac{\hat{P}_1(x,\alpha)}{\hat{P}_0(x,\alpha)} = x^T\alpha$
        The cross-entropy function for binary classification ($y_i \in \{0,1\}$) becomes:
        $J(\alpha) = -\sum_{i=1}^{N} \{y_i \log\hat{P}_1(x_i,\alpha) + (1-y_i)\log(1-\hat{P}_1(x_i,\alpha))\}$
        $J(\alpha) = -\sum_{i=1}^{N} \{y_i x_i^T\alpha - \log(1+\exp(x_i^T\alpha))\}$
        Due to its nonlinear nature, an iterative solution like Iteratively Reweighted Least Squares (IRLS) is required.
    * Neural networks are also typical approaches relying on cross-entropy.

3.  **Discriminant Functions**:
    The classifier is defined as a set of $K$ discriminant functions $g_k(x)$, $k=1,...,K$. The predicted class $\hat{c}(x)$ is $c_k$ if $g_k(x) > g_j(x)$ for all $j \neq k$ (i.e., $k = \arg\max_j g_j(x)$).
    These functions partition the input space into $K$ decision regions separated by decision boundaries.
    For a 0-1 loss function, the optimal choice is $g_k(x) = Prob\{y=c_k|x\}$.
    Multiplying all $g_k(x)$ by the same positive constant or applying a monotonically increasing function $f(\cdot)$ to them (i.e., $f(g_k(x))$) does not change the classification result. Thus, any of the following choices gives identical classification:
    * $g_k(x) = Prob\{y=c_k|x\} = \frac{p(x|y=c_k)P(y=c_k)}{p(x)}$
    * $g_k(x) = p(x|y=c_k)P(y=c_k)$ (since $p(x)$ is common to all)
    * $g_k(x) = \ln p(x|y=c_k) + \ln P(y=c_k)$ (using $\ln$ as the monotonic function)

4.  **Density Estimation via Bayes' Theorem (Generative Approach)**:
    This approach estimates the class-conditional densities $p(x|y=c_k)$ and prior probabilities $P(y=c_k)$. Then, Bayes' theorem is used to find the posterior $Prob\{y=c_k|x\}$:
    $Prob\{y=c_k|x\} = \frac{p(x|y=c_k)P(y=c_k)}{p(x)}$
    This is called a generative approach, in contrast to the discriminative approaches of the previous strategies which model $Prob\{y=c_k|x\}$ or the decision boundary directly.

---
## Discriminant Functions in the Gaussian Case

Assume the class-conditional densities $p(x|y=c_k)$ are multivariate normal (Gaussian): $p(x|y=c_k) \sim \mathcal{N}(\mu_k, \Sigma_k)$, where $x \in \mathbb{R}^n$, $\mu_k$ is the $n \times 1$ mean vector for class $k$, and $\Sigma_k$ is the $n \times n$ covariance matrix for class $k$.
The density is: $p(x|y=c_k) = \frac{1}{(\sqrt{2\pi})^n \sqrt{\det(\Sigma_k)}} \exp\left\{-\frac{1}{2}(x-\mu_k)^T\Sigma_k^{-1}(x-\mu_k)\right\}$
The discriminant function becomes:
$g_k(x) = \ln p(x|y=c_k) + \ln P(y=c_k)$
$g_k(x) = -\frac{1}{2}(x-\mu_k)^T\Sigma_k^{-1}(x-\mu_k) - \frac{n}{2}\ln(2\pi) - \frac{1}{2}\ln(\det(\Sigma_k)) + \ln P(y=c_k)$

* **Quadratic Discriminant Analysis (QDA):** If no assumptions are made about $\Sigma_k$ (i.e., each class can have a different covariance matrix), the discriminant functions $g_k(x)$ are quadratic in $x$.
* **Linear Discriminant Analysis (LDA):** If we assume identical covariance matrices for all classes, $\Sigma_k = \Sigma$ for all $k$, the discriminant functions become linear in $x$. $\Sigma$ is often estimated as the pooled covariance matrix.
    * **Special Case: $\Sigma_k = \sigma^2 I$ (Spherical Covariance)**: All classes have identical, diagonal covariance matrices (spheres).
        The terms involving $\det(\Sigma_k) = (\sigma^2)^n$ and $\Sigma_k^{-1} = (1/\sigma^2)I$ become constants independent of $k$ or simplify. The discriminant function simplifies to:
        $g_k(x) = -\frac{||x-\mu_k||^2}{2\sigma^2} + \ln P(y=c_k)$
        Since the term $x^T x$ from $||x-\mu_k||^2 = (x-\mu_k)^T(x-\mu_k) = x^T x - 2\mu_k^T x + \mu_k^T \mu_k$ is common to all $k$, it can be ignored. This results in a linear discriminant function:
        $g_k(x) = w_k^T x + w_{k0}$
        where $w_k = \frac{1}{\sigma^2}\mu_k$ and $w_{k0} = -\frac{1}{2\sigma^2}\mu_k^T\mu_k + \ln P(y=c_k)$ (bias/threshold).
    * **Decision Boundary (for two classes, $\Sigma_k = \sigma^2 I$):** The set of points where $g_1(x) = g_2(x)$ forms a hyperplane with equation $w^T(x-x_0)=0$, where:
        $w = \mu_1 - \mu_2$
        $x_0 = \frac{1}{2}(\mu_1 + \mu_2) - \frac{\sigma^2}{||\mu_1 - \mu_2||^2} \ln\left(\frac{P(y=1)}{P(y=2)}\right)(\mu_1 - \mu_2)$
        This hyperplane passes through $x_0$ and is orthogonal to $w$ (the vector joining the means).
* **Uniform Prior Case ($P(y=c_k)$ are equal):** The $\ln P(y=c_k)$ term is constant and can be ignored. The decision rule becomes a **minimum distance classifier**:
    * If $\Sigma_k = \sigma^2 I$: Assign $x$ to the class of the nearest mean vector, using Euclidean distance $||x-\mu_k||^2$.
    * If $\Sigma_k = \Sigma$ (general LDA): Assign $x$ based on minimizing Mahalanobis distance: $\hat{c}(x) = \arg\min_k (x-\mu_k)^T\Sigma^{-1}(x-\mu_k)$.

**LDA Parameter Estimation (from data):**
Let $N_k$ be the number of samples in class $c_k$, and $N = \sum N_k$.
* Prior probability: $\hat{P}(y=c_k) = \frac{N_k}{N}$
* Class mean: $\hat{\mu}_k = \frac{1}{N_k} \sum_{i: y_i=c_k} x_i$
* Pooled covariance matrix: $\hat{\Sigma} = \frac{1}{N-K} \sum_{k=1}^{K} \sum_{i: y_i=c_k} (x_i - \hat{\mu}_k)(x_i - \hat{\mu}_k)^T$

---
## Hyperplanes and Linear Classifiers

A hyperplane in $\mathbb{R}^n$ is defined by $h(x, \beta) = \beta_0 + x^T\beta = 0$.
The vector $\beta^* = \frac{\beta}{||\beta||}$ is normal to the hyperplane.
The signed Euclidean distance of any point $x$ to the hyperplane is $\frac{1}{||\beta||}(x^T\beta + \beta_0)$.
If data can be separated by a linear boundary, there are often infinitely many such separating hyperplanes.

### Perceptron (Rosenblatt, 1950s)
A simple linear classifier.
* Classification rule for $y \in \{1, -1\}$: $\text{sign}(\beta_0 + x_q^T\beta)$.
* For all correctly classified points in the training set: $y_i(x_i^T\beta + \beta_0) > 0$.
* Parametric identification minimizes the **perceptron criterion** for misclassified points $\mathcal{M}$:
    $R_{emp}(\beta, \beta_0) = -\sum_{i \in \mathcal{M}} y_i(x_i^T\beta + \beta_0)$
    This is non-negative and proportional to the distance of misclassified points to the hyperplane. Gradients are:
    $\frac{\partial R_{emp}}{\partial\beta} = -\sum_{i \in \mathcal{M}} y_i x_i$
    $\frac{\partial R_{emp}}{\partial\beta_0} = -\sum_{i \in \mathcal{M}} y_i$
    A gradient descent procedure can be used.
* **Limitations**:
    * For separable data, many solutions exist depending on initialization.
    * For non-separable data, the algorithm will not converge.
    * Convergence can be very slow even for separable problems.

### Optimal Separating Hyperplane (Basis of SVMs)
This seeks the hyperplane that separates two classes by maximizing the distance to the closest point from either class (this distance is called the **margin**).
* This provides a unique solution to the separating hyperplane problem and is generally more robust.
* The search for the optimal hyperplane is modeled as the optimization problem:
    $\max_{\beta, \beta_0} C$
    subject to $\frac{1}{||\beta||} y_i(x_i^T\beta + \beta_0) \ge C$ for $i=1, ..., N$.
    This constraint ensures all points are at least distance $C$ from the boundary.
* This is a quadratic program solvable by standard convex optimization techniques.

---
## Naive Bayes Classifier

The Naive Bayes classifier often shows accuracy comparable to more complex models in some domains, especially with discrete inputs.
* For a classification task with $n$ discrete inputs $x = (x_1, ..., x_n)$ and a categorical output $y \in \{c_1, ..., c_K\}$, the Bayes optimal classifier (with 0-1 loss) is:
    $c^*(x) = \arg\max_{k=1,...,K} Prob\{y=c_k|x\}$
    Using Bayes' theorem, this is equivalent to:
    $c^*(x) = \arg\max_{k=1,...,K} Prob\{x|y=c_k\}Prob\{y=c_k\}$ (since $Prob\{x\}$ is constant for all $k$).
* $Prob\{y=c_k\}$ is easy to estimate from data (prior probability of class $k$).
* Estimating $Prob\{x|y=c_k\} = Prob\{x_1, ..., x_n|y=c_k\}$ is much harder due to the high dimensionality of $x$.
* **Naive Bayes Assumption:** The crucial simplifying assumption is that inputs are **conditionally independent given the class**:
    $Prob\{x|y=c_k\} = Prob\{x_1, ..., x_n|y=c_k\} = \prod_{j=1}^{n} Prob\{x_j|y=c_k\}$
* The Naive Bayes classification rule is then:
    $c_{NB}(x) = \arg\max_{k=1,...,K} Prob\{y=c_k\} \prod_{j=1}^{n} Prob\{x_j|y=c_k\}$
* For discrete inputs $x_j$, $Prob\{x_j|y=c_k\}$ is estimated from the frequencies of occurrences of different values of $x_j$ for a given class $c_k$ in the training data.

---
## K-Nearest Neighbors (KNN) Classifier

A non-parametric, instance-based learning algorithm.
For a query point:
1.  Compute the distance between the query and all training samples using a predefined metric.
2.  Rank the neighbors based on their distance to the query.
3.  Select a subset of the $K$ nearest neighbors.
4.  Assign to the query point the class that is most common (majority vote) among these $K$ nearest neighbors.

---
## Evaluation of a Classifier

* **Error rate (or misclassification rate):** Proportion of test samples misclassified. It's an intuitive measure but not always the most appropriate, especially if misclassification costs are unequal or classes are imbalanced.
* **Confusion Matrix:** A convenient way to summarize classifier performance, especially with few classes. For a two-class problem (Positive=1, Negative=0):

    |                        | Predicted Negative (0) | Predicted Positive (1) | Total Real |
    | :--------------------- | :--------------------: | :--------------------: | :---------: |
    | **Real Negative (0)** |        $T_N$         |        $F_P$         |   $N_N$   |
    | **Real Positive (1)** |        $F_N$         |        $T_P$         |   $N_P$   |
    | **Total Predicted** |     $\hat{N}_N$      |     $\hat{N}_P$      |     $N$     |

    * $T_P$: True Positives (correctly classified positives)
    * $T_N$: True Negatives (correctly classified negatives)
    * $F_P$: False Positives (negatives classified as positive - Type I error)
    * $F_N$: False Negatives (positives classified as negative - Type II error)
    * $N_P$: Total actual positives; $N_N$: Total actual negatives
    * $\hat{N}_P$: Total predicted positives; $\hat{N}_N$: Total predicted negatives

* **Balanced Error Rate (BER):** Useful for imbalanced classes. If $N_P=90, N_N=10$, a naive classifier always predicting positive has error rate $ER = (F_P+F_N)/N = 10/100 = 0.1$, which is misleadingly optimistic.
    $BER = \frac{1}{2} \left( \frac{F_P}{N_N} + \frac{F_N}{N_P} \right) = \frac{1}{2} \left( \frac{F_P}{F_P+T_N} + \frac{F_N}{F_N+T_P} \right)$
    It averages errors on each class.

* **Sensitivity (True Positive Rate, TPR, Recall):** Proportion of actual positives correctly identified.
    $SE = TPR = \frac{T_P}{T_P+F_N} = \frac{T_P}{N_P} = 1 - \frac{F_N}{N_P}$ ($0 \le SE \le 1$). Increases by reducing $F_N$.

* **Specificity (True Negative Rate, TNR):** Proportion of actual negatives correctly identified.
    $SP = TNR = \frac{T_N}{F_P+T_N} = \frac{T_N}{N_N} = 1 - \frac{F_P}{N_N}$ ($0 \le SP \le 1$). Increases by reducing $F_P$.
    There is a trade-off: a classifier always returning 0 has $SP=1$ but $SE=0$; one always returning 1 has $SE=1$ but $SP=0$.

* **False Positive Rate (FPR):**
    $FPR = 1 - SP = \frac{F_P}{F_P+T_N} = \frac{F_P}{N_N}$ ($0 \le FPR \le 1$). Decreases by reducing $F_P$.

* **False Negative Rate (FNR):**
    $FNR = 1 - SE = \frac{F_N}{T_P+F_N} = \frac{F_N}{N_P}$ ($0 \le FNR \le 1$). Decreases by reducing $F_N$.

* **Positive Predictive Value (PPV, Precision):** Proportion of positive classifications that are actually positive.
    $PPV = \frac{T_P}{T_P+F_P} = \frac{T_P}{\hat{N}_P}$ ($0 \le PPV \le 1$).

* **Negative Predictive Value (NPV):** Proportion of negative classifications that are actually negative.
    $NPV = \frac{T_N}{T_N+F_N} = \frac{T_N}{\hat{N}_N}$ ($0 \le NPV \le 1$).

* **False Discovery Rate (FDR):** Proportion of positive classifications that are false.
    $FDR = \frac{F_P}{T_P+F_P} = \frac{F_P}{\hat{N}_P} = 1 - PPV$ ($0 \le FDR \le 1$).

* **$F_1$ Score:** Harmonic mean of Precision (PPV) and Recall (TPR), balances both.
    $F_1 = \frac{2 \cdot TPR \cdot PPV}{TPR+PPV} = \frac{2T_P}{2T_P+F_P+F_N}$ ($0 \le F_1 \le 1$).

* Relationship: $FPR = \frac{p}{1-p} \frac{1-PPV}{PPV} (1-FNR)$, where $p = Prob\{y=1\}$.

---
## Receiver Operating Characteristic (ROC) Curve

* A plot of True Positive Rate (Sensitivity) vs. False Positive Rate (1 - Specificity) for different classification thresholds.
* It visualizes the trade-off between the probability of detection (TPR) and the probability of false alarm (FPR).
* Different points on the ROC curve correspond to different thresholds used by the classifier.
* A perfect ROC curve goes from (0,0) to (0,1) to (1,1).
* A random classifier produces an ROC curve along the main diagonal (bisector line) from (0,0) to (1,1). For such a classifier, $P(\hat{y}=1|y=1) = P(\hat{y}=1|y=0)$, meaning $T_P/N_P = F_P/N_N$.
* The **Area Under the ROC Curve (AUROC or AUC)** is a scalar measure summarizing the classifier's performance across all thresholds. A perfect classifier has AUC=1, a random one has AUC=0.5.
* **Example:** For non-separable classes $p(x|+) \sim \mathcal{N}(1,1)$ and $p(x|-) \sim \mathcal{N}(-1,1)$, with rule $\hat{y}=+$ if $x > THR$, else $\hat{y}=-$.
    * If $THR = -\infty$: all examples classified as positive. $F_P=N_N, T_P=N_P$. $SE=1, FPR=1$. Point (1,1) on ROC.
    * If $THR = +\infty$: all examples classified as negative. $F_N=N_P, T_N=N_N$. $SE=0, FPR=0$. Point (0,0) on ROC.

---
## Precision-Recall (PR) Curves

* Plots Precision ($P(y=1|\hat{y}=1)$) vs. Recall ($P(\hat{y}=1|y=1)$, same as Sensitivity/TPR).
* Precision is dependent on the prior probability of the positive class.
* PR curves are considered more informative than ROC curves in highly **unbalanced problems** (e.g., fraud detection, where positive cases are rare). Small changes in FPR (which might look negligible on an ROC curve) can correspond to large changes in the number of false positives and thus large changes in precision.
* **Fraud detection example:** $N_P=100$ frauds out of $N=2 \cdot 10^6$ transactions.
    * Algo1: 100 alerts, 90 relevant ($T_P=90, F_P=10$). $TPR=0.9, FPR \approx 5 \times 10^{-6}, Precision=0.9$.
    * Algo2: 1000 alerts, 90 relevant ($T_P=90, F_P=910$). $TPR=0.9, FPR \approx 4.55 \times 10^{-4}, Precision=0.09$.
    The TPR is identical (0.9). The FPR difference is small (both close to 0 on ROC scale). However, the precision difference (0.9 vs 0.09) is very large and more apparent on a PR curve, making Algo1 much preferable.

---
## Multi-class Problems ($K > 2$)

Strategies to extend binary classifiers to handle multi-class tasks $y \in \{c_1, ..., c_K\}$:

1.  **One-versus-the-Rest (OvR) / One-versus-All (OvA):**
    * **Training:** Train $K$ binary classifiers. Each classifier $k$ is trained to separate class $c_k$ from all other $K-1$ classes (the "rest").
    * **Classification:** For a new input, compute the outputs of all $K$ classifiers. If there is a single consistent output (e.g., only one classifier predicts its class positively), this is the returned class. Otherwise (e.g., multiple positive predictions or no positive prediction, depending on classifier output type), a tie-breaking rule is needed (e.g., choose the one with highest confidence, or select randomly).

2.  **Pairwise / One-versus-One (OvO):**
    * **Training:** Train a binary classifier for each pair of classes. This results in $K(K-1)/2$ independent binary classifiers.
    * **Classification:** For a new input, compute the outputs of all $K(K-1)/2$ classifiers. Each classifier "votes" for one of its two classes. The class that receives the majority of votes is returned. Ties are broken randomly or by some other rule.

3.  **Coding (e.g., Error-Correcting Output Codes - ECOC):**
    * The $K$ classes are coded by a binary vector (codeword) of size $d$. A common choice is $d = \lceil \log_2 K \rceil$.
    * **Training:** Train $d$ binary classifiers. Each classifier $j$ is designed to produce one bit (0 or 1) of the $d$-bit codeword.
    * **Classification:** The outputs of the $d$ classifiers form an output vector in $\{0,1\}^d$. The class whose predefined codeword has the smallest Hamming distance (number of disagreements) to this output vector is selected.
    * Example: $K=8$ classes. $d = \log_2 8 = 3$ binary classifiers are sufficient. Each class $c_i$ is assigned a unique 3-bit code (e.g., C1 -> 000, C2 -> 001, ..., C8 -> 111).

**Number of binary classifiers needed:**
* One-versus-the-rest: $K$
* Pairwise: $K(K-1)/2$
* Coding: $\lceil \log_2 K \rceil$ (minimum)

---
## Final Remarks and Real-World Challenges

Real learning tasks often present challenging configurations beyond simplified assumptions:
* Big data
* Unbalancedness of the classification task
* Heteroskedasticity (non-constant error variance)
* Mixed nature of variables (real, binary, categorical)
* Missing values
* Large number of irrelevant or redundant variables
* Skewed and long-tailed distributions
* Batch vs. online learning
* Nonstationarity, concept drift
* Existing domain knowledge in qualitative form
* Demand for interpretability

---
## The Pillars of Supervised Learning

1.  **Dependence:** A target $y$ is dependent on an input $x$ if $x$ brings information about $y$ (knowing $x$ reduces uncertainty of $y$). Model: $y = f(x) + w$ (where $w$ is noise).
2.  **Estimation:** Learners are estimators subject to the bias/variance trade-off. The most complex learner is not necessarily the one that generalizes best.
3.  **Causality vs. Dependency:** Causality implies dependency, but not vice-versa. "What if I observe" is different from "what if I intervene."

The **bias/variance trade-off** describes how a model's generalization error is affected by its complexity.
* **Underfitting (High Bias):** Model is too simple to capture underlying patterns.
* **Overfitting (High Variance):** Model learns noise in training data, performs poorly on unseen data.
Factors like noise, sample size ($N$), input dimension ($n$), non-linearity, and non-stationarity influence bias (B) and variance (V). Techniques like choosing model complexity, regularization, feature selection, and ensembling aim to manage this trade-off.