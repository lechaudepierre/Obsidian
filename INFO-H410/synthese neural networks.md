# Summary of Neural Networks

This document provides an overview of neural networks, from basic concepts like the Perceptron to more advanced topics like Deep Learning and Transformers.

## Plan

The document covers the following topics:
* Perceptron
* Linear Discriminant
* Multilayer Perceptron (MLP)
* Backpropagation
* Deep Neural Networks (Deep NN)
* Transformers

*(Visual on Page 1 & 2: The first page shows a diagram of an "Réseau neuronal artificiel" (Artificial Neural Network) with "Modèle d'entrée" (Input Model) leading to "nœud d'entrée" (input nodes), then to "nœud caché" (hidden nodes), and finally to "nœud de sortie" (output nodes) producing a "Modèle de sortie" (Output Model). Page 2 illustrates a neural network with an input layer, intermediate layer, and output layer, with connection matrices W and Z. This is applied to a "NAVLAB" (an autonomous vehicle) using a camera for "vision" to "conduite" (drive).)*

## 1. Perceptron

* **Historical Context:** The first neural network model, proposed by Rosenblatt between 1957 and 1961. It was inspired by the human brain, which was seen as the best "computer."
* **Goal:** To associate input patterns with recognition outputs.
* **Analogy:** Akin to a linear discriminant.

* **Constitution:**
    * **Input Layer / Retina:** Receives input signals (typically binary, 1/0).
    * **Output Layer:** Produces output signals (e.g., {0/1}).
    * **Connections/Synapses:** Links between input and output neurons, each with an associated weight.

    *(Visual on Page 5-8: These pages show diagrams of a Perceptron. Input nodes (x_i) are connected to output neurons. Each output neuron j computes a weighted sum of its inputs, $a_j = \sum_{i} x_i w_{ij}$ (where $w_{ij}$ is the weight of the connection from input i to output j). An activation function $f$ is then applied to $a_j$ to produce the output $o_j = f(a_j)$. A bias term $x_0$ (often with value 1) with weight $w_{0j}$ can be included. The decision rule is often a step function: $o_j = 1$ if $a_j > \theta_j$ (a threshold), and $o_j = 0$ otherwise.)*

    * $a_j$: Activation of the j-th output neuron.
    * $x_i$: Activation of the i-th input neuron.
    * $w_{ij}$: Connection parameter (weight) between input neuron i and output neuron j.
    * $o_j$: Output of the j-th neuron, determined by a decision rule (e.g., $o_j = 0$ if $a_j \le \theta_j$, $o_j = 1$ if $a_j > \theta_j$).

* **Learning (Supervised):**
    * Based on input patterns and their desired outputs.
    * **Process:**
        * If the output neuron's activation is correct (matches the desired output), no change is made to the weights.
        * Otherwise (inspired by neurophysiology):
            * If an output neuron was activated but shouldn't have been, decrease the value of its input connections.
            * If an output neuron was unactivated but should have been, increase the value of its input connections.
        * This process is iterated until the output neurons reach the desired values for all patterns.
    * **Widrow-Hoff Learning Rule (Delta Rule):** A common rule to update weights:
        $w_{ij}(t+1) = w_{ij}(t) + \eta (t_j - o_j) x_i$
        Where:
        * $w_{ij}(t+1)$ is the new weight.
        * $w_{ij}(t)$ is the current weight.
        * $\eta$ is the learning rate (a small positive constant).
        * $t_j$ is the target (desired) output for neuron j.
        * $o_j$ is the actual output of neuron j.
        * $x_i$ is the activation of input neuron i.

## 2. Theory of Linear Discriminant

* A linear discriminant computes a function $g(x) = W^T x + w_0$ (where $W$ is a vector of weights, $x$ is the input vector, and $w_0$ is a bias term).
* **Classification Rule:**
    * Class 1 if $g(x) > 0$
    * Class 2 otherwise (if $g(x) \le 0$)
    *(Visual on Page 11: A graph shows a line $g(x)=0$ separating a 2D space into two regions, $g(x)>0$ and $g(x)<0$, for classification.)*
* **Finding W (Weights):**
    * **Gradient Descent:** An iterative optimization algorithm to find the weights that minimize an error function. The weight update rule is:
        $\Delta W_i = -\eta \frac{\partial E}{\partial W_i}$ (for each weight $W_i$), where E is the error.
    * **Sigmoid Function:** Often used as an activation function for statistical interpretation, squashing the output to the range (0,1).
        $Y = \frac{1}{1 + e^{-g(x)}}$
        Its derivative is $Y(1-Y)$, which is easy to compute.
        Classification: Class 1 if $Y > 0.5$, Class 2 otherwise.
    * **Error Functions:**
        * Least Squares: $(Y - Y_d)^2$ (where $Y_d$ is the desired output).
        * Maximum Likelihood (Cross-Entropy for binary classification): $-\sum [Y_d \log Y + (1-Y_d) \log(1-Y)]$
    * The learning rule derived often takes the form: $\Delta W = \eta \sum (Y_d - Y) X_j$ (similar to the Widrow-Hoff rule).

* **Perceptron Limitations:**
    * Learning can be difficult.
    * **Crucially, a single Perceptron cannot separate data that is not linearly separable.**
    * **The XOR Problem:** A classic example of a non-linearly separable problem.
        *(Visual on Page 13: A graph shows the XOR problem. Points (0,0) and (1,1) belong to one class (e.g., output 0), while points (0,1) and (1,0) belong to another class (e.g., output 1). A single straight line cannot separate these two classes.)*
    * This limitation, highlighted by Minsky and Papert, significantly slowed down neural network research for about 20 years.
    * **Solution:** The introduction of the "magical hidden layer" and the backpropagation algorithm.

## 3. Multilayer Perceptron (MLP)

* To overcome the limitations of the single-layer Perceptron (like solving the XOR problem), MLPs introduce one or more **hidden layers** between the input and output layers.
    *(Visual on Page 14: Diagrams illustrate how the XOR problem can be solved using a network with a hidden layer. The first hidden neuron (A) might learn one separating line, the second hidden neuron (B) another, and the output neuron (C) can then combine the outputs of A and B to create a non-linear decision boundary.)*

* **Constitution:**
    * **Input Layer (I neurons):** Receives input $x$.
    * **Hidden Layer(s) (L neurons):** Intermediate layer(s) that perform transformations. The output of a hidden layer is $h$.
    * **Output Layer (J neurons):** Produces the final output $o$.
    * **Connection Matrices:**
        * $W$: Matrix of weights connecting the input layer to the first hidden layer.
        * $Z$: Matrix of weights connecting the last hidden layer to the output layer (or between hidden layers if multiple exist).
    *(Visual on Page 15 & 16: A typical MLP architecture is shown with an input layer, a hidden layer, and an output layer, fully connected. Page 16 shows the computation within an MLP: $a_j = \sum_i x_i w_{ij}$ for the hidden layer, and then $o_k = f(\sum_j h_j z_{jk})$ for the output layer, where $h_j = f(a_j)$ is the output of hidden neuron j.)*

## 4. Error Backpropagation (Backprop)

* **Learning Algorithm for MLPs:** Backpropagation is the most common algorithm for training MLPs. It's essentially an application of gradient descent using the chain rule for derivatives.
* **How it proceeds:**
    1.  **Forward Pass:**
        * Inject an input pattern into the network.
        * Compute the activations of neurons layer by layer, from input to output, to get the network's output.
        ($h = f(W \cdot x)$, then $o = f(Z \cdot h)$ for a one-hidden-layer MLP)
    2.  **Backward Pass (Error Propagation):**
        * **Compute Error at Output Layer:** Calculate the difference between the network's output ($o$) and the desired target output ($t$). This error signal is often denoted as $\delta_{output}$.
            For the logistic sigmoid function $f(x) = 1/(1+e^{-x})$ with derivative $f'(x) = f(x)(1-f(x))$, the error term for an output unit $k$ is typically:
            $\delta_k^{output} = (t_k - o_k) \cdot o_k(1-o_k)$ (if using sum-of-squares error and sigmoid activation).
            More generally, $\delta_{sortie} = f'(Z \cdot h) \cdot (t - o)$.
        * **Adjust Output Layer Weights (Z):** Update the weights connecting the hidden layer to the output layer based on this error.
            $Z(t+1) = Z(t) + \eta \cdot \delta_{sortie} \cdot h^T$
        * **Propagate Error to Hidden Layer(s):** Calculate the error signal for each neuron in the hidden layer(s). The error at a hidden neuron is a weighted sum of the errors of the output neurons it connects to.
            $\delta_{cachée} = f'(W \cdot x) \cdot (Z^T \cdot \delta_{sortie})$
        * **Adjust Hidden Layer Weights (W):** Update the weights connecting the input layer to the hidden layer based on the hidden layer's error signal.
            $W(t+1) = W(t) + \eta \cdot \delta_{cachée} \cdot x^T$
* **Transfer Function:** A differentiable activation function is required. The logistic sigmoid ($f(x) = \frac{1}{1+e^{-x}}$ with $f'(x) = f(x)[1-f(x)]$) is classically used.
    *(Visual on Pages 19-27: A series of diagrams visually steps through the backpropagation algorithm, showing the forward pass (input -> hidden -> output) and then the backward pass where error is calculated at the output and propagated back to adjust Z, then error is calculated for the hidden layer and propagated back to adjust W.)*

*(Visual on Pages 28-30: These diagrams illustrate how increasing the complexity of a neural network (more layers/neurons or more learning/epochs) can lead to better decision boundaries, from a simple linear discriminant to one that can fit more complex, non-linear data. The example seems to be a binary classification task.)*

*(Visual on Page 31 & 32: Page 31 shows a generic NN structure with inputs, multiple layers (Layer I, N, U), and outputs (forecasts). Page 32 gives a practical example: "description de dossier de prêt" (loan application description) with inputs like age, marital status, salary, loan amount, duration. The network outputs probabilities for "Bon" (Good: 18%) and "Mauvais" (Bad: 82%), leading to a "suggestion de décision".)*

* **Tricks for Neural Networks:**
    * Favor simple NNs (complexity can be added to the error function, e.g., regularization).
    * Theoretically, one hidden layer is often enough (Universal Approximation Theorem), but deeper networks can be more efficient.
    * Use cross-validation to prevent overfitting.
        *(Visual on Page 33: A graph shows training error decreasing over time, while validation error decreases initially and then starts to increase, indicating "sur-apprentissage" (overfitting). The optimal point to stop training is where validation error is minimal.)*

## 5. Deep Neural Networks (Deep Learning)

* The "revenge" of Neural Networks.
* **Key Idea of Deep Learning vs. Traditional Machine Learning:**
    * **Traditional ML:** Input -> Feature Extraction (manual) -> Classification -> Output.
    * **Deep Learning:** Input -> Feature Extraction + Classification (learned jointly by the network) -> Output.
    *(Visual on Page 35: This contrast is shown. Deep learning models learn the features automatically as part of the training process.)*
* **Applications:**
    * **Automatic Description of Images (2017):** Examples like "man in black shirt is playing guitar."
        *(Visual on Page 36: Examples of images with automatically generated captions.)*
* **Beyond the Multi-Layer Perceptron:**
    * While one hidden layer can theoretically learn any function, it's not always the most efficient way.
    * **Convolutional Neural Networks (CNNs/ConvNets):** Specialized for processing grid-like data, such as images.
        * **Convolution Layer:** Applies filters to input to create feature maps.
        * **Subsampling (Pooling) Layer:** Reduces dimensionality of feature maps.
        * These layers are typically followed by fully connected layers.
        *(Visual on Page 37 & 39: Page 37 shows a typical CNN architecture: Input -> Convolutions -> Subsampling -> Convolutions -> Subsampling -> Fully Connected -> Output. Page 39 shows a visual representation of a convolution operation (Kernel sliding over Input to produce I*K) and different network types like Deep Feed Forward (DFF) and Deep Convolutional Network (DCN).)*
    * **Activation Functions in Deep Learning:**
        * **Sigmoid:** $\sigma(x) = \frac{1}{1+e^{-x}}$
        * **Tanh:** $\tanh(x)$ (hyperbolic tangent)
        * **ReLU (Rectified Linear Unit):** $\max(0,x)$ - Very popular in deep learning as it helps mitigate the vanishing gradient problem.
        *(Visual on Page 38: Graphs of Sigmoid, Tanh, and ReLU functions.)*
* **Improving Training ("5 weird tricks"):**
    * How to initialize the model.
    * How to choose a nonlinearity (activation function).
    * How to avoid overfitting (e.g., dropout, regularization).
    * How to pre-process the data.
    * **Tools:** TensorFlow, Theano (mentioned as good alternatives). These tools compute gradients automatically and can run on GPUs for speed.
        *(Visual on Page 40: Mentions TensorFlow and Theano.)*
* **Progress in Image Recognition:**
    *(Visual on Page 41: A graph ("Imagenet Image Recognition") shows error rates decreasing significantly from 2011 to 2017, with deep models (SuperVision, Clarifai, VGG, MSRA, Trimps-Soushen) surpassing human performance.)*
* **Testing Training Techniques:**
    *(Visual on Page 42: A graph shows accuracy vs. number of layers for different techniques: "vanilla", "relu", "adam", "batchnorm". "all" (combining techniques) gives the best results, demonstrating the importance of these improvements for training deeper networks.)*
* **Deep in Time (Sequential Data):**
    * **Recurrent Neural Networks (RNNs):** Designed to handle sequential data (e.g., text, time series) by having connections that form cycles, allowing information to persist.
    * **Gated Memory for Sequences (e.g., LSTMs, GRUs):** More advanced RNN architectures that use "gates" to control the flow of information and better handle long-range dependencies.
        * **LSTM (Long Short-Term Memory)**
        * **GRU (Gated Recurrent Unit)**
        *(Visual on Page 44: A diagram of an LSTM cell by Chris Olah. Page 45 shows the equations and a diagram for a GRU cell: $z_t = \sigma(W_z \cdot [h_{t-1}, x_t])$, $r_t = \sigma(W_r \cdot [h_{t-1}, x_t])$, $\tilde{h}_t = \tanh(W \cdot [r_t * h_{t-1}, x_t])$, $h_t = (1-z_t) * h_{t-1} + z_t * \tilde{h}_t$.)*
    * **Different RNN Usages:**
        * one to one (e.g., standard MLP)
        * one to many (e.g., image captioning)
        * many to one (e.g., sentiment classification)
        * many to many (e.g., machine translation, video classification on frame level)
        *(Visual on Page 46: Diagrams illustrating these different RNN architectures.)*
    * **Example: Predictive Algorithms with RNNs:** Recommending items based on sequences of past interactions.
        *(Visual on Page 47: An RNN processing item representations to produce recommendation scores for movies like "GOOD BAD UGLY", "TRUE GRIT", etc.)*

* **Power of Neural Networks:**
    * They transform all information into vast numerical vectors.
    * "Ce qui se ressemble s'assemble" (What resembles each other, assembles together) - similar inputs get mapped to nearby points in the vector space.
        *(Visual on Page 48: A conceptual diagram showing different types of information (info 1, info 2) being mapped to a vector space where similar items (e.g., "chat" - cat, "chien" - dog) are clustered.)*
    * **Word Embeddings (e.g., Word2Vec - CBOW, Skip-gram):** Represent words as dense vectors, capturing semantic relationships.
        *(Visual on Page 49: Diagrams of CBOW (Continuous Bag-of-Words - predicts current word from context) and Skip-gram (predicts context words from current word) models.)*
    * They implicitly account for the statistical dimension of data.
    * They achieve a perfect cocktail between similarity and statistics.

## 6. Transformers

* A more recent and highly influential neural network architecture, particularly dominant in Natural Language Processing (NLP).
* **Key Innovation: Attention Mechanism** (specifically, self-attention). Allows the model to weigh the importance of different parts of the input sequence when processing information.
    *(Visuals on Pages 51-54: These pages introduce Transformers and include diagrams related to the attention mechanism, such as the "Attention is All You Need" paper's architecture with multi-head attention, positional encoding, encoder-decoder stacks.)*
* **Contextual Understanding:** Transformers can better understand the meaning of words in context.
    * Example (Page 55):
        * "Le **vol** du magasin a rapporté un bon butin" (The **theft** from the store brought a good loot)
        * "Le **vol** d’avion a subit de nombreux trous d’air" (The airplane **flight** suffered much turbulence)
        * "Le chien a dévoré l’**os** car il était affamé" (The dog devoured the **bone** because it was hungry)
        * "Le chien a dévoré l’os car il était **délicieux**" (The dog devoured the bone because **it (the bone)** was delicious) - or "il (le chien)"
* **Training Data (e.g., for GPT):** Trained on extraordinarily large text corpora.
    * Hundreds of billions of word sequences from sources like Common Crawl, webtexts, books, scientific publications, code (GitHub), Wikipedia. (Page 56)
* **Statistical Aspect:** Transformers are powerful statistical models that learn patterns from vast amounts of data. (Page 57)
* **Reinforcement Learning (from Human Feedback - RLHF):** Often used to fine-tune large language models (LLMs) like GPT to align their outputs with human preferences. (Page 58)
* **"Mon moment waouhhhh !!!!!!":** (My wow moment !!!!!!) - Likely referring to the impressive capabilities of models like GPT. (Page 59)
    *(Visual on Page 60 & 61: Page 61 shows an example of a program (bubble sort algorithm) and the LLM's simulation of its execution and explanation, demonstrating its reasoning and understanding capabilities.)*
* **Neurosymbolism:** An area of research aiming to combine the strengths of neural networks (learning from data) with symbolic reasoning (knowledge representation, logic). (Page 62)
* **Prompt Engineering:** The art of designing effective prompts to get desired outputs from LLMs.
    *(Visual on Page 64: "La nouvelle ingéniérie du prompt" (The new prompt engineering) showing "Relevant Context Database + LLM Prompt" as input to the LLM.)*

This summary covers the main topics presented in your "Neural Networks" document, highlighting key concepts and visual aids.
