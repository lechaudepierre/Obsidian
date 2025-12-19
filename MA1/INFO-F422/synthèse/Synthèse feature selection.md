This document provides an overview of Feature Selection in machine learning.

## The Feature Selection Problem

Machine learning algorithms can suffer a degradation in accuracy when presented with many input variables (features) that are not necessary for the learning task. Some tasks, particularly in fields like bioinformatics (e.g., gene expression analysis), can involve thousands to tens of thousands of features. Original learning techniques were often not designed to cope with such a large number of irrelevant inputs.

**Feature selection** is the process of selecting a subset of the original features to be used as inputs for the learning algorithm. Using all available features might negatively affect the model's ability to generalize to new, unseen data due to the presence of irrelevant or redundant features. Feature selection can be viewed as a model selection problem, where the "model" includes the choice of which features to use.

---
## Benefits and Drawbacks of Feature Selection

**Potential Benefits:**
* Facilitating data visualization and improving human understanding of the data.
* Reducing measurement and storage requirements.
* Decreasing the training and utilization times of the final predictive model.
* Defying the "curse of dimensionality" to potentially improve the prediction performance of the model.

**Drawbacks:**
* It introduces an additional layer of complexity to the learning procedure, as the search in the hypothesis space is augmented by the dimension of finding the optimal subset of relevant features.
* It can add additional time to the overall learning process.

---
## The Curse of Dimensionality

The "curse of dimensionality" refers to various problems that arise when working with high-dimensional data (i.e., data with many features, $n$).

Consider an $n$-dimensional space and a query point $x_q \in \mathbb{R}^n$. If we want to define a local neighborhood around $x_q$ that contains a certain portion $V$ of the unit volume using a hypercube of edge length $d < 1$, the relationship is:
$d^n = V \implies d = V^{1/n}$
This means that to cover a fixed portion of the volume (e.g., $V=0.5$), the required edge length $d$ of the hypercube approaches 1 as the dimension $n$ increases. For instance, if $V=0.5$:
* For $n=2$, $d = (0.5)^{1/2} \approx 0.7$
* For $n=5$, $d = (0.5)^{1/5} \approx 0.87$
* For $n=50$, $d = (0.5)^{1/50} \approx 0.986$
This implies that to capture a constant fraction of the data, the neighborhood ceases to be "local" in high dimensions.

Conversely, if we keep the edge length $d$ (a measure of locality) constant, the volume of the cubic neighborhood $V=d^n$ decreases rapidly as $n$ increases. For example, if $d=1/2$:
* For $n=1$, $V = (1/2)^1 = 1/2$
* For $n=2$, $V = (1/2)^2 = 1/4$
* For $n=3$, $V = (1/2)^3 = 1/8$
This means that with a fixed degree of locality, we will find fewer and fewer data points in the neighborhood as the dimensionality increases.

**Issues arising from the curse of dimensionality:**
* As $n$ increases, the amount of local data effectively goes to zero, meaning all datasets become sparse in high dimensions.
* To preserve the same degree of locality (i.e., the same number of points in a neighborhood) when input dimension increases from $n_2$ to $n_1 > n_2$, we would need significantly more total data points ($N_1 \gg N_2$).
* In high dimensions, datasets are more likely to exhibit multicollinearity (features being correlated).
* The number of possible models (e.g., feature subsets) to consider increases super-exponentially with $n$.

---
## Methods of Feature Selection

Feature selection methods can be broadly categorized into three groups:

1.  **Filter Methods:**
    * These are preprocessing methods that assess the relevance of features independent of the learning algorithm that will ultimately be used.
    * Features are often ranked or scored based on certain statistical criteria.
    * Examples include ranking features by correlation or mutual information with the target variable, or using compression techniques like Principal Component Analysis (PCA) or clustering of features.
    * **Pros:** Easily scalable to very high-dimensional datasets, computationally simple and fast. Feature selection is performed only once, and then different classifiers can be evaluated.
    * **Cons:** They ignore the interaction with the specific learning algorithm. Often, they assess features univariately (one by one) or with low-variate interactions, thereby ignoring complex feature dependencies.

2.  **Wrapper Methods:**
    * These methods assess subsets of variables according to their usefulness to a given, specific predictor (learning algorithm).
    * The learning algorithm itself is used as part of the evaluation function to score different feature subsets.
    * The feature selection process "wraps around" the learning algorithm.
    * Examples include stepwise selection methods in linear regression (forward selection, backward elimination).
    * **Pros:** They take into account the learning algorithm's bias and can capture feature dependencies relevant to that algorithm.
    * **Cons:** They have a higher risk of overfitting to the training data (as they are tuning features to a specific learner on that data) and are very computationally intensive due to the repeated training of the learning algorithm.
    * **Limitation:** Due to the large number of comparisons, there's a high possibility that high correlations or low prediction errors are found by chance. A bad practice is to use the same set of observations to select the feature set and to assess the generalization accuracy.
    * **External Cross-Validation:** To mitigate this, external cross-validation is recommended. This involves:
        1.  Leaving out a single test fold of the dataset.
        2.  Using the remaining training folds for selecting both variables and the model.
        3.  Evaluating the generalization error (of both the chosen model and feature set) on the held-out test fold.
    * **Assessment by Permutation:** If no additional data is available, permutation testing can estimate how often random data would generate correlation or classification accuracy of the magnitude observed. This involves repeating the feature selection and modeling procedure several times with data where the dependency between input and output is artificially removed (e.g., by permuting the target variable). This provides a baseline distribution of accuracy under randomness.

3.  **Embedded Methods:**
    * In these methods, the feature selection process is an integral part of the learning procedure itself.
    * The algorithm intrinsically performs feature selection during its training phase.
    * Examples: Classification trees (which select features at each split), Random Forests (which rank feature importance), methods based on regularization (like Lasso regression which can shrink some feature coefficients to zero), and representation learning techniques (like autoencoders, though these are more about feature transformation/extraction).
    * **Pros:** They are generally less computationally intensive than wrapper methods while still considering the interaction with the learning algorithm.
    * **Cons:** The feature selection is specific to the learning machine being used; the selected subset might not be optimal for a different type of learning algorithm.

---
## Specific Filter Method Examples

### Principal Component Analysis (PCA)
PCA is a popular method for linear dimensionality reduction. It operates in an unsupervised manner, meaning it doesn't use the target variable information.
* It projects the data from the original $n$-dimensional space into a lower $h$-dimensional space ($h<n$).
* The new axes in this lower-dimensional space are called **principal components (PCs)**. Each PC is a linear combination of the original features.
* **PC1** is the axis (direction) along which the projected observations have the greatest variance. If $x$ is an original data vector and $a$ is a direction vector, the first principal component score is $z = a^T x$. PC1 is found by choosing $a = [a_1, ..., a_n]^T \in \mathbb{R}^n$ such that $z$ has the largest variance. This optimal $a$ is the eigenvector of the covariance matrix of $X$ corresponding to its largest eigenvalue, $\lambda_1$.
* **PC2** is the axis orthogonal to PC1 that has the next greatest variation of the projected observations, and so forth.

**PCA Algorithm:**
1.  Input: Data matrix $X_{[N,n]}$ (N observations, n features). Normalize $X$ into $\tilde{X}$ such that each column has mean 0 and variance 1.
2.  Perform Singular Value Decomposition (SVD) on $\tilde{X}$: $\tilde{X} = UDV^T$, where $U_{[N,N]}$ and $V_{[n,n]}$ are orthogonal matrices, and $D_{[N,n]}$ is a rectangular diagonal matrix with singular values $d_1 \ge d_2 \ge \dots \ge d_n$ on its diagonal.
3.  The transformed data (principal component scores) are $Z = \tilde{X}V = UD$. Each column of $Z_{[N,n]}$ is a PC, and their importance (variance explained) diminishes from left to right.
4.  The first $h < n$ columns of $Z$ (also called eigen-features) are chosen to represent the dataset in a lower dimension.

**How many PCs to choose?**
* Fix a threshold (e.g., $\alpha = 0.95$) on the proportion of variance to be explained: choose $h$ PCs such that $\frac{\sum_{j=1}^{h}\lambda_{j}}{\sum_{j=1}^{n}\lambda_{j}}\ge\alpha$, where $\lambda_j = \frac{d_j^2}{N-1}$ is the $j$-th largest eigenvalue (variance of the $j$-th PC).
* Use a **Scree Plot**: Plot the eigenvalues $\lambda_j$ in decreasing order. Choose $h$ at the "knee" or "elbow" of the curve, where adding more PCs gives diminishing returns in variance explained.
* Select $h$ by cross-validation to optimize the performance of a supervised learning model.

### Clustering
Clustering is an unsupervised learning technique that can be used to group features (or observations) that exhibit similar patterns (e.g., patterns of gene expressions in microarray data). It requires a distance function between variables and between clusters.
* **Nearest neighbor clustering:** The number of clusters is often fixed in advance, and then each variable is assigned to a cluster. Examples include Self Organizing Maps (SOM) and K-means.
* **Agglomerative clustering:** These are bottom-up methods where clusters start as individual variables (or empty), and variables are successively added or merged. An example is **hierarchical clustering**.
    * **Hierarchical Clustering:** Begins by considering all observations (or features) as separate clusters. It then iteratively merges the two samples/clusters that are nearest to each other. In subsequent stages, clusters can also be merged. This requires:
        * An appropriate metric (a measure of distance between pairs of observations).
        * A linkage criterion, which specifies the dissimilarity of sets (clusters) as a function of the pairwise distances of observations in those sets (e.g., complete linkage, average linkage).
    * The output is typically visualized as a **dendrogram**, a tree diagram illustrating the arrangement and merging of clusters.

### Ranking Methods
Ranking methods assess the importance or relevance of each individual variable with respect to the output variable using a univariate measure. These are supervised techniques and typically have a computational complexity of $O(n)$.
* **Measures of relevance:**
    * **Pearson correlation coefficient:** (The greater its absolute value, the more relevant). This assumes a linear relationship.
    * **Significance p-value of a hypothesis test:** (The lower the p-value, the more relevant). Used in binary classification to detect features that split the dataset well. Parametric (e.g., t-test) and nonparametric (e.g., Wilcoxon rank-sum test) tests can be used.
    * **Mutual Information:** (The greater, the more relevant). This can capture non-linear relationships.
* After the univariate assessment, the method ranks the variables in decreasing order of relevance.
* **Pros:** Fast (complexity $O(n)$), intuitive output, and easy to understand.
* **Cons:** They disregard redundancies and higher-order interactions between variables (e.g., between genes). The set of the best $k$ individual features does not necessarily constitute the best $k$-variate feature vector for prediction.

---
## Wrapper Search Strategies

Wrapper methods search for an optimal subset of features $w^*$ within the space $W = \{0,1\}^n$ (where $w[j]=1$ if feature $j$ is selected, $0$ otherwise). The goal is to find $w^*$ that minimizes the generalization error $MiSE_w$ of a model built using that feature subset.
The number of possible feature subsets is $2^n$, making an exhaustive search impossible for even moderately large $n$.

**Greedy Search Methods** (evaluate $O(n^2)$ subsets):
* **Forward Selection:**
    1.  Starts with an empty set of features.
    2.  In the first step, selects the single feature that results in the lowest generalization error when used alone.
    3.  In each subsequent step, adds the feature (from those not yet selected) that, when combined with the currently selected features, yields the lowest generalization error.
    4.  The process continues until adding more features does not improve the error significantly or a predefined number of features is selected.
* **Backward Selection (or Elimination):**
    1.  Starts with the full set of all features.
    2.  In each step, removes the feature whose absence causes the smallest increase (or largest decrease) in generalization error.
    3.  The process continues until removing more features significantly degrades performance or a desired number of features remains.
* **Stepwise Selection:**
    * Combines forward selection and backward elimination. At each step, it tests for the addition of variables not in the current set and the removal of features currently in the set, choosing the action that most improves the model.

---
## Combining Feature Selection Methods Instead of Selecting One

Given that there is no single optimal feature selection technique and that multiple subsets of features might discriminate the data equally well, ensemble approaches can be used.
* Different feature selection methods can be combined.
* Ensemble techniques might involve averaging predictions over models built with multiple feature subsets.
* This can improve the robustness and stability of the final discriminative methods.
* However, using ensemble approaches for feature selection requires additional computational resources.

---
## Shrinkage Methods (Often Embedded)

Shrinkage methods aim to improve an estimator by adding constraints to its parameters, typically to reduce variance.

### Ridge Regression
Ridge regression is a shrinkage method applied to least squares regression.
* The objective is to find coefficients $\beta_r$ that minimize:
    $\hat{\beta}_{r} = \arg\min_{b} \left\{ \sum_{i=1}^{N}(y_{i}-{x_{i}}^{T}b)^{2} + \lambda \sum_{j=1}^{p}b_{j}^{2} \right\} = \arg\min_{b} \left( (Y-Xb)^{T}(Y-Xb) + \lambda b^{T}b \right)$
    where $\lambda > 0$ is a complexity parameter that controls the amount of shrinkage (L2 penalty). The larger the value of $\lambda$, the stronger the constraint and the more the coefficients are shrunk towards zero.
* An equivalent way to write the ridge problem is:
    $\hat{\beta}_{r} = \arg\min_{b} \sum_{i=1}^{N}(y_{i}-{x_{i}}^{T}b)^{2}$, subject to $\sum_{j=1}^{p}b_{j}^{2} \le L$
    There is a one-to-one correspondence between $\lambda$ and $L$.
* The ridge regression solution is:
    $\hat{\beta}_{r} = (X^{T}X + \lambda I_{p})^{-1}X^{T}Y$
    where $I_p$ is a $[p,p]$ identity matrix. Ridge regression shrinks coefficients but typically does not set them exactly to zero, so it doesn't perform explicit feature selection in the sense of discarding features.

### Lasso (Least Absolute Shrinkage and Selection Operator)
The Lasso estimate of linear parameters is returned by:
* $\hat{\beta}_{L} = \arg\min_{b} \sum_{i=1}^{N}(y_{i}-x_{i}^{T}b)^{2}$, subject to $\sum_{j=1}^{p}|b_{j}| \le L$
    This uses an L1 penalty.
* The L1-norm penalty makes the solution nonlinear in $y_i$ and typically requires a quadratic programming algorithm.
* If $L > \sum_{j=1}^{p}|\hat{\beta}_{j, OLS}|$, where $\hat{\beta}_{j, OLS}$ are the ordinary least-squares coefficients, the Lasso returns the OLS solution.
* A key property of Lasso is that it tends to return **sparser solutions** than ridge regression, meaning it forces more coefficients to be exactly zero. This makes Lasso an embedded feature selection method.
* The penalty factor $L$ (or an equivalent $\lambda$) is typically chosen using cross-validation strategies.

**Ridge Regression vs. Lasso (Geometric Interpretation):**
The difference in how they shrink coefficients can be visualized by their constraint regions. The Lasso constraint region ($\sum |b_j| \le L$) is a hyperdiamond (e.g., a square rotated by 45 degrees in 2D), which has "corners" along the axes. The elliptical contours of the Residual Sum of Squares (RSS) are more likely to intersect these corners, leading to solutions where some coefficients are zero. The Ridge constraint region ($\sum b_j^2 \le L$) is a hypersphere (a circle in 2D), which is smooth, making it less likely for coefficients to be exactly zero.

---
## Information Theory Concepts for Feature Selection

These concepts are often used in filter-based feature selection methods, particularly for ranking or assessing relevance.

### Entropy (Continuous Case)
For a continuous random variable $y$ with probability density function $p(y)$, its (differential) entropy is:
$H(y) = -\int \log(p(y))p(y)dy = E_{y}[-\log(p(y))]$
(with the convention $0 \log 0 = 0$).
* Entropy is a functional of the distribution of $y$.
* It measures the predictability (or uncertainty) of $y$. The higher the entropy, the less reliable (more uncertain) are predictions about $y$.
* For a scalar normal random variable $y \sim \mathcal{N}(0, \sigma^2)$, the entropy is $H(y) = \frac{1}{2}(1 + \ln(2\pi\sigma^2)) = \frac{1}{2}(\ln(2\pi e \sigma^2))$.
* For continuous random variables, differential entropy may be negative (due to the logarithmic scale).

### Conditional Entropy
For two continuous random variables $x$ and $y$ with joint density $p(x,y)$ and conditional density $p(y|x)$:
$H(y|x) = -\iint \log(p(y|x))p(x,y)dxdy = E_{x,y}[-\log(p(y|x))] = E_{x}[H(y|x)]$
* This quantifies the remaining uncertainty of $y$ once $x$ is known.
* Properties:
    * In general, $H(y|x) \neq H(x|y)$.
    * Conditioning reduces entropy: $H(y|x) \le H(y)$, with equality if $x$ and $y$ are independent ($x \perp y$).
    * $H(y) - H(y|x) = H(x) - H(x|y)$.

### Mutual Information (MI)
For two random variables $x$ and $y$:
$I(x;y) = \iint \log\frac{p(x,y)}{p(x)p(y)}p(x,y)dxdy$
It can also be expressed as:
$I(x;y) = H(y) - H(y|x) = H(x) - H(x|y)$
(with the convention $0 \log \frac{0}{0} = 0$).
* MI is a widely used measure to define the dependency between variables.
* It measures the amount of information that one random variable contains about another.
* It can also be considered as the distance from independence between the two variables. If $x$ and $y$ are independent, $p(x,y) = p(x)p(y)$, then $I(x;y) = 0$.
* MI is always non-negative ($I(x;y) \ge 0$) and is zero if and only if the two variables are stochastically independent.
* For a normally distributed random vector $(x, y)$ with correlation coefficient $\rho$, their mutual information is $I(x;y) = -\frac{1}{2}\log(1-\rho^2)$. Equivalently, $\rho = \sqrt{1 - \exp(-2I(x;y))}$. Note that $\rho=0$ when $I(x;y)=0$.

### Conditional Mutual Information
For three random variables $x, y, z$:
$I(y;x|z) = H(y|z) - H(y|x,z)$
* This is null if and only if $x$ and $y$ are conditionally independent given $z$.
* **Chain Rule for Mutual Information:** For a set of $n$ variables $X = \{x_1, ..., x_n\}$ and a target $y$:
    $I(X;y) = I(X_{-i};y|x_i) + I(x_i;y) = I(x_i;y|X_{-i}) + I(X_{-i};y)$, for $i=1, ..., n$.
    For $n=2$, this means:
    $I(\{x_1, x_2\};y) = I(x_2;y|x_1) + I(x_1;y) = I(x_1;y|x_2) + I(x_2;y)$

### Notions of Relevance using Mutual Information
Let $X$ be a set of $n$ input variables and $y$ be a target variable. $X_{-j}$ denotes the set $X$ excluding variable $x_j$.
* **Strong Relevance:** A variable $x_j$ is strongly relevant to the target $y$ if $I(X_{-j};y) < I(X;y)$. This means $x_j$ carries information about $y$ that no other variable (or combination of other variables) carries. Strongly relevant features are always necessary for an optimal subset.
* **Weak Relevance:** A variable $x_j$ is weakly relevant to $y$ if it is not strongly relevant AND there exists some subset of other features $S \subseteq X_{-j}$ such that $I(S;y) < I(\{x_j, S\};y)$. This means $x_j$ carries information about the target in a certain context $S$ (i.e., when combined with features in $S$). Weakly relevant features are not always necessary but may become necessary under certain conditions.
* **Irrelevance:** A feature is irrelevant if it is neither strongly nor weakly relevant. It is not necessary at all.

**Example:** Consider $n=4$ features, with $x_3 = -x_2$, and $y = \begin{cases} 1+w, & \text{if } x_1+x_2 > 0 \\ 0, & \text{else} \end{cases}$ (where $w$ is noise).
Here, $x_1$ and $x_2$ are likely strongly or weakly relevant. $x_3$ is perfectly redundant with $x_2$. $x_4$ (if not involved in $y$'s definition) would be irrelevant.

### Markov Blanket
For a set of $n$ random variables $X$, a target variable $y$, and a subset $M_y \subset X$, the subset $M_y$ is said to be a Markov blanket of $y$ (where $y \notin M_y$) if $y$ is conditionally independent of all other variables $X_{-(M_y)}$ given $M_y$:
$I(y; X_{-(M_y)} | M_y) = 0$
The **Theorem (Total conditioning)** states that a variable $x$ is in the Markov blanket of $y$ if and only if $x$ provides information about $y$ even when all other variables (excluding $x$ and $y$) are known:
$x \in M_y \iff I(x;y | X_{-(x,y)}) > 0$
The Markov blanket of $y$ is the minimal set of features that renders $y$ independent of all other features.

### Variable Relevance (Context-Dependent)
The relevance of a variable $x_2$ to a target $y$, given another variable $x_1$ (the context), is the conditional mutual information:
$I(x_2;y|x_1) = I(\{x_1, x_2\};y) - I(x_1;y)$
For an empty context, the relevance is simply the mutual information $I(x_2;y)$.

### Interaction between Variables
From the chain rule, $I(\{x_1, x_2\};y) = I(x_2;y|x_1) + I(x_1;y)$. We can also write:
$I(\{x_1, x_2\};y) = I(x_1;y) + I(x_2;y) - [I(x_1;y) - I(x_1;y|x_2)]$
The term $[I(x_1;y) - I(x_1;y|x_2)]$ (or equivalently $[I(x_2;y) - I(x_2;y|x_1)]$) represents the **interaction** between $x_1$ and $x_2$ in predicting $y$.
* **Negative Interaction (Synergy/Complementarity):** The variables together have more information than the sum of their individual (univariate) information content. This occurs in situations like XOR, where $I(x_1;y) \approx 0$ and $I(x_2;y) \approx 0$, but $I(x_1;y|x_2) > 0$ (or $I(\{x_1,x_2\};y)$ is high).
* **Positive Interaction (Redundancy):** The variables provide overlapping information. For example, if $I(x_1;y) > 0$ and $I(x_2;y) > 0$, but $I(x_1;y|x_2) \approx 0$, it means that once $x_2$ is known, $x_1$ adds little new information about $y$.

### Feature Selection using Mutual Information
Given an output target $y$ and a set of input variables $X = \{x_1, ..., x_n\}$, a goal of feature selection can be to find the subset $X_S \subset X$ of a desired size $d$ (i.e., $|X_S|=d$) that maximizes the mutual information with the target:
$X^* = \arg\max_{X_S \subset X, |X_S|=d} I(X_S;y)$
This maximization can be done in a forward (greedy) manner. Let $X_S$ be the current set of selected variables. The task of adding a new variable $x^*$ from the remaining variables $X - X_S$ can be addressed by solving:
$x^* = \arg\max_{x_k \in X-X_S} I(\{X_S, x_k\};y)$
This requires multivariate estimation of mutual information, which can be challenging. Filter approaches often rely on some low-variate approximation.

### The mRMR (minimum-Redundancy Maximum-Relevancy) Approach
The mRMR feature selection strategy is a greedy method that approximates the step of selecting the next best feature $x_k$ by trying to satisfy two criteria simultaneously:
1.  Maximize relevancy: the feature $x_k$ should have high mutual information with the target $y$, $I(x_k;y)$.
2.  Minimize redundancy: the feature $x_k$ should have low mutual information with the already selected features in $X_S$.
The mRMR algorithm selects the next feature $x^*_{mRMR}$ by maximizing:
$x^*_{mRMR} = \arg\max_{x_k \in X-X_S} \left[ I(x_k;y) - \frac{1}{|X_S|} \sum_{x_j \in X_S} I(x_j;x_k) \right]$
where $|X_S|$ is the number of features already in the selected set $X_S$.