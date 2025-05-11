# Summary of Text Mining: Document Retrieval Techniques

This document provides an overview of techniques used in text mining for document retrieval, covering both foundational and some advanced concepts.

## 1. General Introduction

* **Goal of Information Retrieval:** To retrieve documents relevant to a user's concept or need from a large collection.
* **Process:**
    1.  A user has a concept in mind.
    2.  The user expresses this concept as a query (using words or terms).
    3.  An Information Retrieval (IR) system returns documents most related to this query.
* **Major Challenge:** There isn't a one-to-one mapping between words and concepts. A single word can relate to multiple concepts, and a single concept can be expressed by multiple words (e.g., the French word "marché" can mean "market" or "walked").

## 2. Documents Pre-processing

Before documents can be effectively processed by retrieval models, they undergo several pre-processing steps:

1.  **Extract Text and Structure:** Convert documents from various formats (e.g., Microsoft Word, LaTeX) into a structured format like XML. This separates the textual content from formatting and metadata.
2.  **Remove Stop Words:** Eliminate common words that carry little semantic meaning for retrieval purposes (e.g., "the", "at", "all", "is", "and").
3.  **Named Entity Recognition (NER):** Identify and categorize named entities such as proper names of people, organizations, locations, dates, etc. This helps in understanding the specific subjects of the document.
4.  **Stemming:** Reduce words to their root or base form. This ensures that different forms of a word are treated as equivalent (e.g., "processing," "processed," "processes" all become "process").
    * **Methods for Stemming:**
        * **Dictionary-based:** Uses a dictionary of root forms (e.g., Mmorph developed at the University of Geneva).
        * **Rule-based:** Applies linguistic rules to strip suffixes/prefixes (e.g., Porter's stemming algorithm for English).
        * **Example of French stemming rules:**
            * `aux` → `al` (e.g., `chevaux` → `cheval`)
            * `ouse` → `ou`
            * `eille` → `eil`
            * `nne` → `n`
            * `fs` → `v` (Note: these rules are context-dependent, indicated by `(m>0)` which usually refers to a measure of word stem length).

Tools like the Galilei project (developed at ULB) can assist with these tedious pre-processing tasks.

## 3. Information Retrieval: Basic Standard Techniques (Content-Based Methods)

### 3.1. The Vector-Space Model (VSM)

The VSM represents documents and queries as vectors in a multi-dimensional space where each dimension corresponds to a unique word (or term) from the entire document collection.

* **Core Idea:**
    * Each document $d_j$ is a vector where each element $f_{ij}$ represents the frequency (or a weighted frequency) of word $w_i$ in that document.
    * Queries are also represented as vectors in the same space.
    * This is often called the "bag of words" representation because the order of words is disregarded.
    * These vectors are typically very large (high-dimensional, equal to the number of unique words $n_w$) and sparse (most elements are zero).

* **Term-Document Matrix (D):**
    * A matrix where rows represent words ($n_w$) and columns represent documents ($n_d$).
    * The entry $D_{ij}$ is the frequency $f_{ij}$ of word $w_i$ in document $d_j$.
    * $D = \begin{bmatrix} f_{11} & f_{12} & \dots & f_{1n_d} \\ f_{21} & f_{22} & \dots & f_{2n_d} \\ \vdots & \vdots & \ddots & \vdots \\ f_{n_w1} & f_{n_w2} & \dots & f_{n_wn_d} \end{bmatrix}$

* **Query Representation (q):**
    * A query $q$ is also a vector, typically binary, where an element is 1 if the corresponding word is in the query, and 0 otherwise.
    * $q = \begin{bmatrix} 0 \\ 1 \\ 0 \\ \vdots \\ 0 \end{bmatrix}$ (if word $w_2$ is in the query)

* **Similarity Measurement:**
    * The relevance of a document to a query is determined by the similarity between their respective vectors.
    * **Cosine Similarity:** The most common measure. It calculates the cosine of the angle between the query vector $q$ and a document vector $d_i$.
        * $sim(q, d_i) = \cos(\theta) = \frac{q^T d_i}{||q|| \cdot ||d_i||}$
        * A value closer to 1 indicates higher similarity.
        * Euclidean distance is generally not suitable because query vectors are much shorter (contain fewer non-zero elements) than document vectors.
    * Similarity for all documents can be computed efficiently using the term-document matrix:
        $cos(q,D)=\frac{q^{T}}{||q||}D \cdot diag[\frac{1}{||d_{i}||}]$

#### Refinements to the Basic VSM:

1.  **Term Weighting:**
    * Recognizes that not all words have the same importance or "discriminative power."
    * **Inverse Document Frequency (IDF):** Measures the general importance of a term $w_i$. If a word appears in many documents, it's less discriminative and gets a lower IDF.
        * $idf_i = -\log_2[P(w_i)]$ where $P(w_i)$ is the a priori probability of word $w_i$ appearing in a document.
        * Estimated as: $idf_i = \log \left( \frac{\text{Total number of documents}}{\text{Number of documents containing } w_i} \right)$
        * Words rare across the collection get high IDF scores.
    * **Term Frequency (TF):** Measures the importance of a term $w_i$ within a specific document $d_j$.
        * $tf_{ij} = \frac{f_{ij}}{\sum_{k=1}^{n_w} f_{kj}}$ (normalized frequency of word $w_i$ in document $d_j$ to prevent bias towards longer documents).
    * **TF-IDF Score:** Combines TF and IDF to get a composite weight for each term in each document.
        * $tf.idf_{ij} = tf_{ij} \cdot idf_i$
        * The term-document matrix elements are replaced by these TF-IDF scores.
    * **Query Vector Weighting:** The query vector $q$ can also be weighted, often using the IDF scores of its terms: $q_i = -\log_2[P(w_i)]$ if word $w_i$ is in the query, 0 otherwise.

2.  **Latent Semantic Models (LSM) / Latent Semantic Indexing (LSI):**
    * **Goal:** Capture semantic information and address synonymy (different words with similar meanings) and polysemy (same word with different meanings). For example, a query for "newborn" should ideally retrieve documents containing "baby" even if "newborn" isn't explicitly present.
    * **Principle:** Words are considered semantically related if they frequently co-occur in the same documents.
    * **Method:** Uses dimensionality reduction techniques, primarily **Singular Value Decomposition (SVD)**, on the term-document matrix $D$.
        * SVD decomposes $D$ into $D = U \Sigma V^T$, where $U$ and $V$ are orthogonal matrices, and $\Sigma$ is a diagonal matrix of singular values ($\sigma_1 > \sigma_2 > \dots > \sigma_n > 0$).
        * To reduce dimensionality, only the top $m$ singular values are kept, setting others to zero, creating $\tilde{\Sigma}$.
        * This results in a lower-rank approximation of the original matrix: $\tilde{D} = U \tilde{\Sigma} V^T$. This $\tilde{D}$ is the best rank-$m$ approximation to $D$ in terms of Frobenius norm.
        * This process clusters semantically similar words and documents into a "concept space." Queries are then addressed to this reduced-rank matrix $\tilde{D}$.
    * **Challenge:** Determining the optimal number of dimensions ($m$) to keep. Too few dimensions may lose important information; too many may not achieve sufficient noise reduction or generalization.

### 3.2. Probabilistic Model

Probabilistic models estimate the probability that a document is relevant to a user's query.

* **Core Idea:**
    * Represent user profiles or queries using statistical models.
    * A document $d$ can be either relevant ($R=1$) or not relevant ($R=0$) to a user $u_k$ for a given query.
    * The goal is to rank documents by $P(R=1 | d=x, u_k)$, the probability that document $x$ is relevant to user $u_k$.

* **Binary Independence Retrieval (BIR) Model:**
    * **Document Representation:** Each document $d_i$ is a binary vector where $[d_i]_j = 1$ if word $w_j$ is present in $d_i$, and 0 otherwise.
    * **Relevance Feedback:** The model often uses initial relevance judgments (e.g., from a VSM ranking or explicit user feedback) to build the probabilistic model. Documents are initially classified as "considered relevant" or "considered irrelevant."
    * **Odds Ratio:** Instead of directly computing $P(R=1|d=x, u_k)$, it's often easier to compute the odds:
        $Odds(R=1|d=x, u_k) = \frac{P(R=1|d=x, u_k)}{P(R=0|d=x, u_k)}$
        This is a monotonic increasing function of $P(R=1|d=x, u_k)$, so it yields the same ranking.
    * **Using Bayes' Law:**
        $Odds(R=1|d=x, u_k) = \frac{P(d=x|R=1, u_k)P(R=1|u_k)}{P(d=x|R=0, u_k)P(R=0|u_k)}$
    * **Conditional Independence Assumption (Naive Bayes):** The BIR model assumes that words occur independently of each other within a document, given the relevance status of that document. This is a strong, simplifying assumption.
        $P(d=x|R, u_k) = \prod_{n=1}^{n_w} P(d_n = x_n | R, u_k)$
    * **Final Ranking Score (proportional to odds):**
        $\lambda \propto \prod_{n \text{ s.t. } x_n=1} \frac{P(d_n=1|R=1, u_k)}{P(d_n=1|R=0, u_k)}$ (considering only words present in the document $x$).
        The terms $P(d_n=1|R=1, u_k)$ (probability of word $w_n$ appearing in a relevant document) and $P(d_n=1|R=0, u_k)$ (probability of word $w_n$ appearing in an irrelevant document) are estimated from the frequencies in the sets of relevant and irrelevant documents.
    * **Query Expansion:** The process of refining the query based on relevant documents (e.g., adding terms from highly ranked documents) can be used with probabilistic models.

* **Limitations and Extensions:**
    * The independence assumption is often violated in practice.
    * More sophisticated models exist, such as Poisson models (to account for word counts, not just presence/absence) or models considering second-order interactions (correlations) between words.

## 4. Assessment of Document Retrieval Systems

To evaluate the effectiveness of an IR system, several measures are commonly used:

* **Precision:** The proportion of retrieved documents that are actually relevant.
    * Precision = $\frac{|\text{Relevant documents} \cap \text{Retrieved documents}|}{|\text{Retrieved documents}|}$
    * Indicates how many of the system's "hits" were actually useful.

* **Recall:** The proportion of all relevant documents in the collection that were retrieved by the system.
    * Recall = $\frac{|\text{Relevant documents} \cap \text{Retrieved documents}|}{|\text{Relevant documents}|}$
    * Indicates how comprehensively the system found all the relevant items.

* **Trade-off:** There is typically a trade-off between precision and recall. Systems that retrieve more documents (higher recall) may retrieve more irrelevant ones (lower precision), and vice-versa.

* **F-measure (or F1-score):** A single measure that combines precision and recall using their harmonic mean. It gives a balanced assessment.
    * $F = 2 \cdot \frac{\text{Precision} \times \text{Recall}}{\text{Precision} + \text{Recall}}$

*(The document's table of contents also mentions "Information retrieval: More recent techniques" including PageRank, HITS, and exploiting relational structure, but the provided text focuses primarily on the basic methods detailed above.)*
