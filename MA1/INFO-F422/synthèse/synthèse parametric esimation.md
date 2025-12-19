# Summary: Parametric Estimation

This document provides an in-depth overview of parametric estimation, a core concept in statistical machine learning. It covers the process of estimating unknown parameters of a probability distribution based on observed data.

## 1. Introduction to Parametric Estimation

* **Goal:** Given a random variable (r.v.) $z$ whose probability distribution $F_z(z)$ is unknown but can be expressed in a parametric form $F_z(z, \theta)$ (where $\theta$ is an unknown, constant parameter), the goal is to find an estimate $\hat{\theta}$ of this parameter. This estimate should make the parameterized distribution $F_z(z, \hat{\theta})$ closely match the true underlying distribution $F_z(z)$.
* **Assumptions:**
    * The form of the probability distribution $F_z(z, \theta)$ is known, but the specific value of the parameter $\theta$ (which belongs to a parameter space $\Theta$) is not.
    * We have a sample $D_N$ consisting of $N$ independent and identically distributed (i.i.d.) measurements of $z$.
* **I.I.D. Samples:**
    * **Identically Distributed:** All observations are drawn from the same underlying (but fixed) probability distribution.
        * $Prob\{z_i = z\} = Prob\{z_j = z\}$ for all observations $i, j$ and any value $z$.
    * **Independently Distributed:** The value of one observation does not influence the probability of observing the value of another.
        * $Prob\{z_j = z | z_i = z_i'\} = Prob\{z_j = z\}$.
* **Outcomes of Estimation:**
    * **Point Estimation:** Provides a single specific value $\hat{\theta}$ as the estimate for $\theta$.
    * **Interval Estimation:** Provides a region (interval) within the parameter space $\Theta$ that is likely to contain the true value of $\theta$.

## 2. Point Estimation

A point estimator is a function $\hat{\theta} = h(D_N)$ that maps the observed dataset $D_N$ to a single value in the parameter space $\Theta$.

### Methods of Constructing Estimators

The document focuses on two main methods:

#### a. Plug-in Principle

* **Core Idea:** If the parameter $\theta$ can be expressed as a functional $t(F)$ of the true distribution $F$, the plug-in estimate $\hat{\theta}$ is obtained by applying the same functional to the empirical distribution function $\hat{F}(z)$: $\hat{\theta} = t(\hat{F}(z))$.
* **Empirical Distribution Function ($\hat{F}_z(z)$):**
    * Defined as $\hat{F}_z(z) = \frac{\text{Number of samples } z_i \le z}{N}$.
    * It's a staircase function that approximates the true cumulative distribution function (CDF) based on the observed sample.
* **Examples of Plug-in Estimators:**
    * **Sample Average ($\hat{\mu}$):** If $\theta = E[z] = \int z dF(z)$, then the plug-in estimate is $\hat{\mu} = \int z d\hat{F}(z) = \frac{1}{N}\sum_{i=1}^{N}z_i$.
    * **Sample Variance ($\hat{\sigma}^2$):** The plug-in estimate for variance $\sigma^2$ is $\hat{\sigma}^2 = \frac{1}{N-1}\sum_{i=1}^{N}(z_i - \hat{\mu})^2$. The denominator is $(N-1)$ to ensure unbiasedness (Bessel's correction).
    * **Other Examples:** Sample skewness, sample upper critical point, sample correlation.

#### b. Maximum Likelihood (Covered in Section 4)

### Sampling Distribution

* Since the dataset $D_N$ is a realization of a random process, the point estimator $\hat{\theta} = h(D_N)$ is also a random variable.
* The **sampling distribution** is the probability distribution of this estimator $\hat{\theta}$.
* It's a theoretical concept that describes how the estimate $\hat{\theta}$ would vary if we were to repeatedly draw different datasets $D_N$ of the same size $N$ from the true underlying distribution and compute $\hat{\theta}$ for each.
* **Monte Carlo Illustration:** The concept can be illustrated by:
    1.  Generating R random datasets $D_N^{(r)}$ from a known $F_z(z, \theta)$.
    2.  Computing the estimate $\hat{\theta}^{(r)}$ for each dataset.
    3.  Plotting a histogram of these R estimates to visualize the sampling distribution.
    4.  Analyzing statistics (mean, variance) of these estimates.

## 3. Properties of Point Estimators

Key properties to evaluate the quality of an estimator $\hat{\theta}$:

### a. Bias

* **Definition:** An estimator $\hat{\theta}$ is **unbiased** if its expected value (over all possible datasets $D_N$) is equal to the true parameter $\theta$: $E_{D_N}[\hat{\theta}] = \theta$.
* **Bias:** If not unbiased, the bias is $Bias[\hat{\theta}] = E_{D_N}[\hat{\theta}] - \theta$.
* **Notes:**
    * An unbiased estimator "on average" gives the correct value.
    * If $\hat{\theta}$ is unbiased for $\theta$, $f(\hat{\theta})$ is not necessarily unbiased for $f(\theta)$.
    * Sample average $\hat{\mu}$ is an unbiased estimator of the population mean $\mu$.
    * Sample variance $\hat{\sigma}^2 = \frac{1}{N-1}\sum(z_i - \hat{\mu})^2$ is an unbiased estimator of the population variance $\sigma^2$.

### b. Variance

* **Definition:** The variance of an estimator $\hat{\theta}$ is the variance of its sampling distribution: $Var[\hat{\theta}] = E_{D_N}[(\hat{\theta} - E_{D_N}[\hat{\theta}])^2]$.
* It measures the precision of the estimator – how much the estimates $\hat{\theta}$ tend to vary from one sample to another.
* **Variance of Sample Average:** $Var[\hat{\mu}] = \frac{\sigma^2}{N}$, where $\sigma^2$ is the variance of the underlying distribution. This shows that the variance of the sample average decreases as the sample size $N$ increases.
* **Standard Error:** The standard deviation of the estimator, $\sqrt{Var[\hat{\theta}]}$, often called the standard error. For $\hat{\mu}$, it's $\sigma/\sqrt{N}$.

### c. Mean Squared Error (MSE)

* **Definition:** $MSE = E_{D_N}[(\theta - \hat{\theta})^2]$. It measures the average squared difference between the estimator and the true parameter.
* **Bias-Variance Decomposition:** A fundamental result states that $MSE = (Bias[\hat{\theta}])^2 + Var[\hat{\theta}]$.
    * This means the MSE combines both the bias (accuracy) and variance (precision) of an estimator.
    * There's often a trade-off: reducing bias might increase variance, and vice-versa.

### d. Sampling Distributions for Gaussian Random Variables

If $z_1, ..., z_N$ are i.i.d. from $\mathcal{N}(\mu, \sigma^2)$:
* $\hat{\mu} = \frac{1}{N}\sum z_i \sim \mathcal{N}(\mu, \sigma^2/N)$.
    * Thus, $\frac{\hat{\mu}-\mu}{\sigma/\sqrt{N}} \sim \mathcal{N}(0,1)$ (standard normal).
* $\frac{\sqrt{N}(\hat{\mu}-\mu)}{\hat{\sigma}} \sim \mathcal{T}_{N-1}$ (Student's t-distribution with $N-1$ degrees of freedom), where $\hat{\sigma}^2 = \frac{1}{N-1}\sum (z_i - \hat{\mu})^2$.

## 4. Maximum Likelihood Estimation (MLE)

A powerful method for constructing estimators.

### a. Likelihood Function

* Given a probability density function (pdf) or probability mass function (pmf) $p_z(z, \theta)$ and an i.i.d. sample $D_N = \{z_1, ..., z_N\}$.
* The **likelihood function** $L_N(\theta)$ is the joint probability (density) of observing the given sample $D_N$, viewed as a function of the parameter $\theta$:
    $L_N(\theta) = p_{D_N}(D_N, \theta) = \prod_{i=1}^{N} p_z(z_i, \theta)$.
* **Rationale:** $L_N(\theta)$ measures how "likely" the observed data are for different values of $\theta$.

### b. Maximum Likelihood Estimator ($\hat{\theta}_{ml}$)

* **Principle:** The MLE $\hat{\theta}_{ml}$ is the value of $\theta$ that maximizes the likelihood function $L_N(\theta)$:
    $\hat{\theta}_{ml} = \arg\max_{\theta \in \Theta} L_N(\theta)$.
* This means $\hat{\theta}_{ml}$ is the parameter value under which the observed data are most probable.
* **Log-Likelihood ($l_N(\theta)$):** It is often easier to work with the natural logarithm of the likelihood function, called the log-likelihood:
    $l_N(\theta) = \log L_N(\theta) = \sum_{i=1}^{N} \log p_z(z_i, \theta)$.
    Since $\log(\cdot)$ is a monotonically increasing function, maximizing $l_N(\theta)$ is equivalent to maximizing $L_N(\theta)$:
    $\hat{\theta}_{ml} = \arg\max_{\theta \in \Theta} l_N(\theta)$.
* **Finding the MLE:**
    * If $l_N(\theta)$ is differentiable, the MLE can often be found by solving $\frac{\partial l_N(\theta)}{\partial \theta} = 0$.
    * One must also check the second derivative condition $\frac{\partial^2 l_N(\theta)}{\partial \theta^2}|_{\hat{\theta}_{ml}} < 0$ to ensure a maximum.
    * **Numerical Methods:** If an analytical solution is not available, iterative numerical optimization techniques (e.g., gradient ascent, Newton-Raphson) are used.

### c. Examples of MLEs

* **Gaussian Distribution:**
    * If $z \sim \mathcal{N}(\mu, \sigma^2)$ and $\sigma^2$ is known, $\hat{\mu}_{ml} = \frac{1}{N}\sum_{i=1}^{N} z_i = \hat{\mu}$ (sample average).
    * If both $\mu$ and $\sigma^2$ are unknown:
        * $\hat{\mu}_{ml} = \frac{1}{N}\sum_{i=1}^{N} z_i = \hat{\mu}$.
        * $\hat{\sigma}_{ml}^2 = \frac{1}{N}\sum_{i=1}^{N} (z_i - \hat{\mu}_{ml})^2$. Note this is different from the unbiased sample variance $\hat{\sigma}^2$ (which has $N-1$ in the denominator). $\hat{\sigma}_{ml}^2$ is biased for small $N$.
* **Uniform Distribution:** If $z \sim \mathcal{U}(0, M)$, then $\hat{M}_{ml} = \max(z_1, ..., z_N)$.
* **Poisson Distribution:** If $z \sim Poisson(\lambda)$, then $\hat{\lambda}_{ml} = \frac{1}{N}\sum_{i=1}^{N} z_i = \hat{\mu}$.

### d. Properties of Maximum Likelihood Estimators

Under certain regularity conditions (and assuming the model is correctly specified):
* **Asymptotically Unbiased:** $\hat{\theta}_{ml}$ becomes unbiased as $N \rightarrow \infty$. (It can be biased for finite $N$, e.g., $\hat{\sigma}_{ml}^2$).
* **Asymptotically Efficient:** For large $N$, $\hat{\theta}_{ml}$ has the smallest possible variance among all asymptotically unbiased estimators (achieves the Cramér-Rao lower bound).
* **Asymptotically Normally Distributed:** The sampling distribution of $\hat{\theta}_{ml}$ approaches a normal distribution as $N \rightarrow \infty$: $\hat{\theta}_{ml} \approx \mathcal{N}(\theta, I(\theta)^{-1})$, where $I(\theta)$ is the Fisher information.
* **Invariance Property:** If $\hat{\theta}_{ml}$ is the MLE of $\theta$, then for any function $g(\theta)$, the MLE of $g(\theta)$ is $g(\hat{\theta}_{ml})$.

## 5. Interval Estimation

Provides a range of plausible values for the parameter $\theta$, rather than a single point.

* **Goal:** To construct an interval $[\underline{\theta}, \overline{\theta}]$ from the data $D_N$ such that it contains the true parameter $\theta$ with a certain high probability.
* **Confidence Interval:** A $100(1-\alpha)\%$ confidence interval for $\theta$ is a random interval $[\underline{\theta}(D_N), \overline{\theta}(D_N)]$ such that:
    $Prob\{\underline{\theta} \le \theta \le \overline{\theta}\} = 1-\alpha$, where $\alpha \in [0,1]$ is the significance level (e.g., $\alpha=0.05$ for a 95% confidence interval).
* **Interpretation:** If we were to repeatedly sample datasets $D_N$ and construct such an interval for each, then approximately $100(1-\alpha)\%$ of these intervals would contain the true, fixed value of $\theta$.
    * **Important:** $\theta$ is fixed (but unknown); the interval is random (depends on $D_N$).
* **Construction (Example using Normal Distribution):**
    * If an estimator $\hat{\theta}$ is unbiased, $Var[\hat{\theta}] = \sigma_{\hat{\theta}}^2$, and its sampling distribution is $\hat{\theta} \sim \mathcal{N}(\theta, \sigma_{\hat{\theta}}^2)$.
    * Then, $Prob\{\theta - z_{\alpha/2}\sigma_{\hat{\theta}} \le \hat{\theta} \le \theta + z_{\alpha/2}\sigma_{\hat{\theta}}\} = 1-\alpha$, where $z_{\alpha/2}$ is the critical value from the standard normal distribution (e.g., 1.96 for $\alpha=0.05$).
    * Rearranging this gives the confidence interval for $\theta$:
        $[\hat{\theta} - z_{\alpha/2}\sigma_{\hat{\theta}}, \hat{\theta} + z_{\alpha/2}\sigma_{\hat{\theta}}]$.
    * If $\sigma_{\hat{\theta}}$ itself depends on unknown parameters (like $\sigma$ in the case of estimating $\mu$), it might be replaced by an estimate (e.g., using $\hat{\sigma}$ and the t-distribution).

This summary covers the main theoretical aspects of parametric estimation as presented in the document, including the definition of the problem, methods for finding point estimates (plug-in, MLE), properties for evaluating these estimates (bias, variance, MSE), and an introduction to interval estimation.