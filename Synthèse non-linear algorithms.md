This document provides a comprehensive overview of nonlinear regression algorithms, detailing their theoretical underpinnings, learning methodologies, and practical considerations.

---
## Introduction to Nonlinear Modeling

Nonlinear regression algorithms are essential when the relationship between input variables and the output variable cannot be adequately captured by a linear model. A wide array of such algorithms exists, each relying on a distinct set of assumptions, known as an **inductive bias**, about the nature of the target function. These algorithms invariably come with a set of **(hyper)parameters** that act like knobs, allowing practitioners to control the fundamental trade-off between **bias** (underfitting) and **variance** (overfitting) to achieve optimal model performance.

---
## Global vs. Local Approaches

Two main philosophies guide the development of nonlinear models: global and local (or divide-and-conquer) approaches.

### Global Approaches
Global methods aim to define the input-output relationship using a single analytical function that spans the entire input domain. Learning, in this context, is treated as **function estimation**: given a dataset, the goal is to derive a hypothesis that best approximates the overall data distribution.
* **Examples:** Linear models, nonlinear statistical regressions, and Artificial Neural Networks (when trained to model the entire dataset with a single complex function).
* **Why choose this?** When there's a belief that a single, coherent underlying process governs all the data.

### Local and Divide-and-Conquer Approaches
These methods decompose a complex modeling problem into several simpler ones. The solutions to these simpler sub-problems are then combined to yield the final solution.
* **Advantages:**
    * Simpler sub-problems can often be tackled effectively with simpler, well-understood estimation techniques (e.g., linear methods), which have been well studied and developed over the years.
    * Learning can be more adaptive to local properties of the data, such as heteroskedasticity (non-constant variance of errors).
* **Why choose this?** When the underlying data-generating process is believed to vary across different regions of the input space, or when the overall problem is too complex for a single global model.

Two main paradigms within divide-and-conquer are modular modeling and local modeling.

---
## Modular Modeling
In modular modeling, different "modules" or models are responsible for different parts or **operating regimes** of the input space. This approach involves **nonlinear structural identification** and parametric identification based on the whole dataset.
* **Examples:** Radial Basis Functions (RBFs), Local Model Networks, Classification and Regression Trees.
* **Why choose this?** Useful when distinct, identifiable regions in the input space have different characteristic behaviors. For instance, a chemical process might have different dynamics at different temperature and pressure regimes.

---
## Local Modeling
Local modeling approaches function estimation as **value estimation**. Instead of seeking a complete description of the input-output mapping across the entire domain, these methods approximate the function only in a **neighborhood** of the specific point for which a prediction is desired. Linear techniques are often adopted for both parametric and structural identification within these local regions.
* **Examples:** KNN, lazy learning, local learning, locally weighted regression.
* **Why choose this?** Effective when the function is highly irregular or when a global model is hard to define, but local behavior is simpler. Predictions are made on-demand, adapting to the local data structure around the query point.

---
## Artificial Neural Networks (ANNs) 🧠

ANNs are computational models inspired by the parallel, distributed information processing of the brain. The most common type is the **feed-forward network (FNN)**, also known as the **multi-layer perceptron (MLP)**. Modern interpretations of ANNs lean more towards statistical reasoning for function approximation and generalization, rather than strict biological mimicry. The development of ANNs has seen three historical waves: **cybernetics** (1940s-1960s), **connectionism** (1980s-1995), and the current wave of **deep learning** (starting around 2006).

### Feed-forward Neural Network (FNN) Architecture
* **Neurons:** Simple processing units organized in layers.
* **Layers:** All FNNs have an input layer and an output layer; they may have one or more hidden layers between them. For simplicity, FNNs with $n$ inputs and 1 output are often considered.
* **Weights:** Nodes are connected by real-valued weights (model parameters).
* **Connectivity:** Connections form an acyclic graph (no closed loops are admitted).
* **Bias Unit:** Plays a role similar to the intercept in linear models, providing an affine transformation rather than just a linear one. (Note: This "bias" is distinct from the statistical bias of an estimator!)

The output of the $v$-th hidden unit in the $l$-th layer ($z_{v}^{(l)}$) is given by:
$z_{v}^{(l)} = g^{(l)}(a_{v}^{(l)})$
where $a_{v}^{(l)}$ is the weighted sum of outputs from the previous layer (layer $l-1$):
$a_{v}^{(l)} = \sum_{k=0}^{H^{(l-1)}} w_{kv}^{(l)} z_{k}^{(l-1)}$
Here, $w_{kv}^{(l)}$ is the weight of the edge from the $k$-th node in layer $l-1$ to the $v$-th node in layer $l$, $z_{k}^{(l-1)}$ is the output of the $k$-th node in layer $l-1$ (with $z_0^{(l)}$ typically being a bias unit for layer $l$), $H^{(l-1)}$ is the number of units in layer $l-1$, and $g^{(l)}(\cdot)$ is the activation function for layer $l$.

### Activation Functions
Activation functions introduce nonlinearity into the network, enabling it to learn complex patterns. Without them, a multi-layer network would behave like a single-layer linear model.
* **Logistic (Sigmoid) Function:** A smooth, S-shaped function that squashes its input into the range (0, 1).
    $g(a) = \frac{1}{1+e^{-a}}$
    It's a smoothed version of the threshold firing behavior of biological neurons.
* **Other Common Activation Functions**:
    * **Hyperbolic Tangent (Tanh):** $g(a) = \tanh(a) = \frac{e^{a}-e^{-a}}{e^{a}+e^{-a}}$. Similar to sigmoid but output ranges from -1 to 1. Its derivative is $g'(a) = 1 - g(a)^2$.
    * **Rectified Linear Unit (ReLU):** $g(a) = \max(0, a)$. Widely used, especially in deep networks, due to its simplicity and ability to mitigate vanishing gradients.
    * **Identity:** $g(a) = a$. Used in the output layer for regression tasks.
    * **Sign:** $g(a) = \text{sign}(a)$. Used in older perceptron models.
    * **Hard Tanh:** A bounded linear function.

Historically, Minsky and Papert showed in 1969 that a single-layer perceptron (using a step function) cannot represent the XOR logical function, highlighting the need for multi-layer architectures with nonlinear activations for more complex problems. Two-layer networks can represent XOR.

### Single-Hidden-Layer FNN
For a network with $L=2$ (one input layer, one hidden layer, one output layer), the input-output relationship is:
$\hat{y} = g^{(2)}(a_{1}^{(2)}) = g^{(2)}\left(\sum_{k=0}^{H} w_{k1}^{(2)} z_{k}\right)$
where $z_{k} = g^{(1)}\left(\sum_{j=0}^{n} w_{jk}^{(1)} x_{j}\right)$ are the outputs of the $H$ hidden units (and $z_0$ is a bias).
If both $g^{(1)}(\cdot)$ and $g^{(2)}(\cdot)$ were linear mappings, this functional would be linear.
Learning involves finding:
1.  **Parameters:** The values of weights $w^{(l)}$ for $l=1,2$.
2.  **Structural Hyperparameters:** The number of layers $L$ (fixed at 2 here) and the number of hidden nodes $H$.

### Parametric Identification: Backpropagation
For a fixed number of hidden nodes $H$, backpropagation is a gradient-based algorithm used to estimate the optimal weights $W = \{w_{kv}^{(l)}\}$ by minimizing a cost function, typically the Mean Squared Error (MSE) or $\hat{MiSE}_{emp}(W)$:
$\hat{MiSE}_{emp}(W) = \frac{\sum_{i=1}^{N}(y_{i}-\hat{y}_{i})^{2}}{N} = \frac{\sum_{i=1}^{N}(y_{i}-\hat{y}(x_{i},W))^{2}}{N}$
The goal is to find $W^{*} = \arg\min_{W} \overline{MiSE}_{emp}(W)$. Backpropagation cleverly exploits the network's layered structure to recursively compute the gradients of the cost function with respect to each weight using the chain rule of derivatives.

* **Regularized Parametric Identification:** To prevent overfitting and reduce variance by reducing the search space, a regularization term $J(W)$ is often added to the cost function:
    $W^{*} = \arg\min_{W} (\overline{MiSE}_{emp}(W) + \lambda J(W))$
    where $\lambda \ge 0$ is a tuning parameter. A common choice is the quadratic (L2) regularization, also known as **weight decay**:
    $J(W) = \sum_{w \in W} w^2$
* **Gradient Descent:** The simplest (and least effective) form of backpropagation is iterative gradient descent:
    $W(\tau+1) = W(\tau) - \eta \frac{\partial\overline{MiSE}_{emp}(W(\tau))}{\partial W(\tau)}$
    where $W(\tau)$ is the weight vector at iteration $\tau$, and $\eta \in [0,1)$ is the learning rate. Weights are typically initialized with small random values and changed to reduce error. This method can be inefficient due to slow convergence and no guaranteed monotonic decrease of the error. More advanced optimizers like Levenberg-Marquardt are often more effective.

**Backpropagation Example (Simplified for $n=1$, one hidden layer, two hidden nodes, no bias units, activation $g(\cdot)$)**:
Output: $\hat{y}(x) = g(w_{11}^{(2)}g(w_{11}^{(1)}x) + w_{21}^{(2)}g(w_{12}^{(1)}x))$
Weights $W = [w_{11}^{(1)}, w_{12}^{(1)}, w_{11}^{(2)}, w_{21}^{(2)}]$.
The derivative of $\overline{MISE}_{emp}$ w.r.t. any weight $w$ is:
$\frac{\partial\overline{MISE}_{emp}}{\partial w} = -2/N \sum_{i=1}^{N}((y_{i}-\hat{y}(x_{i},W))\frac{\partial\hat{y}(x_{i})}{\partial w})$
Let $a_v^{(1)}$ be the net input to hidden node $v$, and $a_1^{(2)}$ be the net input to the output node. $z_v$ is the output of hidden node $v$.
* **Hidden-to-Output Layer weights** (e.g., $w_{v1}^{(2)}$ for $v=1,2$):
    Since $a_1^{(2)}(x) = w_{11}^{(2)}z_1 + w_{21}^{(2)}z_2$:
    $\frac{\partial\hat{y}(x)}{\partial w_{v1}^{(2)}} = \frac{\partial g}{\partial a_{1}^{(2)}} \frac{\partial a_{1}^{(2)}}{\partial w_{v1}^{(2)}} = g'(a_{1}^{(2)}(x))z_{v}(x)$
* **Input-to-Hidden Layer weights** (e.g., $w_{1v}^{(1)}$ for $v=1,2$):
    Since $a_v^{(1)} = w_{1v}^{(1)}x$:
    $\frac{\partial\hat{y}(x)}{\partial w_{1v}^{(1)}} = \frac{\partial g}{\partial a_{1}^{(2)}} \frac{\partial a_{1}^{(2)}}{\partial z_{v}} \frac{\partial z_{v}}{\partial a_{v}^{(1)}} \frac{\partial a_{v}^{(1)}}{\partial w_{1v}^{(1)}} = g'(a_{1}^{(2)}(x)) w_{v1}^{(2)} g'(a_{v}^{(1)}(x)) x$
The term $g'(a_{1}^{(2)}(x))$ is computed once and reused for the upper layer's weights, illustrating the efficiency of computing gradients in reverse order (from output to input) after a forward pass. For sigmoid activation $g$, $g'(a) = \frac{e^{-a}}{(1+e^{-a})^2}$.

### Automatic Differentiation
Modern deep learning frameworks heavily rely on **automatic differentiation (autodiff)** to compute these complex gradients. Autodiff views any computer program as a sequence of elementary arithmetical operations. By applying the chain rule systematically, it can compute derivatives automatically.
Consider $y = f(g(h(x)))$. If we let $w_1 = h(x)$, $w_2 = g(w_1)$, $w_3 = f(w_2) = y$, then by chain rule:
$\frac{\partial y}{\partial x} = \frac{\partial y}{\partial w_2} \frac{\partial w_2}{\partial w_1} \frac{\partial w_1}{\partial x}$
* **Forward Accumulation:** Computes derivatives by propagating them forward through the computation graph. A separate pass is needed for each independent variable. For $y = f(x_1, x_2) = \sin(x_1) + x_1 x_2$, we compute $\dot{w}_i = \frac{\partial w_i}{\partial x}$ by summing contributions $\frac{\partial w_i}{\partial w_j} \dot{w}_j$ from predecessors $w_j$.
* **Backward Accumulation (Reverse Mode):** Computes derivatives by propagating them backward from the output. This is generally more efficient when there are many inputs and few outputs (like in training NNs, where the output is a scalar loss function), requiring only one pass to get all gradients w.r.t. inputs/parameters. For $y = f(x_1, x_2)$, we compute $\bar{w}_i = \frac{\partial y}{\partial w_i}$ by summing contributions $\bar{w}_j \frac{\partial w_j}{\partial w_i}$ from successors $w_j$. Backpropagation is a special case of reverse mode autodiff.

### Universal Approximation Theorem
A remarkable theoretical result states that a two-layer FNN with sigmoidal hidden units (or other suitable non-polynomial activation functions) is a **universal approximator**. It can approximate arbitrarily well any functional (one-one or many-one) continuous mapping, provided the number of hidden units $H$ is sufficiently large. While powerful, this theorem is of no practical use for choosing $H$ for a finite dataset $N$ or a generic nonlinear mapping.

---
## Deep Architectures (Deep Learning - DL) 🚀

Until around 2006, FNNs with more than two layers (deep networks) were rarely used in literature due to difficulties in training, leading to poor training performance and large generalization errors. These issues stemmed from problems like **local minima**, **plateaus** in the loss landscape, and the **vanishing gradient problem** (where gradients become extremely small in lower layers during backpropagation, hindering learning in those layers). Solutions obtained with deeper neural networks starting from random initialization often performed worse than those for networks with 1 or 2 hidden layers.

A resurgence occurred from 2006 onwards, thanks to work from several academic and private labs.

### Reasons for the Neural Network Renaissance
* **Massive datasets availability**.
* **GPU availability** for parallel processing.
* **Automatic differentiation libraries** (e.g., TensorFlow, PyTorch).
* **Pre-training techniques:** Unsupervised learning to initialize each layer, one after another.
* **New activation functions** (e.g., ReLU).
* **Regularization principles** (e.g., dropout).
* Shift from **feature engineering to representation learning**.
* **Transfer learning**.
* **New architectures:** Autoencoders, convolutional networks, transformers.

### Deep Learning Success Stories
* **Image Recognition:** A convolutional network won the ImageNet Large-Scale Visual Recognition Competition in 2012, significantly reducing the error rate from 26.1% to 15.3%.
* **Speech Recognition:** Error rates dropped by up to half, leading to deployment in systems like Android phones.
* **Autonomous Vehicles:** Used for tasks like traffic sign recognition and pedestrian detection.
* **Other Areas:** Image segmentation, face detection, analysis of particle accelerator data in physics, prediction of mutation effects in bioinformatics, machine translation, chatbots, and generative AI.
Yann LeCun, Geoffrey Hinton, and Yoshua Bengio received the ACM Turing Award in 2019 for their foundational work in deep learning.

### Characteristics of Deep Architectures
* **Hierarchical Decomposition:** DL decomposes complex mappings (e.g., image to class label) into a series of nested simpler mappings, each handled by a different layer of the model. This is analogous to a multi-step computer program.
* **Representation Learning:** They learn a nested hierarchy of concepts and representations; for example, in images, from pixels to edges, then motifs, parts of objects, and finally the class of the object. This moves away from manually engineered features towards learning representations.
* **Transfer Learning and Reuse:** Networks pre-trained on vast datasets (e.g., images) can be adapted for new tasks, leveraging the learned representations in a "greedy" approach.

### Autoencoders
Autoencoders are a type of ANN used for **unsupervised learning**, essentially performing a nonlinear version of Principal Component Analysis (PCA).
* **Architecture:** A multi-input, multi-output neural network that aims to map its input to itself. It consists of an **encoder** function that maps the input to a hidden layer (often of lower dimensionality, the "encoding") and a **decoder** function that reconstructs the input from this encoding.
* **Training:** Trained by minimizing the **reconstruction error** between the input and the output.
* **Applications:** Dimensionality reduction, data compression, and feature learning for pre-training deep networks.

### Learning Deep Architectures
A common strategy, especially in the early days of the DL resurgence, was **greedy layer-wise unsupervised pre-training**:
1.  Initialize the parameters of the lower layer using an unsupervised learning algorithm (e.g., an autoencoder).
2.  Use the output of this first layer (a new representation for the raw input) as input for another layer, and initialize that layer similarly.
3.  Repeat for subsequent layers.
4.  **Fine-tuning:** After pre-training all layers, the entire FNN is fine-tuned using a supervised algorithm (like backpropagation) with the target labels.
Unsupervised pre-training can be seen as a form of variance reduction by constraining the solution to a more favorable region in the parameter space.

### Convolutional Networks (CNNs or ConvNets) 🖼️
CNNs are biologically inspired architectures that mimic the processing in the visual cortex of animals. They are particularly powerful for data with a grid-like topology, such as images, but also text and sound processing. They exploit local and spatial correlation in the data.

Key components of CNNs include:
* **Convolution Layers:** Apply a set of learnable **filters** (kernels) to the input. Each filter slides across the input, computing dot products to create **feature maps**. A crucial aspect is **shared weights**: the same filter (and thus the same set of weights) is applied across different spatial locations in the input. This ensures **translation invariance** (the model can detect a feature regardless of its position) because the weights depend on spatial separation, not on absolute positions.
    * _Example:_ In an image, one filter might learn to detect horizontal edges, another vertical edges, etc.
* **Pooling Layers (Subsampling):** Reduce the spatial dimensions of the feature maps, thereby reducing the number of parameters and computation in the network, and also providing a degree of translation invariance. Pooling takes large images and shrinks them down while preserving the most important information. Common pooling operations are max pooling (taking the maximum value in a local region) or average pooling.
    * _Example:_ Taking a 2x2 region and replacing it with its maximum value shrinks the feature map by a factor of 2 in each dimension.
* **Activation Functions / Normalization:** Typically applied after convolution. ReLU is a common choice, changing negative values to zero (normalization).
* **Fully Connected Layers:** Usually found at the end of the network, after several convolution and pooling layers. These layers perform classification based on the high-level features extracted by the preceding layers.

**CNN Design:** Involves stacking several layers: images get filtered, rectified (e.g., by ReLU), and pooled to create new feature maps which are filtered and shrunken again and again. The input to each layer (two-dimensional arrays) looks like the output (two-dimensional arrays).
**Hyperparameters:** Many choices need to be made: for each convolution layer, how many features (filters) and how many pixels in each feature (filter size); for each pooling layer, the window size; for each extra fully connected layer, the number of hidden neurons.

**Deep Learning vs. Conventional Machine Learning:** Deep learning models generally require more data to achieve superior performance compared to conventional ML algorithms. With smaller datasets, conventional ML might be more effective or comparable. This assumes i.i.d. data in a stationary setting.

---
## Decision Trees 🌳

Decision Trees are a divide-and-conquer method that partitions the input space into a set of mutually exclusive regions, with a specific model assigned to each region.
* **Structure:**
    * **Internal Nodes:** Represent a decision-making unit that tests an attribute, determining which child node to visit next based on the outcome.
    * **Terminal Nodes (Leaves):** Have no child nodes and are associated with a specific partition of the input space.
        * In **classification trees**, a leaf contains a class label for its region.
        * In **regression trees**, a leaf contains a model (e.g., a constant value like the mean of the training samples in that region, or a linear model) that specifies the input/output mapping for that partition.
* **Interpretability:** Small trees are generally easy to understand and interpret.

### Regression Tree Predictor
For a query point $x_q \in \mathbb{R}^n$, it traverses the tree according to the decisions at each internal node until it reaches a leaf $\overline{j}$. The output is then given by the model $h_{\overline{j}}(x_{q},\alpha_{\overline{j}})$ associated with that leaf. For example, if the model in leaf $R2$ is $h_2(x_q, \alpha_2)$, then for $x_q$ falling into $R2$, the output is $\hat{y}_q = h_2(x_q, \alpha_2)$.

### Regression Tree Learning
This typically involves two stages: **tree growing** and **tree pruning**.
1.  **Tree Growing:**
    * This is a recursive process of making a succession of univariate (single feature) splits that partition the training data into increasingly homogeneous subsets.
    * Starting with the entire dataset at the root node, the algorithm exhaustively searches for the "best" split (feature and split point) that maximizes the reduction in empirical risk (e.g., Sum of Squared Errors, SSE).
    * Let $D(t)$ be the dataset at node $t$ of size $N(t)$, and $h_t$ be the local fitting model in that node (e.g., sample mean for constant fit). The empirical error at node $t$ is:
        $SSE_{emp}(t) = \min_{\alpha_t} \sum_{i=1}^{N(t)} L(y_i, h_t(x_i, \alpha_t))$, where $L$ is a loss function (e.g., squared error).
    * For a potential split $s$ of node $t$ into two children $t_l$ (left) and $t_r$ (right), the decrease in empirical error is:
        $\Delta E(s, t) = SSE_{emp}(t) - (SSE_{emp}(t_l) + SSE_{emp}(t_r))$
        where $N(t_r) + N(t_l) = N(t)$.
    * The best split $s^*$ is the one that maximizes this decrease: $s^* = \arg\max_s \Delta E(s, t)$.
    * The dataset is then partitioned into two disjoint subsets, and the process is applied recursively to the child nodes.
    * **Stopping Criteria:** Growing stops when the error or the error reduction $\Delta E$ is smaller than a threshold.
2.  **Tree Pruning:**
    * A fully grown tree often overfits the training data, leading to a **high overfitting risk**; pruning is required.
    * Cost-complexity pruning is a common method. It uses a complexity-based measure of tree performance:
        $R_{\lambda}(\mathcal{T}) = SSE_{emp}(\mathcal{T}) + \lambda |\mathcal{T}|$
        where $SSE_{emp}(\mathcal{T})$ is the sum of squared errors over all terminal nodes of tree $\mathcal{T}$, $|\mathcal{T}|$ is the number of leaves of $\mathcal{T}$ (a measure of complexity), and $\lambda \ge 0$ is a tuning parameter that accounts for the tree's complexity.
    * For a given $\lambda \ge 0$, the algorithm finds the subtree $\mathcal{T}(\lambda)$ that minimizes $R_{\lambda}(\mathcal{T})$. By gradually increasing $\lambda$, a sequence of trees with decreasing complexities is generated. The best tree in this sequence is often chosen using cross-validation.

### Decision Tree Hyperparameters
These manage the bias/variance trade-off:
* Minimum number of samples per leaf.
* Maximum number of leaves.
* Maximum depth of the tree.
* Growing criterion (not necessarily training error).
* Shrinking parameter $\lambda \ge 0$ for pruning.

### Pros and Cons of Decision Trees
* **Pros:**
    * Automatic identification of relevant variables (features used in splits are deemed important).
    * Tree-growing algorithms scale well to large numbers of samples $N$.
    * Can handle both continuous and categorical features, as well as missing data.
    * Small trees are easy to interpret.
* **Cons:**
    * Large trees can be difficult to interpret.
    * Individual trees often have poor prediction accuracy (high variance). They tend to be unstable, meaning small changes in the data can lead to very different tree structures.

**Why choose Decision Trees?** Their interpretability (for small trees) makes them attractive for explaining decisions. They are non-parametric and can capture nonlinear relationships and interactions between features. However, their predictive power is often improved by using them in ensembles.

---
## Random Forests (RF) 🌲🌲🌲

Random Forests are an ensemble learning technique proposed by Leo Breiman that addresses the high variance of individual decision trees. Ensemble methods are efficient when they combine low-bias and (ideally) independent estimators. Non-pruned (deep) decision trees are low-bias but high-variance estimators. RFs reduce variance by averaging the predictions of many decorrelated trees.

The decorrelation is achieved through two main mechanisms:
1.  **Bootstrap Aggregation (Bagging):** Each tree is grown on a bootstrap sample (random sample with replacement) of the original training data.
2.  **Random Feature Selection:** At each split in each tree, instead of considering all features, only a random subset of features is considered as candidates for the best split.

### Random Forest Algorithm
1.  Generate a set of $B$ bootstrap datasets from the original training data.
2.  For each bootstrap set, fit a maximal-depth decision tree. When growing each tree, at each node, select the best split from a random subset of $n'$ features (this is called feature bagging).
3.  The Random Forest prediction is the average (for regression) or majority vote (for classification) of the predictions from the $B$ individual trees.
4.  **Out-of-Bag (OOB) Error:** For each sample $x_i$ in the original dataset, it was "out of the bag" (not included) for some of the bootstrap samples used to train certain trees. The OOB error for $x_i$ is calculated using only those trees for which $x_i$ was an OOB sample:
    $e_{i} = y_{i} - \frac{1}{B_{i}}\sum_{b=1}^{B_{i}}h(x_{i},\alpha^{-i})$ (for regression)
    where $B_i < B$ is the number of trees that did not use sample $i$ in their training bootstrap set, and $h(x_i, \alpha^{-i})$ is the prediction of such a tree for $x_i$. The average of these $e_i$ (or $e_i^2$) gives an unbiased estimate of the generalization error without needing a separate validation set.

Typically, the size of the random feature subset $n'$ is $\sqrt{n}$ (where $n$ is total features). If $n'=n$, RF effectively becomes a standard bagging approach for trees. The precision of RF improves by enhancing single classifiers and reducing their correlation.

### Random Forest Hyperparameters
* Parameters of the individual trees (e.g., depth).
* Number of trees $B$ in the forest.
* Size $n'$ of the random feature subset at each split: small $n'$ (high decorrelation, large bias) vs. large $n'$ (smaller decorrelation, lower bias).

If the $B$ trees in the forest are almost unbiased, have comparable variance $Var[h_b] = \sigma^2$, and a mutual correlation $\rho$, the RF regression predictor $h_{rf}$ is then almost unbiased and its variance is:
$Var[h_{rf}] = \frac{(1-\rho)\sigma^{2}}{B} + \rho \sigma^{2}$
A random forest strategy reduces its variance by increasing the forest size $B$ and making the trees as uncorrelated as possible (reducing $\rho$).

### Why are Random Forests Successful?
* **"Off-the-shelf" learner:** Generally performs well with default hyperparameters and requires no complex tuning.
* **Fast to construct:** Trees can be grown in parallel.
* **Interpretable (to some extent):** While a large forest is not directly interpretable, measures like variable importance (related to cost function decrease during splitting, accumulated over all trees for a given feature) provide insights.
* **Effective implementation**.
* **Out-of-bag (OOB) generalization error and variance assessment:** For large B, it converges to leave-one-out error.
* **Handles mixed data types:** Manages mixtures of numeric and categorical predictor variables.
* **Robustness:** Immune to input outliers and invariant under monotone input transformations.
* **Feature selection incorporated**.

**Why choose Random Forests?** They are a powerful and versatile algorithm that often provides excellent accuracy with little tuning. They are robust to overfitting and can handle complex datasets.
* _Real-life example:_ Widely used in various domains, from predicting customer churn and credit risk scoring to bioinformatics for gene selection and disease prediction. An enhanced version is gradient boosting trees.

---
## Gradient Boosting Trees (GBT) 💪

Gradient Boosting is another ensemble technique that builds models (typically decision trees) sequentially. Unlike Random Forests where trees are grown independently, in boosting, each new tree attempts to correct the errors made by the ensemble of previously grown trees. This is done by resampling training points to focalize (e.g., by reweighting) on input space regions where the accuracy is bad.

* **Goal:** Improve the performance of "weak learners" (e.g., deep trees, although typically shallow trees are used as weak learners in boosting).
* **Characteristics:**
    * Trees are not independent but strictly related.
    * Less inherently parallel than RF because of the sequential nature.
    * Can be more sensitive to hyperparameter tuning than RF.
    * Adapted for both classification and regression tasks.

### Gradient Boosting Trees Algorithm (for Squared Loss Regression)
GB builds an additive sequence of trees:
$h_{M}(x) = \sum_{m=1}^{M} \mathcal{T}(x, \alpha_m)$
where $\mathcal{T}(x, \alpha_m)$ is the $m$-th tree with parameters $\alpha_m$ (which contain leaves weights, disjoint regions, and local models).
Trees are added in a **forward stagewise manner**. At the $m$-th step, the algorithm seeks to find the tree $\mathcal{T}(x, \alpha_m)$ that minimizes the loss when added to the current ensemble $h_{m-1}(x)$:
$\alpha_m = \arg\min_{\alpha} \sum_{i=1}^{N} L(y_i, h_{m-1}(x_i) + \mathcal{T}(x_i, \alpha))$
For squared error loss $L(y, \hat{y}) = (y - \hat{y})^2$, the solution is to fit a regression tree $\mathcal{T}(x_i, \alpha_m)$ that best predicts the **residuals** from the previous step:
$r_{i} = y_i - h_{m-1}(x_i)$ for $i=1, \dots, N$
So, the new tree $\mathcal{T}(x, \alpha_m)$ is trained to predict $r_{i}$.
This is called forward stagewise additive modeling: the next tree aims to cover the discrepancy (residual) between the target and the current ensemble prediction. Already added trees are not adjusted. Gradient-based versions exist for other differentiable loss criteria.

### Gradient Boosting Hyperparameters
* Size of the constituent trees.
* Number of iterations $M$ (number of trees).
* **Learning rate (or shrinkage parameter):** A small factor that scales the contribution of each new tree. This helps to prevent overfitting by making the learning process more conservative.

**Why choose Gradient Boosting?** GBTs are often among the top-performing algorithms in machine learning competitions, especially for structured/tabular data. They can capture very complex relationships.
* _Real-life example:_ Used in search ranking, recommendation systems, and fraud detection.

---
## Radial Basis Functions (RBF) 🌐

RBF networks are a type of **modular architecture**. The output is a weighted sum of basis functions:
$y = \sum_{j=1}^{m} \rho_j(x; c_j, B_j) h_j$
where:
* $h_j$ are constant weights for each basis function.
* $\rho_j(\cdot)$ are the radial basis functions, each with a center $c_j$ and a bandwidth (or scale/variance) $B_j$.
* A common RBF is the Gaussian function:
    $\rho_j(x; c_j, B_j) = \exp\left(-\frac{||x-c_j||^2}{B_j}\right)$
    where $||\cdot||$ is typically the L2 norm. This function's value is maximal at its center $c_j$ and decreases monotonically towards zero as the input $x$ moves away from $c_j$.

If the basis functions $\rho_j = \rho_j(\cdot, \eta_j)$ (where $\eta_j = \{c_j, B_j\}$ are parameters of the basis function) have localized "receptive fields" and a limited degree of overlap with their neighbors, the weights $h_j$ can be interpreted as locally piecewise constant models. The idea of basis functions arose in different fields, leading to similar approaches with different names like Local Model Networks and Neuro-Fuzzy Inference Systems.

### Fitting RBF Networks
1.  **Fitting Basis Functions:** For a given number $m$ of basis functions, their centers $c_j$ and variances (related to $B_j$) can be determined using unsupervised clustering techniques (like K-means or Expectation-Maximization) on the input data $x_i$.
2.  **Fitting RBF Weights:** Once the basis function parameters $\eta_j$ are fixed for $j=1, \dots, m$, the problem of finding the weights $h_j$ (and potentially an overall bias $h_0$) becomes a linear regression problem. Given a dataset $D_N = \{\langle x_i, y_i \rangle\}_{i=1}^N$, we have a system of linear equations:
    $y_i \approx h_0 + \sum_{j=1}^{m} h_j \rho_j(x_i, \eta_j)$ for each data point $i$.
    This can be written in matrix form as $Y = X\beta$, where $Y = [y_1, \dots, y_N]^T$ is the vector of target values, $\beta = [h_0, h_1, \dots, h_m]^T$ is the vector of weights to be learned, and $X$ is the design matrix with entries $X_{i0}=1$ and $X_{ij} = \rho_j(x_i, \eta_j)$ for $j=1, \dots, m$. This can be solved using standard least squares.

**Why choose RBFs?** They offer a conceptually simple way to model nonlinear functions by combining local experts. Training can be relatively fast if centers are determined heuristically or by clustering, as the weight determination is then a linear problem.
* _Real-life example:_ Function approximation, time series prediction, and control systems.

---
## Local Model Networks (LMN)

LMNs are an extension of RBFs where the constant local outputs $h_j$ are replaced by more complex **local models** $h_j(x, \alpha_j)$ (e.g., linear models specific to each region):
$y = \sum_{j=1}^{m} \rho_j(x, \eta_j) h_j(x, \alpha_j)$
Additionally, the basis functions $\rho_j(\cdot)$ are often constrained to form a "**partition of unity**," meaning they sum to 1 for any input $x$:
$\sum_{j=1}^{m} \rho_j(x, \eta_j) = 1 \quad \forall x \in \mathcal{X}$
This ensures that every point in the input space has equal weight from the basis functions, and variations in the output are solely due to the different local models $h_j$.

---
## Local Learning 📍

Local learning methods, also known as instance-based or memory-based learning, delay processing until a prediction for a query point $x_q$ is requested.

### Local Learning Procedure
Given a query point $x_q \in \mathbb{R}^n$:
1.  **Compute Distances:** Calculate the distance (using a predefined metric, e.g., Euclidean) between $x_q$ and all samples in the training set.
2.  **Rank Neighbors:** Sort the training samples based on their distance to $x_q$.
3.  **Select Neighborhood:** Choose a subset of the $k$ nearest neighbors. The **bandwidth**, which measures the size of the neighborhood, determines this selection.
4.  **Fit Local Model:** Fit a simple model (e.g., constant, linear) using only the selected neighbors. The prediction for $x_q$ is then made using this local model.

Several structural (or smoothing) hyperparameters control the amount of smoothing, including the bandwidth, the order of the local model, and the distance metric used.

### The Bandwidth Trade-off
The choice of bandwidth (or $k$) is critical and controls the bias/variance trade-off:
* **Too Narrow Bandwidth (small $k$):** The model uses very few neighbors. This can lead to **high variance** (overfitting) as the prediction is sensitive to noise in the few local points. The prediction error $e$ can be large.
* **Too Large Bandwidth (large $k$):** The model uses many neighbors, possibly from distant regions where the function behavior is different. This can lead to **high bias** (underfitting) as the local model smooths over important local details. The prediction error $e$ can also be large.

For a constant local model (predicting the average $y$ of the $k$ closest neighbors $x_{[i]}$ of $x_q$), the Mean Squared Error (MSE) at $x_q$ can be decomposed as:
$MSE(x_q) = \underbrace{\sigma_{w}^{2}}_{\text{Irreducible Error}} + \underbrace{\left(\frac{1}{k}\sum_{i=1}^{k}f(x_{[i]}) - f(x_q)\right)^2}_{\text{Squared Bias}} + \underbrace{\frac{\sigma_{w}^{2}}{k}}_{\text{Variance}}$
where $f(\cdot)$ is the true underlying function and $\sigma_w^2$ is the noise variance. Increasing $k$ (larger bandwidth) tends to decrease variance but increase bias, and vice-versa.

### Selection of the Number of Neighbors ($k$)
For a given query point $x_q$ and a range of possible $k$ values ($[k_{min}, k_{max}]$):
1.  For each $k$, compute the prediction $\hat{y}_q(k)$ (e.g., if local linear model, $\hat{y}_q(k) = x_q^T \hat{\beta}(k)$).
2.  Estimate the generalization error for each $k$, often using Leave-One-Out cross-validation error vectors ($\overline{MiSE}_{LOO}(k)$) calculated over the training set (or a subset).
3.  A "winner-takes-all" approach selects $\hat{k} = \arg\min_{k \in [k_{min}, k_{max}]} \overline{MiSE}_{LOO}(k)$, and the final prediction is $\hat{y}_q = x_q^T \hat{\beta}(\hat{k})$.

### Local Model Combination
Instead of picking a single best $k$, one can combine predictions from several good local models:
1.  Order the predictions $\hat{y}_q(k)$ and the $\overline{MISE}_{LOO}(k)$ values so that $\overline{MISE}_{LOO}(k_i) \le \overline{MISE}_{LOO}(k_j)$ for all $i < j$.
2.  The final prediction is a weighted average of the predictions from the top $b$ models (where $b$ is another hyper-parameter):
    $\hat{y}_q = \frac{\sum_{i=1}^{b}\zeta_{i}\hat{y}_{q}(k_{i})}{\sum_{i=1}^{b}\zeta_{i}}$, where weights $\zeta_{i}=1/\overline{MISE}_{LOO}(k_{i})$

**Why choose Local Learning?** It's adaptive and can model complex functions without making strong global assumptions. It's particularly useful when the data density varies or the function's complexity changes across the input space.
* _Real-life example:_ KNN is used in recommendation systems (find users similar to you), and locally weighted regression can be used for smoothing noisy time series data.

---
## The "No Free Lunch" Theorem 🚫🍔 and Model Selection

A crucial concept in machine learning is that there is **no single learning algorithm that is universally superior** to all others for all possible problems, or even to random guessing. The performance of an algorithm depends heavily on the match between the (unknown) true data distribution and the (implicit or explicit) inductive bias of the learner. This is often referred to as the "No Free Lunch" theorem.

### Rules of Thumb for Model Designers
Given this, model selection and design require careful consideration:
* **Understand Assumptions:** Be aware of the underlying assumptions each approach makes before applying it.
* **Simplicity First!**. While reality is often nonlinear, linear methods have a wealth of theoretical and algorithmic support.
* **Domain Knowledge MATTERS.**. But data too!.
* **Avoid "Algorithm Religion":** The best learning algorithm does NOT exist. It's better to be confident with a number of alternative techniques (preferably linear and nonlinear) and use them in parallel on the same task.
* **Combining and Regularization:** These are powerful non-parametric tools that make few assumptions and appear as powerful tools. They are at the forefront of data analysis technology. Do not forget to test them when you have a data analysis problem.
* **Features are Key:** Features determine most of the accuracy of a learner, because a model is only as good as its features.

---
## Beyond Simplifications: Real-World Challenges 🌍

Real-world learning tasks often present complexities that are neglected in simpler theoretical treatments. These include:
* **Big data**.
* **Unbalancedness of the classification task**.
* **Heteroskedasticity** (non-constant variance of errors).
* **Mixed nature of the variables** (real, binary, categorical, potentially with many levels).
* **Missing values**.
* **Large number of irrelevant (or redundant) variables**.
* **Skewed and long-tailed distributions** of inputs and outputs.
* **Batch vs. online nature of the learning task**.
* **Nonstationarity, concept drift** (the underlying data distribution may change over time).
* **Existing domain knowledge in qualitative form**.
* **Demand for interpretability**.