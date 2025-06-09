# INFO-F422 - Statistical Foundations of Machine Learning - Questions and Python Code

## Question 1

**TEST_CL_7**

Let us consider a classification binary task with input x ∈ ℝ and binary output y whose classes are -1 and 1. Which statements are always true if the loss function is not 0/1 and the marginal density of the input is p(x)?

_No Python code for this question - it's a multiple choice theoretical question._

## Question 2

**TEST_CL_12**

Let us consider a classification binary task with input x ∈ ℝ and binary output y whose classes are 0 and 1. Suppose that P(y = 1|x = q) = p

Let us consider the following loss function where CFP and CFN are two positive quantities.

_No Python code for this question - it's a multiple choice theoretical question with a loss matrix._

## Question 3

**TEST_CL_9**

Let us consider a classification binary task where the two classes are 0 and 1. The task is not separable if and only if (necessary and sufficient condition)

_No Python code for this question - it's a multiple choice theoretical question._

## Question 4

**TEST_CL_2**

Let us consider a classification binary task with input x ∈ ℝ and binary output y whose classes are 0 and 1. Suppose that P(y = 1|x) = 0.4 if x < 0 and P(y = 1|x) = 0.8 if x ≥ 0

Let us consider the following loss function.

_No Python code for this question - it's a multiple choice theoretical question with a loss matrix._

## Question 5

**TEST_CL_8**

Let us consider a classification binary task with input x ∈ ℝ and binary output y whose classes are -1 and 1. Which statements are always true if the loss function is 0/1 and the marginal density of the input is p(x)?

_No Python code for this question - it's a multiple choice theoretical question._

## Question 6

**TEST_CL_6**

Let us consider a classification binary task with input x ∈ ℝ and binary output y whose classes are -1 and 1. Which statements are always true if the loss function is not 0/1?

_No Python code for this question - it's a multiple choice theoretical question._

## Question 7

**TEST_CL_BA_6**

Let us consider a binary classification task where the target class y takes value in {−1, 1}, the input is the r.v. x and the inverse conditional distributions of x are • p(x|y = 1) is Normal with mean -1 and variance 1 • p(x|y = −1) is Normal with mean 1 and variance 1

Suppose that the class 1 has an a priori probability equal to 0.95. By using the Bayes' theorem compute the class returned by the Bayes classifier for

_No Python code for this question - it has R code only._

## Question 8

**TEST_CL_BA_PY_1**

Let us consider a binary classification task where the target class y takes value in {−1, 1}, the input is the r.v. x and the inverse conditional distributions of x are 

• p(x|y = 1) is Uniform in [-1,1] 
• p(x|y = −1) is Uniform in [0,1]

Suppose that the two classes have the same a priori probability. By using Bayes' theorem and Python compute

```python
import scipy.stats

x = 1

# a priori probabilities
p_m = 1/2
p_p = 1 - p_m

# inverse probabilities
px_p = scipy.stats.uniform.pdf(x, loc=-1, scale=2)  # Uniform between -1 and 1
# p (x=0| y=1)
px_m = scipy.stats.uniform.pdf(x, loc=0, scale=1)  # Uniform between 0 and 1

# conditional probabilities
py_p = px_p * p_p / (px_p * p_p + px_m * p_m)
# p (y=1|x)
py_m = px_m * p_m / (px_p * p_p + px_m * p_m)

# p (y=-1|x)
print(f"x={x} P(y=1|x)={py_p} P(y=-1|x)={py_m}")

rm(list=ls())
x=1

# a priori probabilities
p.m=1/2
p.p=1-p.m

# inverse probabilities
px.p=dunif(x,-1,1)
# p (x=0| y=1)
px.m=dunif(x,0,1)

# conditional probabilities
py.p=px.p*p.p/(px.p*p.p+px.m*p.m)
# p (y=1|x)
py.m=px.m*p.m/(px.p*p.p+px.m*p.m)
# p (y=-1|x)

cat("x=",x," P(y=1|x)=",py.p, " P(y=-1|x)=",py.m,"\n")
```

## Question 9

**TEST_CL_BA_8**

Let us consider a binary classification task where the target class y takes value in {−1, 1}, the input is the vector [x₁, x₂]ᵀ and the inverse conditional distributions of x are • p(x|y = 1) is Normal bivariate with mean [−1, −1]ᵀ and covariance matrix S • p(x|y = −1) = w₁p₁ + w₂p₂ is a mixture of two Normal bivariate distributions p₁ and p₂ with means μ₁ = [1, 1]ᵀ μ₂ = [0, 0]ᵀ and covariance matrix S

where w₁ = w₂ = 0.5 and S = [2 1; 1 2]

Suppose that the class 1 has an a priori probability equal to 0.75. By using the Bayes' theorem compute the class returned by the Bayes classifier for

_No Python code for this question - it has R code only._

## Question 10

**TEST_CL_AL_PY_3**

Let us consider a binary classification task where the target class y takes value in {−1, 1}, the input is the r.v. x ∈ ℝ² and the observed dataset in contained in the variable Q6.G1.D of the .Rdata file.

By using a Naive Bayes approach and assuming that the conditional distributions are Normal, compute the predicted class for

```python
import pyreadr
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import norm

# Load the R data file
result = pyreadr.read_r("EXAM.Rdata")
D = result["Q6.G1.D"].values

# Extract indices for classes 1 and -1
Ip = np.where(D[:, 2] == 1)[0]
In = np.where(D[:, 2] == -1)[0]

classes = ["-1", "1"]

N = D.shape[0]
n = 2

# Extract the first two columns
X = D[:, :n]

# Plotting the classes
plt.scatter(X[Ip, 0], X[Ip, 1], color="green", label="Class 1")
plt.scatter(X[In, 0], X[In, 1], color="red", label="Class -1")
plt.xlabel("Feature 1")
plt.ylabel("Feature 2")
plt.legend()
plt.show()

# Calculate prior probabilities
Pp = len(Ip) / N
Pn = 1 - Pp

# Calculate variances
Sigmap = np.var(X[Ip, :], axis=0, ddof=1)
Sigman = np.var(X[In, :], axis=0, ddof=1)

# Calculate means
mup = np.mean(X[Ip, :], axis=0)
mun = np.mean(X[In, :], axis=0)

def dest(x, X_values):
    """Calculate the probability density of x given the mean and standard deviation of X_values."""
    return norm.pdf(x, loc=np.mean(X_values), scale=np.std(X_values, ddof=1))

# Test samples
Xts = np.array([[0, 0], [1, 1], [-1, -1], [2, 2]])

for x in Xts:
    NBp = dest(x[0], X[Ip, 0]) * dest(x[1], X[Ip, 1]) * Pp
    NBn = dest(x[0], X[In, 0]) * dest(x[1], X[In, 1]) * Pn
    prediction = classes[np.argmax([NBn, NBp])]
    print(f"Prediction in x={x} is {prediction}")
```

## Question 11

**TEST_CL_AL_PY_4**

Let us consider a binary classification task where the target class y takes value in {−1, 1}, the input is the r.v. x ∈ ℝ² and the observed dataset in contained in the variable Q6.G1.D of the .Rdata file.

By using the formula (7.2.67) of the Handbook and the Python command numpy.cov to compute the covariances, compute the class returned by the discriminant function approach in the following points

```python
import pyreadr
import numpy as np
import matplotlib.pyplot as plt
import math

# Load the RData file
result = pyreadr.read_r("EXAM.Rdata")
D = result["Q6.G1.D"].values

# Find indices where the third column is 1 and -1
Ip = np.where(D[:, 2] == 1)[0]
In_ = np.where(D[:, 2] == -1)[0]

classes = ["-1", "1"]

N = D.shape[0]

n = 2

X = D[:, 0:n]

# Plot the points
plt.scatter(X[Ip, 0], X[Ip, 1], color='green')
plt.scatter(X[In_, 0], X[In_, 1], color='red')
plt.show()

Pp = len(Ip) / N
Pn = 1 - Pp

Sigmap = np.cov(X[Ip,:].T)
Sigman = np.cov(X[In_,:].T)

mup = np.mean(X[Ip,:], axis=0)
mun = np.mean(X[In_,:], axis=0)

Xts = np.array([[0, 0], [1, 1], [-1, -1], [2, 2]])

for x in Xts:
    diff_p = x - mup
    diff_n = x - mun
    gp = (-0.5 * np.dot(np.dot(diff_p.T, np.linalg.inv(Sigmap)), diff_p)
          - n / 2 * math.log(2 * math.pi)
          - 0.5 * math.log(np.linalg.det(Sigmap))
          + math.log(Pp))
    
    gn = (-0.5 * np.dot(np.dot(diff_n.T, np.linalg.inv(Sigman)), diff_n)
          - n / 2 * math.log(2 * math.pi)
          - 0.5 * math.log(np.linalg.det(Sigman))
          + math.log(Pn))
    
    prediction = classes[np.argmax([gn, gp])]
    print(f"Prediction in x={x} is {prediction}")
```

## Question 12

**TEST_CL_AS_PY_2**

Let us consider a binary classification task where the target class y takes value in {−1, 1}, the input is the r.v. x ∈ ℝ² and the observed dataset is contained in the variable Q6.G1.D of the .Rdata file.

By using a Naive Bayes approach and assuming that the conditional distributions are Normal, compute for the training set the number of:

- True Positives
- False Positives
- True Negatives
- False Negatives

```python
import pyreadr
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import norm

# Load the R data file
result = pyreadr.read_r("EXAM.Rdata")

# Assign D to the loaded data object Q6.G1.D
D = result["Q6.G1.D"].values

n = 2

# Extract the first n columns as X and the (n+1)th column as Y
X = D[:, :-1]
Y = D[:, -1]

# Find indices where Y == 1 and Y == -1
Ip = np.where(Y == 1)[0]
In_ = np.where(Y == -1)[0]

classes = ["-1", "1"]

N = D.shape[0]

# Plot the data points
plt.scatter(X[Ip, 0], X[Ip, 1], color="green", label="1")
plt.scatter(X[In_, 0], X[In_, 1], color="red", label="-1")
plt.legend()
plt.show()

# Calculate prior probabilities
Pp = len(Ip) / N
Pn = 1 - Pp

# Calculate variance matrices
Sigmap = np.var(X[Ip, :], axis=0, ddof=1)
Sigman = np.var(X[In_, :], axis=0, ddof=1)

# Calculate mean vectors
mup = np.mean(X[Ip, :], axis=0)
mun = np.mean(X[In_, :], axis=0)

def dest(x, data):
    """
    Calculate the normal density of x given the mean and standard deviation of data.
    """
    return norm.pdf(x, loc=np.mean(data), scale=np.std(data, ddof=1))

TP = 0
FP = 0
TN = 0
FN = 0

for i in range(N):
    x = X[i, :]
    y = Y[i]
    
    NBp = dest(x[0], X[Ip, 0]) * dest(x[1], X[Ip, 1]) * Pp
    NBn = dest(x[0], X[In_, 0]) * dest(x[1], X[In_, 1]) * Pn
    
    if NBp > NBn:
        if y > 0:
            TP += 1
        else:
            FP += 1
    elif NBp < NBn:
        if y < 0:
            TN += 1
        else:
            FN += 1

print(f"TP={TP} FP={FP} TN={TN} FN={FN}")
```

## Question 13

**TEST_CL_AS_6**

Let us consider a classification task both the input x and the target y are binary random variables and the related conditional probability table is

_No Python code for this question - it's a theoretical question with probability tables._

## EXAM Question (Description)

**EXAM,2021,1s,Q5,1,PY**

Consider the R dataframe Q6.G1.D in EXAM_2021_1s.Rdata that contains the observed dataset of a binary classification task where target is y ∈ {−1, 1} and the input x ∈ ℝ².

The student should:

1. trace the ROC curve of a Naive Bayes classifier using a Normal approximation of the conditional density.
2. trace the ROC curve of the classifier c(x) = {1 if x₁ > h, -1 else} for different values of h.
3. choose the best classifier on the basis of the ROC curve.
4. in the following question, copy the R code used for tracing the ROC curves
5. in the following question, upload a pdf file showing the 2 ROC curves and justifying the choice of the classifier.

```python
import numpy as np
import matplotlib.pyplot as plt
import pyreadr

# Load the R data file
result = pyreadr.read_r("EXAM_2021_1s.Rdata")
# Assuming Q6.G1.D is stored in the Rdata file
D = result["Q6.G1.D"]  # Adjust the key based on actual data structure

def NB(X, Y):
    N = len(Y)
    n = X.shape[1]
    Yhat = np.zeros(N)
    I1 = np.where(Y == 1)[0]
    I0 = np.where(Y == -1)[0]
    p1 = len(I1) / N
    p0 = 1 - p1
    
    for i in range(N):
        p1x = 1
        p0x = 1
        for j in range(n):
            p1x *= np.exp(-0.5 * ((X[i, j] - np.mean(X[I1, j])) ** 2 / (2 * np.var(X[I1, j])) / np.sqrt(2 * np.pi * np.var(X[I1, j]))))
            p0x *= np.exp(-0.5 * ((X[i, j] - np.mean(X[I0, j])) ** 2 / (2 * np.var(X[I0, j])) / np.sqrt(2 * np.pi * np.var(X[I0, j]))))
        
        Yhat[i] = p1x * p1 / (p1x * p1 + p0x * p0)
    
    return Yhat

N = 50
n = 2

X = D.iloc[:, 0:2].values
Y = D.iloc[:, 2].values

N1 = np.sum(Y == 1)
N0 = np.sum(Y == -1)
Yhat1 = X[:, 0]
Yhat2 = NB(X, Y)

# Sort indices
s1_idx = np.argsort(Yhat1)
s2_idx = np.argsort(Yhat2)

TPR1 = []
FPR1 = []
TPR2 = []
FPR2 = []

for i in range(1, N+1):
    I1 = s1_idx[:i]
    remaining_indices1 = np.setdiff1d(np.arange(N), I1)
    TPR1.append(np.sum(Y[remaining_indices1] == 1) / N1)
    FPR1.append(np.sum(Y[remaining_indices1] == -1) / N0)
    
    I2 = s2_idx[:i]
    remaining_indices2 = np.setdiff1d(np.arange(N), I2)
    TPR2.append(np.sum(Y[remaining_indices2] == 1) / N1)
    FPR2.append(np.sum(Y[remaining_indices2] == -1) / N0)

# Plotting
plt.figure()
plt.plot(FPR1, TPR1, 'yellow', label='TH')
plt.plot(FPR1, FPR1, '--')  # diagonal line
plt.plot(FPR2, TPR2, 'black', label='NB')
plt.xlabel('FPR')
plt.ylabel('TPR')
plt.title('ROC curves')
plt.legend(loc='center right')
plt.show()
```

## Question 14

**EXAM,2021,1s,Q5,1,PY**

Consider the R dataframe Q5.G1.D in EXAM_2021_1s.Rdata

It contains the observed dataset of a binary classification task where target is y ∈ {−1, 1} and the input x ∈ ℝ².

Let us consider the following three classifiers:

1. {1 if x₁ > 0, -1 else}
2. {1 if x₁x₂ < 0, -1 else}
3. {1 if x₂ > 0, -1 else}

Fill the confusion matrix of each classifier and determine which is the most accurate classifier in terms of:

- Misclassification Rate
- Balanced Misclassification Rate
- Precision
- True Positive Rate
- True Negative Rate

```python
import numpy as np
import pyreadr

# Load the R data file
data = pyreadr.read_r("EXAM_2021_1s.Rdata")
# Assuming Q5.G1.D is stored in the Rdata file
D = data["Q5.G1.D"]  # Adjust the key based on actual data structure

def assess(Y, Yhat):
    if len(Y) != len(Yhat):
        raise ValueError("wrong sizes")
    
    TP = np.sum((Y == 1) & (Yhat == 1))
    FP = np.sum((Y == -1) & (Yhat == 1))
    TN = np.sum((Y == -1) & (Yhat == -1))
    FN = np.sum((Y == 1) & (Yhat == -1))
    
    P = np.sum(Y == 1)
    N = np.sum(Y == -1)
    Phat = np.sum(Yhat == 1)
    Nhat = np.sum(Yhat == -1)
    
    return {
        'TP': TP, 'FP': FP, 'TN': TN, 'FN': FN,
        'TPR': TP/P, 'FPR': FP/N, 'TNR': TN/N, 'PR': TP/Phat,
        'ER': (FP+FN)/(P+N),
        'BER': 0.5*(FP/(TN+FP)+FN/(FN+TP))
    }

# Extract features and target
X = D.iloc[:, 0:2]
Y = D.iloc[:, 2]
N = X.shape[0]
n = X.shape[1]

# Create classifiers
C1hat = np.full(N, -1)
C1hat[X.iloc[:, 0] > 0] = 1

C2hat = np.full(N, -1)
C2hat[(X.iloc[:, 0] * X.iloc[:, 1]) <= 0] = 1

C3hat = np.full(N, -1)
C3hat[X.iloc[:, 1] < 0] = 1

# Initialize lists for metrics
PR = []
ER = []
BER = []
TPR = []
TNR = []

# Evaluate classifiers
classifiers = [C1hat, C2hat, C3hat]
for i, chat in enumerate(classifiers, 1):
    ASSC = assess(Y, chat)
    TP = ASSC['TP']
    FP = ASSC['FP']
    FN = ASSC['FN']
    TN = ASSC['TN']
    
    ER.append(ASSC['ER'])
    BER.append(ASSC['BER'])
    PR.append(ASSC['PR'])
    TPR.append(ASSC['TPR'])
    TNR.append(ASSC['TNR'])
    
    print(f"Classifier {i}:")
    print(np.array([[TP, FP], [FN, TN]]))

# Print results
print(f"\n which.min(ER)= {np.argmin(ER)+1},")
print(f"\n which.min(BER)= {np.argmin(BER)+1},")
print(f"\n which.max(PR)= {np.argmax(PR)+1},")
print(f"\n which.max(TPR)= {np.argmax(TPR)+1},")
print(f"\n which.max(TNR)= {np.argmax(TNR)+1},")
print(f"TNR= {TNR}\n")
```

## Question 15

**EXAM,2122,2s,Q4,1,PY**

Consider the dataframe Q4.G1.D (in EXAM_2122_2s.Rdata) that contains the observed dataset of a binary classification task where the target is y ∈ {−1, 1} and the input x ∈ ℝ².

The student should:

1. trace the ROC curves of a Nearest Neighbour Classifier with 5 neighbours (5NN) and a Nearest Neighbour Classifier with 10 neighbours (10NN)
2. trace the Precision/Recall curves of a Nearest Neighbour Classifier with 5 neighbours (5NN) and a Nearest Neighbour Classifier with 10 neighbours (10NN)
3. select the best classifier on the basis of the ROC curve and justify the answer
4. select the best classifier on the basis of the PR curve and justify the answer
5. provide the R code used for tracing the ROC and the PR curves
6. upload the images of the ROC and the PR curves
7. explain clearly how you estimate the conditional posterior probability of the target.

You should use the same dataset for both training and testing the classifier.

```python
import numpy as np
import matplotlib.pyplot as plt
import pyreadr
import os

# Load the R data
# Assuming the .Rdata file is in the current directory

result = pyreadr.read_r("EXAM_2122_2s.Rdata")
Q4_G2_D = result["Q4.G2.D"].values

np.random.seed(0)

X = Q4_G2_D[:, :2]
Y = Q4_G2_D[:, 2]

I1 = np.where(Y == 1)[0]
I0 = np.where(Y == -1)[0]

N1 = len(I1)
N0 = len(I0)
N = len(Y)
Yhat1 = np.zeros(N)
Yhat2 = np.zeros(N)

def KNN(X_train, Y_train, x_test, k):
    """KNN classifier function"""
    distances = np.sqrt(np.sum((X_train - x_test)**2, axis=1))
    k_nearest_indices = np.argsort(distances)[:k]
    k_nearest_labels = Y_train[k_nearest_indices]
    return np.mean(k_nearest_labels)

for i in range(N):
    Yhat1[i] = KNN(X, Y, X[i], 5)
    Yhat2[i] = KNN(X, Y, X[i], 10)

# Sort indices for both predictions
s1_idx = np.argsort(Yhat1)[::-1]
s2_idx = np.argsort(Yhat2)[::-1]

TPR1, FPR1, PR1 = [], [], []
TPR2, FPR2, PR2 = [], [], []

for i in range(1, N + 1):
    I1_subset = s1_idx[:i]
    TPR1.append(np.sum(Y[I1_subset] == 1) / N1)
    FPR1.append(np.sum(Y[I1_subset] == -1) / N0)
    PR1.append(np.sum(Y[I1_subset] == 1) / i)
    
    I2_subset = s2_idx[:i]
    TPR2.append(np.sum(Y[I2_subset] == 1) / N1)
    FPR2.append(np.sum(Y[I2_subset] == -1) / N0)
    PR2.append(np.sum(Y[I2_subset] == 1) / i)

# Create subplots
fig, (ax1, ax2, ax3) = plt.subplots(1, 3, figsize=(15, 5))

# First plot
ax1.scatter(X[I1, 0], X[I1, 1])
ax1.scatter(X[I0, 0], X[I0, 1], color='red')

# Second plot (ROC curve)
ax2.plot(FPR1, TPR1, color='yellow', label='KNN5')
ax2.plot(FPR1, FPR1, '--')  # diagonal line
ax2.plot(FPR2, TPR2, color='black', label='KNN10')
ax2.set_xlabel('FPR')
ax2.set_ylabel('TPR')
ax2.set_title('ROC curves')
ax2.legend(loc='lower right', fontsize=7, ncol=3)

# Third plot (Precision-Recall curve)
ax3.plot(TPR1, PR1, color='yellow', label='KNN5')
ax3.plot(TPR2, PR2, color='black', label='KNN10')
ax3.set_xlabel('Recall')
ax3.set_ylabel('Precision')
ax3.set_title('PR curves')
ax3.legend(loc='lower left', fontsize=7, ncol=3)

plt.tight_layout()
plt.show()
```