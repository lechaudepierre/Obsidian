![[Capture d’écran 2025-12-21 à 10.10.12.png]]
## 1. Explain clustering as an unsupervised learning method

#### 1.1 Definition and Goal

Clustering is an informal and intuitive process of partitioning a set of data points into groups (clusters) such that:

- Data points within the same group are **highly similar**.
- Data points in different groups are **dissimilar**.

As an **unsupervised learning method**, clustering does not rely on predefined labels or categories. Instead, it explores the underlying structure of the data to discover natural groupings or hidden patterns.

#### 1.2 The "Chicken and Egg" Problem

In many clustering algorithms (like representative-based methods), we face an optimization dilemma:

- To find the best **groups**, we need to know the **representatives** (centroids/medoids).
- To find the best **representatives**, we need to know the **groups**.

Because the optimal assignments are unknown _a priori_, clustering is typically solved using an **iterative approach**:

1. Start with candidate representatives.
2. Assign points to the closest representative.
3. Update representatives based on the new assignments.
4. Repeat until convergence.

#### 1.3 Key Applications

Clustering is used across various domains for different purposes:

- **Data Summarization:** Using cluster representatives (centroids) as a summary of the entire dataset.
- **Customer Segmentation:** Grouping customers based on shared attributes for targeted marketing.
- **Social Network Analysis:** Detecting communities within a network.
- **Preprocessing Tool:** Acting as a helper for other Data Mining problems (e.g., performing outlier analysis by identifying points that do not fit into any cluster).

#### 1.4 Categories of Clustering Algorithms

The slides identify several major approaches:

1. **Representative-Based:** K-Means, K-Medians, K-Medoids.
2. **Hierarchical:** Agglomerative (bottom-up) or Divisive (top-down).
3. **Density-Based:** DBSCAN, DENCLUE (finding dense regions of arbitrary shapes).
4. **Probabilistic Model-Based:** Soft clustering where points can belong to multiple clusters with certain degrees of membership.

## 2. Apply the K-Means, the K-Medians, and the DBSCAN Algorithms to given datasets.

#### 2.1 K-Means Algorithm

**Input Required:**

- Dataset D with n data points
- Number of clusters k
- Distance function: Euclidean distance (L2-norm)

**Steps:**

1. **Initialize:** Randomly select k data points as initial cluster representatives (centroids)
2. **Assignment Step:**
    - For each data point, calculate the Euclidean distance to each centroid
    - Assign each point to the closest centroid
    - This forms k clusters C₁, C₂, ..., Cₖ
3. **Optimization Step:**
    - For each cluster Cⱼ, calculate the new centroid (mean) as:
        - New centroid = average of all points in the cluster
        - Calculate mean for each dimension separately
4. **Repeat:** Continue steps 2-3 until convergence (centroids no longer change significantly or max iterations reached)

**Key Points:**

- Representatives are NOT chosen from the original dataset (they are calculated means)
- Objective: Minimize sum of squared Euclidean distances (SSE)
- Works best for spherical clusters
- Time complexity: O(n·k·t·d) where t is iterations, d is dimensions

---

#### 2.2 K-Medians Algorithm

**Input Required:**

- Dataset D with n data points
- Number of clusters k
- Distance function: Manhattan distance (L1-norm)

**Steps:**

1. **Initialize:** Randomly select k data points as initial cluster representatives
2. **Assignment Step:**
    - For each data point, calculate the Manhattan distance to each representative
    - Manhattan distance: |x₁ - y₁| + |x₂ - y₂| + ... + |xₐ - yₐ|
    - Assign each point to the closest representative
3. **Optimization Step:**
    - For each cluster Cⱼ, calculate the new representative as:
        - Find the median for each dimension independently
        - New representative = (median of dimension 1, median of dimension 2, ...)
4. **Repeat:** Continue steps 2-3 until convergence

**Key Points:**

- Representatives are NOT from the original dataset (they are calculated medians)
- Objective: Minimize sum of Manhattan distances
- More robust to outliers than K-Means (median is less sensitive than mean)
- Median is chosen independently for each dimension

---

#### 2.3 DBSCAN Algorithm

**Input Required:**
- Dataset D with n data points
- Eps: radius of neighborhood
- MinPts (τ): minimum number of points to form a dense region

**Steps:**
1. **Initialize:** Mark all points as unvisited, cluster counter C = 0
2. **For each unvisited point P:**
    - Mark P as visited
    - Find all points within Eps distance (neighborhood): NeighborPts = regionQuery(P, Eps)
    - **If |NeighborPts| < MinPts:**
        - Mark P as noise (may be changed later)
    - **Else (P is a core point):**
        - Create new cluster: C = C + 1
        - Add P to cluster C
        - Expand cluster using NeighborPts
3. **To expand cluster:**
    - For each point P' in NeighborPts:
        - If P' is unvisited:
            - Mark P' as visited
            - Find P' neighbors: NeighborPts' = regionQuery(P', Eps)
            - If |NeighborPts'| ≥ MinPts:
                - Add NeighborPts' to NeighborPts (expand the neighborhood)
        - If P' is not in any cluster:
            - Add P' to cluster C
4. **Result:** Points are classified as:
    - **Core points:** Have ≥ MinPts in their Eps-neighborhood
    - **Border points:** In neighborhood of core points but not core themselves
    - **Noise points:** Neither core nor border

**Key Points:**

- Number of clusters is NOT predefined (discovered automatically)
- Can detect clusters of arbitrary shapes
- Handles noise/outliers explicitly
- Time complexity: O(n²) worst case, O(n·log(n)) with spatial indexing
- Works well for clusters with varying densities when using progressive DBSCAN

---

#### Comparison Summary

|Algorithm|Representatives|Input Parameters|Best For|
|---|---|---|---|
|K-Means|Mean (centroid)|k clusters|Spherical clusters, no outliers|
|K-Medians|Median|k clusters|Data with outliers|
|DBSCAN|None|Eps, MinPts|Arbitrary shapes, noisy data|

## 3. Compare K-Means, K-Medians and K-Medoids Algorithms.

#### 1. Distance Function & Objective

**K-Means:**

- Distance: Euclidean distance (L2-norm)
- Objective: Minimize sum of squared Euclidean distances (SSE)
- Formula: Σ(distance²) for all points to their representatives

**K-Medians:**

- Distance: Manhattan distance (L1-norm)
- Objective: Minimize sum of Manhattan distances
- Formula: Σ|x₁ - y₁| + |x₂ - y₂| + ... for all points

**K-Medoids:**

- Distance: Any distance function (flexible)
- Objective: Minimize sum of distances using the chosen distance function
- Does not require mean/median calculations

---

#### 2. Representative Selection

**K-Means:**

- Representatives are **calculated centroids** (means)
- Representatives are **NOT from the original dataset**
- Computed as: average of all points in each cluster
- Mean calculated for each dimension

**K-Medians:**

- Representatives are **calculated medians**
- Representatives are **NOT from the original dataset** 
- Computed as: median of all points in each cluster
- Median chosen **independently for each dimension**

**K-Medoids:**
- Representatives are **ALWAYS actual data points** from dataset D
- Representatives are **chosen from the original dataset**
- Cannot create "virtual" representatives

---

#### 3. Optimization Strategy

**K-Means:**

- Standard iterative approach
- Assignment step: assign points to closest centroid
- Optimization step: recalculate centroid as mean of cluster points
- Relatively fast convergence

**K-Medians:**

- Standard iterative approach
- Assignment step: assign points to closest median
- Optimization step: recalculate median for each dimension
- Similar to K-Means but more computationally intensive

**K-Medoids:**

- Uses **hill-climbing strategy**
- Initialize set S with k points from database D
- **Iteratively improve** by exchanging a single point from S with another point from D
- At each iteration: try **multiple exchanges** and choose the best one
- Each exchange viewed as a hill-climbing step
- More computationally expensive than K-Means

---

#### 4. Robustness to Outliers

**K-Means:**

- **Sensitive to outliers**
- Mean is heavily influenced by extreme values
- Can get stuck with singleton clusters if outlier used in initialization
- Outliers can significantly skew centroids

**K-Medians:**

- **More robust to outliers** than K-Means
- Median is less sensitive to extreme values
- Better handles datasets with noise or anomalies

**K-Medoids:**

- **Most robust to outliers**
- Since representatives must be actual data points
- Outliers less likely to be chosen as representatives
- Not affected by extreme calculated values

---

#### 5. Data Type Requirements

**K-Means:**

- Requires **numerical data**
- Needs to calculate mean (arithmetic average)
- Cannot handle categorical or complex data types
- Works only in continuous spaces

**K-Medians:**

- Requires **numerical data**
- Needs to calculate median
- Limited to ordered numerical values
- Cannot handle complex data types

**K-Medoids:**

- Works with **any data type**
- Only requires a distance/dissimilarity function
- Can handle: numerical, categorical, mixed, complex types
- Most flexible for different data types

---

#### 6. Cluster Shape Bias

**K-Means:**

- **Biased towards spherical clusters**
- Due to Euclidean distance and squared error objective
- Does not work well for clusters of arbitrary shapes
- Assumes roughly equal-sized, round clusters

**K-Medians:**

- Less biased than K-Means but still has shape preferences
- Manhattan distance creates diamond-shaped distance contours
- Better for grid-like patterns

**K-Medoids:**

- Shape depends on chosen distance function
- More flexible in handling different cluster shapes
- Can adapt better to irregular clusters

---

#### 7. Computational Complexity

**K-Means:**

- **Fastest** of the three
- Simple mean calculations
- Efficient for large datasets
- Time complexity: O(n·k·t·d) where n=points, k=clusters, t=iterations, d=dimensions

**K-Medians:**

- **Moderate** complexity
- Median calculation more expensive than mean
- Requires sorting for each dimension
- Slightly slower than K-Means

**K-Medoids:**

- **Most expensive** computationally
- Must evaluate multiple point exchanges at each iteration
- Hill-climbing with multiple trials per iteration
- Much slower for large datasets
- Time complexity: O(k(n-k)²·t) in typical implementations

---

#### 8. Use Cases & When to Choose

**K-Means:**

- Large datasets with numerical features
- When speed is priority
- Data without significant outliers
- Spherical, well-separated clusters
- Euclidean distance is appropriate

**K-Medians:**

- Numerical data with outliers
- When robustness to noise is needed
- Manhattan distance is more appropriate (e.g., city-block distances)
- Moderate-sized datasets

**K-Medoids:**

- Complex or non-numerical data types
- When representatives must be actual data points
- Small to medium datasets (due to computational cost)
- Need interpretable cluster centers
- Custom distance functions required
- High presence of outliers

---

#### Summary Table

|Feature|K-Means|K-Medians|K-Medoids|
|---|---|---|---|
|**Distance**|Euclidean (L2)|Manhattan (L1)|Any distance|
|**Representative**|Mean (calculated)|Median (calculated)|Actual data point|
|**From Dataset?**|No|No|Yes|
|**Outlier Robustness**|Low|Medium|High|
|**Data Types**|Numerical only|Numerical only|Any type|
|**Speed**|Fast|Moderate|Slow|
|**Cluster Shape**|Spherical bias|Some bias|Flexible|
|**Calculation Required**|Mean|Median|Distance only|
## 4.  Compare between the different families of clustering Algorithms (representative-based, density based, probabilistic model-based).
#### 1. Representative-Based Algorithms
- **Examples:** K-Means, K-Medians, K-Medoids
- **Core Idea:** Find a high-quality set of representatives, then link data points to their closest representatives

#### 2. Density-Based Algorithms
- **Examples:** DBSCAN, DENCLUE, Grid-Based methods
- **Core Idea:** Detect dense areas in the data, then grow and merge them

#### 3. Probabilistic Model-Based Algorithms
- **Examples:** Gaussian Mixture Models, EM Algorithm, Fuzzy Clustering
- **Core Idea:** Assume data is generated from a mixture of probability distributions; perform soft clustering

---

#### Detailed Comparison

##### Cluster Assignment Approach

**Representative-Based:**
- **Hard clustering:** Each point belongs to exactly ONE cluster
- Assignment based on proximity to nearest representative
- Clear, deterministic assignment
- No uncertainty or ambiguity in membership

**Density-Based:**
- **Hard clustering with noise:** Each point is either in ONE cluster or marked as noise
- Assignment based on density reachability
- Points classified as: core, border, or noise
- Noise points don't belong to any cluster

**Probabilistic Model-Based:**
- **Soft clustering:** Points can belong to MULTIPLE clusters
- Each point has a membership degree/probability for each cluster
- Captures uncertainty in cluster assignment
- Partition matrix [wᵢⱼ] where 0 ≤ wᵢⱼ ≤ 1
- Sum of memberships for each point = 1

---

##### Number of Clusters

**Representative-Based:**
- **Must be predefined:** User specifies k (number of clusters) as input
- Cannot discover the "natural" number of clusters
- Major limitation: difficult to determine good value for k
- May need to try multiple k values or use post-processing (cluster merging)

**Density-Based:**
- **Automatically discovered:** Number of clusters NOT predefined
- Algorithm determines number based on data structure
- Depends on density parameters (Eps, τ/MinPts)
- Can find varying numbers of clusters in same dataset with different parameters

**Probabilistic Model-Based:**
- **Must be predefined:** User specifies k (number of distributions)
- Similar to representative-based
- Need to know or estimate number of underlying distributions

---

##### Cluster Shape Flexibility

**Representative-Based:**
- **Limited flexibility:** Shape implicitly defined by distance function
- K-Means: biased towards **spherical clusters**
- Assumes prototypical shape
- Cannot handle arbitrary or complex shapes well
- Struggles with elongated or irregular clusters

**Density-Based:**
- **High flexibility:** Can detect **clusters of arbitrary shapes**
- Not limited by distance function assumptions
- Can handle: elongated, curved, irregular, nested clusters
- Best for complex geometric structures
- Main advantage over representative-based methods

**Probabilistic Model-Based:**
- **Moderate flexibility:** Depends on assumed distribution
- Gaussian mixture: tends toward elliptical clusters
- Limited by parameterized distribution choice
- More flexible than K-Means but less than density-based

---

##### Handling Outliers and Noise

**Representative-Based:**
- **Poor outlier handling**
- Outliers MUST be assigned to some cluster
- Can significantly distort cluster representatives
- K-Means especially sensitive (mean affected by outliers)
- Can create singleton clusters if outlier in initialization
- No concept of "noise" points

**Density-Based:**
- **Excellent outlier handling**
- Explicit **noise point** category
- Noise points not forced into clusters
- Core algorithm feature, not afterthought
- Points in sparse areas identified as noise
- Natural and principled noise detection

**Probabilistic Model-Based:**
- **Moderate outlier handling**
- Outliers get low membership probabilities across all clusters
- Not explicitly identified as noise
- Can influence parameter estimation
- Better than hard clustering but not as explicit as DBSCAN

---

##### Algorithm Complexity

**Representative-Based:**
- **Relatively simple** conceptually and computationally
- Chicken-and-egg problem: need representatives to assign points, need assignments to find representatives
- Solved with iterative approach
- Two main steps: Assignment + Optimization
- Time complexity: O(n·k·t·d) for K-Means
- Fast convergence, efficient for large datasets

**Density-Based:**
- **Moderate to high complexity**
- DBSCAN: O(n²) worst case, O(n·log(n)) with spatial indexing
- Major bottleneck: finding neighbors within distance Eps
- Grid-based: grows exponentially with dimensionality (p^d hyper-cubes)
- Can be computationally infeasible in high dimensions

**Probabilistic Model-Based:**
- **High complexity** both conceptually and computationally
- EM algorithm: iterative expectation and maximization steps
- Requires probability calculations for all points and all clusters
- Parameter estimation can be computationally expensive
- May require multiple initializations for good results

---

##### Parameters Required

**Representative-Based:**
- **k:** number of clusters (critical parameter)
- Initialization method for representatives
- Distance function
- Convergence criteria (optional)
- Simple parameter set but k is difficult to choose

**Density-Based:**
- **Eps:** radius of neighborhood (critical)
- **τ/MinPts:** density threshold - minimum points for dense region (critical)
- Grid-based also needs: **p** (grid resolution)
- Parameters are related and interdependent
- Different challenge than choosing k
- Progressive DBSCAN can help: iterate with increasing Eps

**Probabilistic Model-Based:**
- **k:** number of distributions
- Distribution type (e.g., Gaussian)
- Distribution parameters (μ, σ for Gaussian)
- Convergence criteria
- Most complex parameter set

---

##### Objective Function

**Representative-Based:**
- **Explicit optimization:** Minimize sum of distances
- K-Means: minimize SSE (sum of squared errors)
- K-Medians: minimize sum of L1 distances
- Clear, quantifiable objective
- Iteratively improve objective function
- Local optimum guaranteed (but not global)

**Density-Based:**
- **Implicit objective:** No explicit function to minimize
- Goal: find density-connected regions
- Based on density threshold and connectivity
- Success measured by quality of dense region detection
- No optimization in traditional sense

**Probabilistic Model-Based:**
- **Maximize likelihood:** P(D|C) - probability of data given clusters
- Find clusters that most likely generated the observed data
- Maximize: Π P(oᵢ|C) over all objects
- Principled statistical framework
- Compromise: assume parameterized distributions for tractability

---

##### Use Cases and Applications

**Representative-Based:**
- **Best for:**
  - Data summarization (use representatives as summary)
  - Customer segmentation with clear categories
  - Large datasets requiring efficiency
  - Well-separated, compact clusters
  - When cluster centers are meaningful

**Density-Based:**
- **Best for:**
  - Spatial data analysis (GIS applications)
  - Detecting communities in networks
  - Map-making (e.g., road segments from GPS tracks)
  - Data with noise and outliers
  - Clusters with irregular shapes
  - Unknown number of clusters
  - Outlier detection as byproduct

**Probabilistic Model-Based:**
- **Best for:**
  - Multi-label scenarios (reviews for multiple products)
  - Understanding uncertainty in assignments
  - User intent analysis (multiple search intents)
  - When objects naturally belong to multiple categories
  - Statistical modeling and inference
  - When soft boundaries between clusters exist

---

##### Advantages and Disadvantages

**Representative-Based:**

✅ **Advantages:**
- Simple to understand and implement
- Computationally efficient
- Works well for spherical, well-separated clusters
- Scales to large datasets
- Representatives provide interpretable summaries

❌ **Disadvantages:**
- Must specify k in advance
- Sensitive to initialization
- Poor with arbitrary cluster shapes
- Sensitive to outliers (especially K-Means)
- Can converge to local optima
- Biased by distance function choice

---

**Density-Based:**

✅ **Advantages:**
- No need to specify number of clusters
- Handles arbitrary cluster shapes
- Robust to outliers (explicit noise handling)
- Can detect clusters of varying sizes and densities
- Natural for spatial data

❌ **Disadvantages:**
- Difficult to choose Eps and MinPts parameters
- Struggles with varying density clusters (single τ)
- Computationally expensive in high dimensions
- Grid-based: exponential growth with dimensionality (curse of dimensionality)
- May miss clusters if density threshold inappropriate

---

**Probabilistic Model-Based:**

✅ **Advantages:**
- Soft clustering captures uncertainty
- Principled statistical framework
- Handles overlapping clusters
- Provides probability interpretations
- Suitable for multi-membership scenarios

❌ **Disadvantages:**
- Computationally expensive
- Complex to implement and understand
- Must specify k and distribution type
- Assumptions about distributions may be wrong
- Sensitive to initialization
- Requires statistical knowledge

---

##### Summary Table

| Feature | Representative-Based | Density-Based | Probabilistic Model-Based |
|---------|---------------------|---------------|---------------------------|
| **Assignment** | Hard (one cluster) | Hard + noise | Soft (multiple clusters) |
| **# of Clusters** | Predefined (k) | Discovered | Predefined (k) |
| **Cluster Shape** | Limited (spherical bias) | Arbitrary shapes | Moderate (elliptical) |
| **Outliers** | Poor handling | Excellent (noise points) | Moderate |
| **Complexity** | Low | Moderate-High | High |
| **Key Parameters** | k | Eps, MinPts | k, distribution type |
| **Objective** | Minimize distances | Implicit (density) | Maximize likelihood |
| **Speed** | Fast | Moderate | Slow |
| **Best For** | Compact clusters | Irregular shapes, noise | Overlapping clusters |
| **Scalability** | Excellent | Poor (high-dim) | Poor |

---

##### Choosing the Right Family

**Choose Representative-Based when:**
- You have a rough idea of number of clusters
- Clusters are compact and well-separated
- Speed and efficiency are priorities
- Outliers are minimal
- Large datasets

**Choose Density-Based when:**
- Number of clusters is unknown
- Clusters have complex, arbitrary shapes
- Data contains significant noise/outliers
- Working with spatial or geographic data
- Need explicit outlier detection

**Choose Probabilistic Model-Based when:**
- Objects can belong to multiple clusters
- Need uncertainty quantification
- Statistical interpretation is important
- Overlapping clusters expected
- Small to medium datasets

## 5. Analyse the complexity of the studied clustering algorithms 

#### K-Means Algorithm

###### Time Complexity

**O(n · k · t · d)**

Where:

- n = number of data points
- k = number of clusters
- t = number of iterations
- d = number of dimensions

###### Breakdown

- **Assignment step:** O(n · k · d) - compute distance from each point to each centroid
- **Optimization step:** O(n · d) - calculate new centroids
- **Per iteration:** O(n · k · d)
- **Total:** Multiply by t iterations

###### Bottleneck

Computing distances between all point-representative pairs in the assignment step

---

#### K-Medians Algorithm

###### Time Complexity

**O(n · k · t · d) + sorting overhead**

###### Key Difference from K-Means

- Similar structure to K-Means
- **Additional cost:** Finding median requires sorting for each dimension
- Sorting each dimension: O(n log n) per cluster per dimension
- **Optimization step:** O(k · d · n log n)
- **Slower than K-Means** due to median calculation

###### Bottleneck

Median computation requires sorting values in each dimension for each cluster

---

#### K-Medoids Algorithm

###### Time Complexity

**O(k · (n-k)² · t)** or **O(n² · k · t)** in worst case

###### Breakdown

- Uses hill-climbing strategy
- At each iteration: try multiple point exchanges
- **Exchange evaluation:** For each candidate swap, recalculate objective function
- Must evaluate O((n-k) · k) possible swaps per iteration
- Each swap evaluation costs O(n)
- **Much more expensive than K-Means**

###### Bottleneck

Evaluating multiple point exchanges and recalculating distances for each potential swap

---

#### DBSCAN Algorithm

###### Time Complexity

**Worst case: O(n²)** **With spatial indexing: O(n · log(n))**

###### Breakdown

- **Major bottleneck:** Finding neighbors within distance Eps for each point
- Without optimization: O(n) neighbors check × n points = O(n²)
- **With spatial index** (R-tree, KD-tree): O(log n) per neighbor query
- Total with indexing: O(n · log n)

###### Space Complexity

- O(n) for storing point labels and visited status
- Additional O(n · log n) for spatial index structures

###### Key Factor

Complexity heavily depends on whether spatial indexing is used

---

#### Grid-Based Algorithms

###### Time Complexity

**O(n + p^d)**

Where:

- p = number of grid intervals per dimension
- d = number of dimensions

###### Breakdown

1. **Grid assignment:** O(n) - assign each point to grid cell
2. **Dense cell identification:** O(p^d) - check density of each cell
3. **Connected components:** O(p^d) - graph traversal (BFS/DFS)

###### Space Complexity

**O(p^d)** - store grid structure

###### Critical Issue

**Curse of dimensionality:** p^d grows exponentially with d

- Example: p=10, d=5 → 10^5 = 100,000 cells
- Example: p=10, d=10 → 10^10 = 10 billion cells
- **Becomes computationally infeasible in high dimensions**

---

#### DENCLUE Algorithm

###### Time Complexity

**O(n²)** for naive implementation **O(n · log n)** with spatial indexing

###### Breakdown

- **Kernel density estimation:** Sum influence of all points for each point
- Naive: O(n) calculations per point × n points = O(n²)
- With spatial indexing: only consider nearby points
- **Gradient ascent:** Additional iterations for each point to find density attractors

###### Bottleneck

Computing kernel density requires considering influence from many/all points

---

#### EM Algorithm (Probabilistic Model-Based)

###### Time Complexity

**O(n · k · t · d)**

###### Breakdown

- **E-step:** O(n · k · d) - calculate probability of each point belonging to each cluster
- **M-step:** O(n · k · d) - update parameters (means, covariances)
- **Per iteration:** O(n · k · d)
- **Additional cost:** Evaluating probability density functions more expensive than simple distances

###### Key Point

Similar to K-Means in structure but with more expensive probability computations per operation

---

#### Complexity Comparison Summary

|Algorithm|Time Complexity|Space Complexity|Main Bottleneck|
|---|---|---|---|
|**K-Means**|O(n·k·t·d)|O(n+k·d)|Distance calculations|
|**K-Medians**|O(n·k·t·d) + sorting|O(n+k·d)|Median computation|
|**K-Medoids**|O(n²·k·t)|O(n)|Swap evaluations|
|**DBSCAN**|O(n²) or O(n·log n)|O(n)|Neighbor queries|
|**Grid-Based**|O(n + p^d)|O(p^d)|Exponential growth|
|**DENCLUE**|O(n²) or O(n·log n)|O(n)|Kernel density|
|**EM Algorithm**|O(n·k·t·d)|O(n·k)|Probability calcs|

---

#### Scalability Analysis

###### Best for Large Datasets (n >> k)

1. **K-Means** - linear in n, most scalable
2. **Grid-Based** - if d is small
3. **K-Medians** - slightly slower than K-Means

###### Poor for Large Datasets

1. **K-Medoids** - quadratic in n
2. **DBSCAN** - quadratic without indexing
3. **DENCLUE** - quadratic without indexing

###### High-Dimensional Challenges

- **Grid-Based:** Exponential growth p^d → **infeasible**
- **DBSCAN:** Distance calculations become less meaningful
- **All algorithms:** Curse of dimensionality affects distance-based methods

---

#### Optimization Techniques

###### For Distance-Based Algorithms

- **Spatial indexing:** R-trees, KD-trees, ball trees
- Reduces O(n²) → O(n·log n) for neighbor queries
- Essential for DBSCAN, DENCLUE on large datasets

###### For Representative-Based

- **Smart initialization:** K-Means++ reduces iterations (t)
- **Early stopping:** Convergence criteria to reduce t
- **Mini-batch:** Process subsets for very large n

###### For K-Medoids

- **Sampling:** Use subset of points for swap evaluation
- **PAM variants:** CLARA, CLARANS for large datasets

---

#### Practical Considerations

**When n is large (millions of points):**

- Use K-Means or optimized DBSCAN with indexing
- Avoid K-Medoids

**When d is large (high dimensions):**

- Avoid grid-based methods
- Dimensionality reduction preprocessing recommended

**When k is large:**

- All representative-based methods suffer
- Consider hierarchical approaches

**When accuracy > speed:**

- K-Medoids acceptable for small-medium datasets
- EM algorithm for probabilistic interpretation

## 6. Explain how to assess clustering quality 

#### General Principles

**What Makes Good Clustering?**

- **High intra-class similarity:** Points within same cluster are very similar (cohesive)
- **Low inter-class similarity:** Points in different clusters are dissimilar (distinctive)

**Quality Depends On:**

1. Similarity/distance measure used
2. Algorithm implementation
3. Ability to discover hidden patterns

---

#### Quality Assessment Methods

##### 1. Sum of Squared Distances to Centroids (SSE)

**Formula:** SSE = Σ (distance from point to its cluster representative)²

**How It Works:**

- Calculate squared distance from each point to its cluster centroid
- Sum over all points in all clusters
- **Lower values = better clustering**

**Characteristics:**

- ✅ Simple to compute and understand
- ✅ Suitable for representative-based methods (K-Means)
- ❌ Favors spherical clusters
- ❌ Always decreases as k increases (can't compare different k values)
- ❌ Not suitable for arbitrary-shaped clusters

---

##### 2. Intra-cluster to Inter-cluster Distance Ratio

**Formula:** Ratio = Average intra-cluster distance / Average inter-cluster distance

**How It Works:**

1. Sample pairs of points from dataset D
2. **Set P:** Pairs in same cluster
3. **Set Q:** Pairs in different clusters
4. Calculate average distances for P and Q
5. Compute ratio: Intra/Inter

**Interpretation:**

- **Lower values = better clustering**
- Closer points within clusters, farther points between clusters

**Characteristics:**

- ✅ Considers both cohesion and separation
- ✅ Works for different cluster shapes
- ✅ Intuitive interpretation
- ❌ Requires sampling for large datasets

---

##### 3. Silhouette Coefficient

**Formula for Point i:** **s(i) = (b(i) - a(i)) / max(a(i), b(i))**

Where:

- **a(i)** = D_avg-in = average distance from point i to all other points in its cluster
- **b(i)** = D_min-out = minimum average distance from point i to points in other clusters

**Range and Interpretation:**

- **Range:** -1 to +1
- **+1:** Point well-matched to its cluster (excellent)
- **0:** Point on border between clusters (ambiguous)
- **Negative:** Point likely in wrong cluster (poor)

**Overall Clustering:**

- Average silhouette score = mean of all individual coefficients
- Higher average = better clustering

**Characteristics:**

- ✅ Most comprehensive measure
- ✅ Provides per-point and overall assessment
- ✅ Detects misclassified points
- ✅ Can compare different k values
- ❌ Computationally expensive O(n²)

---

#### Quality Measures Comparison Table

|Measure|Range|Better Value|Best For|Limitation|
|---|---|---|---|---|
|**SSE**|[0, ∞)|Lower|K-Means, quick check|Spherical bias, k comparison|
|**Intra/Inter Ratio**|[0, ∞)|Lower|Balanced assessment|Sampling required|
|**Silhouette**|[-1, +1]|Higher|Comprehensive evaluation|Expensive O(n²)|

---

#### When to Use Each Measure

**Use SSE when:**

- Working with K-Means or similar algorithms
- Need quick assessment
- Clusters are roughly spherical
- Comparing different initializations with same k

**Use Intra/Inter Ratio when:**

- Want balanced cohesion/separation view
- Arbitrary cluster shapes
- Can afford sampling

**Use Silhouette when:**

- Need most reliable quality assessment
- Want to identify poorly clustered points
- Comparing different k values
- Dataset is not too large

---

#### Practical Assessment Strategy

**Step-by-Step:**

1. **Visual Inspection** (if d ≤ 3)
    
    - Plot clusters in 2D/3D
    - Check separation and cohesion
2. **Quick Check (SSE)**
    
    - Initial assessment
    - Algorithm tuning
3. **Comprehensive Evaluation (Silhouette)**
    
    - Final clustering validation
    - Identify problem points (negative scores)
    - Compare different k values
4. **Domain Validation**
    
    - Do clusters make sense?
    - Are representatives interpretable?
    - Match domain knowledge?

---

#### Choosing Optimal k

**Elbow Method:**

- Plot quality measure vs k
- Look for "elbow" where improvement slows
- Sharp drops indicate natural k value

**Trade-offs:**

- **Higher k:** Lower SSE, risk of over-fragmentation
- **Lower k:** May miss natural groupings, better interpretability

---

#### Key Challenges

**The k Selection Problem:**

- No single perfect k value
- Requires interpretation and domain knowledge
- Multiple measures provide different perspectives

**Context Dependency:**

- Quality depends on clustering goal
- Same result may be good for one task, poor for another
- Domain expertise essential

---

#### Summary

**Good clustering achieves:**

- High cohesion within clusters
- High separation between clusters
- Meaningful and interpretable results

**Best practice:**

- Use multiple measures together
- Combine quantitative metrics with visualization
- Validate against domain knowledge
- Consider application-specific requirements