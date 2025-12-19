# Detailed Summary: Course on Data Mining

This document provides a comprehensive overview of data mining, from foundational concepts like data warehousing to advanced techniques and real-world applications, as presented in the course material by Hugues Bersini (IRIDIA - ULB).

## 1. Introduction: From Data to Knowledge

The core idea is to transform "oceans of data" into "islands of knowledge." Data mining aims to understand existing data and predict future outcomes.

## 2. The Data Miner Steps

The process of data mining involves several key stages:

1.  **Data Warehousing:** Consolidating data from various sources into a central repository designed for analysis and decision support.
    * **Architecture:** Includes internal/external data sources, ETL (Extract, Transform, Load) processes, data marts (subsets of a data warehouse for specific departments), and tools for OLAP (Online Analytical Processing), querying, and reporting.
    * **Characteristics:** Subject-oriented, integrated, time-variant (historical), and non-volatile. It shifts data from production-focused systems to decision-based systems.
    * **Dimensions and Facts:** Data is often organized using a star schema, with fact tables (containing metrics like sales, margins) and dimension tables (e.g., Product, Period, Supplier, Client).

2.  **Data Preparation:** This is a crucial step.
    * **Cleaning & Homogenization:** Handling missing values, correcting errors, and ensuring consistency.
    * **Transformation & Composition:** Creating new attributes from existing ones.
    * **Reduction:** Reducing data volume or dimensionality while preserving essential information.
    * **For Time Series:** Time adjustment and alignment.

3.  **Data Modelling:** This is where algorithms are applied to uncover patterns and build predictive models. Researchers are mainly interested in this phase.

## 3. Data Mining: Understand and Predict

Data mining is distinct from OLAP. Its primary goals are:
* **To Understand:** Discover underlying structures, relationships, and patterns within the data.
* **To Predict:** Use the discovered patterns to forecast future values or classify new data points.

## 4. Main Techniques of Data Mining

Several techniques are employed in data mining:

* **Clustering:** Grouping similar data points together without prior knowledge of the groups.
* **Outlier Detection:** Identifying data points that are significantly different from the rest.
* **Association Analysis (Market Basket Analysis):** Discovering relationships between items in a dataset (e.g., "customers who buy X also tend to buy Y").
* **Forecasting:** Predicting future values based on historical time-series data.
* **Classification:** Assigning data points to predefined categories or classes.

## 5. The Era of Big Data

The exponential growth of data ("data volume doubles every 18 months") has led to the Big Data revolution.
* **Challenges:** Storing, processing, and analyzing massive datasets (terabytes and beyond).
* **Technologies:**
    * **HDFS (Hadoop Distributed File System):** A distributed file system for storing large datasets across clusters of computers.
    * **MapReduce:** A programming model for processing large datasets in parallel across a distributed cluster. The process involves:
        * **Map:** Processing input data and generating intermediate key-value pairs.
        * **Reduce:** Aggregating the intermediate pairs to produce the final output.
    * **Other Tools:** The Hadoop ecosystem includes tools like Sqoop, Flume (data integration), Pig, Hive (data access), HBase, Cassandra (NoSQL databases), YARN (scheduling), Mahout (machine learning), etc.
    * **HBase:** A NoSQL, column-oriented database built on top of HDFS, suitable for sparse data.

## 6. Discovering Structure in Data (Unsupervised Learning)

These techniques aim to find inherent patterns without labeled outputs.

### 6.1. Clustering
Grouping data points based on similarity.
* **Methods (with a metric):**
    * **Hierarchical Clustering:** Builds a tree of clusters.
        * *Example:* An image shows data points (salary vs. age) being grouped into clusters.
    * **K-Means:** Partitions data into K predefined clusters.
    * **Neural Network Clustering (Kohonen's Self-Organizing Maps):** Uses neural networks for clustering.
* **Methods (without a metric, using a cost function):**
    * **Grouping Genetic Algorithms.**

### 6.2. Association Analysis
Also known as Market Basket Analysis.
* **Goal:** Identify items that frequently co-occur in transactions.
    * *Example:* A table shows transactions with items like Juice, Tea, Coffee, Milk, Sugar, Pop.
* **Metric: Improvement:** Measures how much more likely two items are purchased together than expected if they were independent.
    * Formula: `IMPROVEMENT = (N * nij) / (ni * nj)`
        * `N`: Total number of transactions
        * `nij`: Number of transactions containing both item i and item j
        * `ni`: Number of transactions containing item i
        * `nj`: Number of transactions containing item j
    * An improvement value > 1 suggests a positive association.

## 7. Discovering Input/Output (I/O) Relationships in Data (Supervised Learning)

These techniques learn a mapping from input features (I) to an output variable (O).

* **Classification:** The output (O) is a class label. The input (I) is a set of features (e.g., x, y coordinates).
    * Goal: Predict the class for new input data.
* **Time Series Prediction:** The output (O) is a future value `x(t+1)`. The input (I) is a past value `x(t)` or a sequence of past values.
    * Goal: Predict the next value in a sequence.

### 7.1. Real-World Applications (Examples from IRIDIA - ULB)
* Recognition of glassy lacks at Glaverbel.
* Prediction of changes in exchange quotations for Masterfood and d'Ieteren.
* Recognition of events and prediction of electrical charge with Tractebel.
* Analysis of delays in aviation with Eurocontrôle.
* Modeling of industrial processes with Honeywell, FAFER, et Siemens.
* User-friendly search engine with the Walloon Region.
* Pixel classification of satellite images.

### 7.2. Specific Application Examples:
* **Financial Prediction:** Predicting future trends of stock market indices (e.g., MIB daily index) for automatic trading.
* **Economic Variables:** Predicting car matriculations in Belgium to support marketing campaigns.
* **Modeling of Industrial Plants:**
    * *Rolling Steel Mill:* Predicting steel plate flow stress based on chemical/physical properties to optimize production.
    * *Wastewater Treatment Plant:* Modeling plant dynamics to control water pollutants.
* **Environmental Problems:** Predicting algae summer blooming based on chemical concentrations to monitor river states.
* **Teledetection (Remote Sensing):** Classifying land cover types (arable land, urban fabric, forests, etc.) from satellite images. Bagfs shows better performance (lower error rate, higher Kappa) compared to C4.5 in an example.

## 8. Building Models: Comprehensible vs. Non-Comprehensible

A model uses data to learn, but once built, it can exist independently of the data. It has a structure and parameters. The goal is to find models that are:
* **Accurate:** Good fit to the data.
* **Simple:** Based on physical knowledge or engineering principles.
* **Robust:** Reliable and generalizable.
* **Understandable:** Easy for humans to interpret, aiding decision-making.

### 8.1. Comprehensible Models
Models whose decision-making process is transparent.

#### 8.1.1. Decision Trees
* **Characteristics:**
    * Work well with qualitative attributes.
    * Treat attributes separately, creating classification surfaces parallel to axes.
    * Good for comprehension because they select and separate variables.
* **How they work:**
    * Constructed top-down.
    * At each node, the most "discriminant" attribute is selected to split the data.
    * **Information Gain:** A statistical criterion (based on entropy) used to choose the best attribute.
        * `Entropy = -p_yes * log2(p_yes) - p_no * log2(p_no)`
        * Entropy is 0 if all instances belong to one class (pure).
        * Entropy is 1 if instances are equally mixed.
        * `Gain(S, A) = Entropy(S) - Σ_v (|S_v|/|S|) * Entropy(S_v)`
        * The attribute `A` that maximizes the gain is chosen.
* **Algorithms:** ID3, C4.5 (by Quinlan). Favor smaller trees (simpler models).
* **Advantages:**
    * Can handle noisy data.
    * Can learn logical models (AND/OR rules).
* **Examples:**
    * A tree classifying credit risk based on "Salaire" (Salary) and "Taux d'endettement" (Debt Ratio).
        * If Salaire < 100:
            * If Endettement < 20: Pas risque (No risk)
            * Else: Risque (Risk)
        * Else (Salaire >= 100):
            * If Endettement < 45: Pas risque
            * Else: Risque
    * A tree predicting tax cheating based on "Refund," "Marital Status," and "Taxable Income."
* **Limitation:** May not capture relationships involving combinations of attributes directly (e.g., "Is a good client if (salary - expenses) > 30000").

#### 8.1.2. Fuzzy Logic
* **Concept:** Uses linguistic rules to map inputs to outputs.
    * *Example:* "IF I eat 'a lot' THEN I take weight 'a lot'."
* Represents imprecise concepts using fuzzy sets and membership functions.
* Models can be readable, adaptable, and semi-automatic to build.

### 8.2. Non-Comprehensible Models ("Black Box" Models)
Models whose internal workings are complex and not easily interpretable, but often offer high predictive accuracy.
* Examples (from more to less comprehensible generally):
    * Linear Discriminant Analysis
    * Local Approaches (see Lazy Methods)
    * Fuzzy Rules (can be complex)
    * **Support Vector Machines (SVM):** Find an optimal hyperplane to separate classes.
    * Radial Basis Function (RBF) Networks
    * **Neural Networks (NN):** Inspired by biological brains, composed of interconnected nodes (neurons). Universal approximators but are black-box models.
    * Polynomials, Wavelets.

#### 8.2.1. Global Modeling (e.g., Neural Networks)
* **Advantages:**
    * Can exist without the original data once trained.
    * Achieve information compression.
    * SVMs have strong mathematical foundations and are practical.
    * Can detect global structures in data.
    * Allow sensitivity analysis of variables.
    * Can incorporate prior knowledge.
* **Drawbacks:**
    * Make assumptions of uniformity.
    * Have the bias of their chosen structure.
    * Can be hard to adapt.
    * Choosing the right architecture/model can be challenging.

## 9. Ensemble Methods: Improving Prediction

Combining multiple "weak classifiers" can lead to better generalization and accuracy.

### 9.1. Bagging (Bootstrap Aggregating)
* Proposed by Leo Breiman.
* Creates multiple training sets by random sampling with replacement (bootstrapping).
* Trains a classifier on each bootstrap sample.
* Aggregates predictions (e.g., majority vote for classification).
* Effective for "unstable" inducers (e.g., C4.5, neural networks, but not k-NN).
* Increases accuracy by reducing variance.

### 9.2. Multiple Feature Subsets (MFS)
* Uses subsets of features to train different classifiers.
* Parameters: `K` (proportion of features in subsets), `R` (number of subsets).
* Can decrease variance and bias through randomness.

### 9.3. BAGFS
* A multiple classifier system that combines MFS within each Bagging iteration (MFS inside Bagging).
* Parameters: `B` (number of bootstraps), `K` (proportion of features), `R` (number of feature subsets).
* Decision rule: Majority vote.
* **Experimental Results:** A table shows BAGFS (7x7) performing competitively or significantly better than C4.5, BagMFS 50, Boosting 50, Bag 50, and MFS 50 on several UCI datasets (e.g., hepatitis, glass, ionosphere, ringnorm).

## 10. Model Selection and Validation

* **The Challenge:** Many models can perfectly fit the *training* data.
* **Cross-Validation:** Essential to assess how a model generalizes to *unseen* data. It helps differentiate models that truly capture underlying patterns from those that merely memorize the training set.
    * The performance on a separate *testing set* is what makes the difference.

## 11. Lazy Methods (Instance-Based Learning)

These methods defer processing until a prediction is requested.

* **Core Idea:** "The best model is the data itself." Predictions are based directly on the stored training examples.
* **Synonyms:** Memory-based, instance-based, example-based, distance-based, nearest-neighbor.
* Applicable for regression, classification, and time series prediction, with quantitative or qualitative features.

### 11.1. Local Modeling Procedure
1.  **Distance Calculation:** Compute the distance between the query point (new instance) and all training samples using a predefined metric.
2.  **Neighbor Ranking:** Rank neighbors based on their distance to the query.
3.  **Subset Selection (Bandwidth):** Select a subset of the nearest neighbors. The "bandwidth" determines the size of this neighborhood.
4.  **Local Model Fitting:** Fit a simple local model (e.g., constant, linear) to the selected neighbors.

### 11.2. Bias/Variance Trade-off in Local Models
* **Too Few Neighbors (Small Bandwidth):** Leads to **overfitting** (high variance), where the model is too sensitive to noise in the training data.
* **Too Many Neighbors (Large Bandwidth):** Leads to **underfitting** (high bias), where the model is too simple to capture the underlying data structure.
* **Data-Driven Bandwidth Selection:** Use cross-validation (e.g., leave-one-out) to find the optimal bandwidth that minimizes prediction error.
    * **PRESS (Prediction Sum of Squares):** An efficient way to perform leave-one-out cross-validation for linear models.

### 11.3. Advantages of Lazy Methods
* No assumption of data uniformity.
* Often justified in real-life scenarios.
* Adaptive to local data characteristics.
* Conceptually simple.

### 11.4. Lazy Learning (LL)
* The entire learning procedure is deferred until a prediction is required for a specific query point (query-by-query learning).
* Contrasts with "eager" methods (e.g., neural networks) where learning is done in advance, and the model is stored.

### 11.5. Experimental Results & Applications
* **Static Benchmarks:** LL (linear, constant, combination) compared favorably against local modeling, Cubist (regression trees), Feed Forward NN, Mixtures of Experts, and Neuro-Fuzzy models on various datasets. Paired t-tests showed LL combination was significantly better many times and significantly worse fewer times.
* **Dynamic Tasks:**
    * Long-horizon forecasting (e.g., Santa Fe time series, Mackey-Glass). LL was able to predict abrupt changes.
    * Nonlinear control.
* **Awards:** Received awards in CoIL (Protecting rivers) and Advanced Black-box techniques competitions.

## 12. The Crucial Role of Similarity Measures

"THE DEFINITION OF A RIGHT AND ADEQUATE SIMILARITY MEASURE → THIS IS WHAT COUNTS MOST !!"

The choice of how to measure similarity (or distance) between data points is fundamental for many data mining techniques.

### 12.1. For Tabular Data
* **Quantitative Attributes:**
    * Euclidean distance (used in k-NN, SVM, Linear models).
* **Quantitative + Qualitative Attributes:**
    * Decision Trees implicitly use a measure based on the number of common attribute values leading to the same leaf.

### 12.2. For Linguistic/Text Data
* **Text Mining:**
    * Analysis based on term frequency (e.g., from RSS News).
    * **Web Mining (Hyperprisme project):** Automatic profiling of users (e.g., based on positive/negative keywords), automatic grouping of users.
    * **Clustering Reviewers' Comments (P&G example):** Identifying topics from customer reviews (e.g., "enjoying product smell/feel," "complaints about lumpy product").
* **Vectorization Techniques:**
    * **Word2Vec:** Learns vector representations (embeddings) for words. `model.wv.similarity('word1', 'word2')` can compute similarity. `model.wv.most_similar()` finds similar words.
    * **Doc2Vec:** Learns vector representations for entire documents.
* **Transformers (e.g., GPT-3, ChatGPT):** Advanced neural network architectures using "attention" mechanisms, trained on vast amounts of text data. They excel at understanding context and generating human-like text.

### 12.3. Similarity Based on Compression
* A novel way to measure similarity between two documents (A and B):
    * `C(A)`: Length of compressed A.
    * `C(B)`: Length of compressed B.
    * `C(AB)`: Length of compressed concatenation of A and B.
    * `Similarity(A,B) = 1 - [C(A) + C(B) - C(AB)] / C(A)` (if C(A) >= C(B)).
* Can be used to find similarity between natural languages.

### 12.4. For Genomics Data
* **InSilico DB Project:** A central data hub for genomics-based biomedical research (Data + Tools).
    * **Microarray Chips:** Measure gene expression levels.
    * **Goal:** Personalized medicine by identifying robust drug-genotype-phenotype relationships. Requires large sample sets.
    * Integration with visualization and analysis tools (IGV, Excel, GenePattern, R/Bioconductor).

### 12.5. For Graph Data
* **Challenges:** Analyzing huge graphs (e.g., protein interaction databases, social networks like Facebook).
* **Tasks:** Classification, community detection, influence measurement.
* **Link Prediction:** Predicting future connections in a network.
* **Bag of Paths:** A similarity measure for nodes in a graph based on the paths connecting them (considering path length and number).
    * Theory involves minimization under constraints, related to Boltzmann distribution.
* **Application: Genealogical Tree of Scientific Papers (Springer Link):**
    * Crawling publication data (DOI, references, date).
    * Constructing citation networks to rank papers and understand scientific evolution.

## 13. Other Applications and Projects (IRIDIA - ULB)

* **BridgeIris:** (Details not specified, but likely a project).
* **Sudden Infant Death Syndrome (SIDS):** (Application area).
* **Cancer Diagnosis:** (Application area).
* **SMART (Detection of Outlier Clinical Sites):** Using PCA and other analyses to identify fraudulent or unusual clinical trial centers.
* **Elicit-IT Project:** (Text mining entrepreneurial activities).
* **Book Analysis:** Using network analysis (clustering coefficient, degree) to study relationships in books.
* **Tevizz (Second Screen):** (Application related to television/media).

## 14. Pragmatic Conclusions

* **For Comprehension:** Decision trees and fuzzy logic are often preferred.
* **For Precision (Accuracy):** Global models (like NNs, SVMs) and lazy methods often perform better.
* It's crucial to be clear about *why* you are building models (the objective).

## 15. Future Research Directions

Continued research is needed to:
* Mine diverse data types more effectively:
    * **Text:** Word, Excel, HTML, XML, OCR-produced files, ASCII.
    * **Software:** Web services (often via XML).
    * **Multimedia:** Images, music, movies.
    * **Human/Machine Interface Actions:** Clickstreams.
* Handle heterogeneous data sources (relational databases, object databases, XML) through homogenization.
* Develop and refine **similarity measures**, as this remains a cornerstone of effective data mining.

