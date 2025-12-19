# Nonparametric Estimation

## 1. Introduction to Estimation of Arbitrary Parameters

* **Context:** Given a sample $D_N$ from a random variable $z$.
* **Simple Case (Mean):** If $\theta = E[z]$, the estimate $\hat{\mu} = \frac{1}{N}\sum_{i=1}^{N}z_{i}$ has:
    * $Bias[\hat{\mu}] = 0$
    * $Var[\hat{\mu}] = \frac{\sigma^{2}}{N}$
    * If the setting is parametric (e.g., normal distribution), the entire distribution of $\hat{\mu}$ is known.
* **Complex Cases:** For other parameters (e.g., skewness, bivariate correlation), it's easy to define an estimator (e.g., plug-in, maximum likelihood), but assessing its accuracy (bias, variance) is difficult.
* **Nonparametric Setting:** In a nonparametric setting, the analytical form of $Var[\hat{\theta}]$ and $Bias[\hat{\theta}]$ for an arbitrary estimator $\hat{\theta}$ is typically unavailable.
* **Definition:** Statistical methods that do not rely on any specific assumption about the form of the probability distribution are called **nonparametric** or **distribution-free**.

## 2. Example: The Patch Data (Efron/Tibshirani book)

* **Scenario:** A pharmaceutical company wants to introduce a new medical patch for hormone infusion. An experimental study with $N=8$ subjects compares a placebo, an 'old' patch, and a 'new' patch.
* **Goal:** Show bioequivalence. The FDA approval criterion is $\theta = \frac{|E(new) - E(old)|}{E(old) - E(placebo)} \le 0.2$.
* **Estimator:** $\hat{\theta} = \frac{|\hat{\mu}_{new} - \hat{\mu}_{old}|}{\hat{\mu}_{old} - \hat{\mu}_{placebo}}$. This is a ratio estimator, which can be biased even if numerator and denominator estimators are unbiased.
* **Data Example:**
    * Let $z = \text{old} - \text{placebo}$ and $y = \text{new} - \text{old}$.
    * The plug-in estimate is $\hat{\theta} = t(\hat{F}) = \frac{|\hat{\mu}_{y}|}{\hat{\mu}_{z}}$. For the given data, $\hat{\theta} = \frac{452.3}{6342} = 0.07$.
* **Question:** Does this satisfy the FDA criterion? What about its accuracy, bias, and variance?

## 3. Example: Vaccine Data

* **Scenario:** Pfizer and BioNTech interim analysis of a Randomized Controlled Trial (RCT) for a COVID-19 vaccine (over 43,000 volunteers).
* **Data:**
    * Vaccinated: 8 infected, 21500-8 non-infected
    * No vaccine: 86 infected, 21500-86 non-infected
* **Efficacy Rate:** $1 - \frac{8}{86} \approx 90\%$.

## 4. The Bootstrap Method

* **Problem:** How to assess the accuracy of an estimator $\hat{\theta}$ and its sampling distribution when the underlying distribution of $z$ is unknown.
* **Bootstrap Idea (Efron, 1979):** A computer-based technique to estimate the accuracy of $\hat{\theta}$. In the absence of other information, the sample itself offers the best guide to the sampling distribution.
* **Name Origin:** From the phrase "to pull oneself up by one's bootstrap," like Baron Munchausen escaping a lake.
* **Process:**
    1.  Given an original sample $D_N$.
    2.  Generate $B$ bootstrap datasets $D_{(b)}$ (for $b=1,...,B$), each of size $N$, by **resampling with replacement** from $D_N$.
        * Python command: `np.random.choice(N, size=N, replace=True)`
    3.  For each bootstrap dataset $D_{(b)}$, calculate the statistic $\hat{\theta}_{(b)} = g(D_{(b)})$.
    4.  The empirical distribution of these $\hat{\theta}_{(b)}$ values is used to approximate the sampling distribution of $\hat{\theta}$.
* **Purpose:** Construct sampling distributions, confidence intervals, and tests for significance.

### 4.1. Bootstrap Sampling Details

* **Duplication:** Bootstrap datasets nearly always contain duplicate points from the original set.
* **Underlying Principle:** Replaces the unknown true distribution $F(\cdot)$ of $z$ with the known empirical distribution $\hat{F}_N(\cdot)$ (each original sample point has probability $1/N$ of being chosen on each draw).
* **Probability of k choices:** The probability that a point is chosen exactly $k$ times in a bootstrap dataset $D_{(b)}$ is given by the binomial distribution:
    $Prob\{k\} = \frac{N!}{k!(N-k)!} (\frac{1}{N})^k (\frac{N-1}{N})^{N-k}$, for $0 \le k \le N$.
* **Number of Distinct Bootstrap Datasets:** $\binom{N+k-1}{k}$ where $k=N$, so $\binom{2N-1}{N}$. This number is large even for small $N$.
    * Example: If $N=3$ and $D_N = \{a,b,c\}$, we have 10 different bootstrap sets.
* **Balanced Bootstrap Sampling:** The $B$ bootstrap sets are generated such that each original data point is present exactly $B$ times in the entire collection of bootstrap samples.

## 5. Bootstrap Estimates

### 5.1. Bootstrap Estimate of Variance

* **Bootstrap Replication:** $\hat{\theta}_{(b)} = g(D_{(b)})$ is the value of the statistic for the $b$-th bootstrap sample.
* **Bootstrap Estimate of Variance:**
    $Var_{bs}[\hat{\theta}] = \frac{\sum_{b=1}^{B}(\hat{\theta}_{(b)} - \hat{\theta}_{(\cdot)})^{2}}{(B-1)}$, where $\hat{\theta}_{(\cdot)} = \frac{\sum_{b=1}^{B}\hat{\theta}_{(b)}}{B}$ (the mean of bootstrap replications).
* $Var_{bs}[\hat{\theta}]$ is the variance of $\hat{\theta}$ if the distribution of $z$ were $\hat{F}$.
* If $\hat{\theta} = \hat{\mu}$, for $B \rightarrow \infty$, $Var_{bs}[\hat{\theta}]$ converges to $Var[\hat{\mu}]$.

### 5.2. Bootstrap Estimate of Bias

* Let $\hat{\theta}$ be the plug-in estimator from the original sample $D_N$.
* **Bootstrap Estimate of Bias:** $Bias_{bs}[\hat{\theta}] = \hat{\theta}_{(\cdot)} - \hat{\theta}$.
* **Bias Corrected Estimate:** Since $Bias[\hat{\theta}] = E[\hat{\theta}] - \theta \Rightarrow \theta = E[\hat{\theta}] - Bias[\hat{\theta}]$, the bootstrap bias-corrected estimate is:
    $\hat{\theta}_{bs} = \hat{\theta} - Bias_{bs}[\hat{\theta}] = \hat{\theta} - (\hat{\theta}_{(\cdot)} - \hat{\theta}) = 2\hat{\theta} - \hat{\theta}_{(\cdot)}$.
    * Example application: `NonParametric/patch.py` for bias and variance estimation in the patch data.

## 6. Bootstrap Percentile Confidence Interval

* **Method:** Uses the upper and lower $\alpha/2$ values of the bootstrap distribution of $\hat{\theta}_{(b)}$ to construct a $100(1-\alpha)\%$ confidence interval.
* Let $\hat{\theta}_{L,\alpha/2}$ be the value such that a fraction $\alpha/2$ of bootstrap estimates are less than it.
* Let $\hat{\theta}_{H,\alpha/2}$ be the value exceeded by a fraction $\alpha/2$ of bootstrap estimates.
* **Approximate Confidence Interval (Efron's percentile confidence limit):** $[\hat{\theta}_{L,\alpha/2}, \hat{\theta}_{H,\alpha/2}]$.
* Example: Vaccine data efficacy rate bootstrap distribution (script `NonParametric/pfizer.py`).

## 7. Number of Bootstrap Replicates (B)

* Adjustable based on computer resources.
* For $B \rightarrow \infty$, the bootstrap estimate of variance converges to the plug-in estimate.
* **Rules of Thumb:**
    1.  Even a small number of bootstrap replications, e.g. $B=25$, is usually informative. $B=50$ is often enough to give a good estimate of $Var[\hat{\theta}]$.
    2.  Very seldom are more than $B=200$ replications needed for estimating $Var[\hat{\theta}]$.
    3.  Much larger $B$ values are required for bootstrap confidence intervals.

## 8. The Bootstrap Principle

* **Goal:** Estimate the distribution of $\hat{\theta} - \theta$.
* **Bootstrap Approach:** Uses Monte Carlo approximation to the distribution of $\hat{\theta}_{(b)} - \hat{\theta}$.
* **Rationale:** For $N$ sufficiently large, these two distributions should be nearly the same. The variability of $\hat{\theta}_{(b)}$ (based on empirical distribution $\hat{F}$) around $\hat{\theta}$ mimics the variability of $\hat{\theta}$ (based on true distribution $F$) around $\theta$.
* **Theoretical Justification (Glivenko-Cantelli Theorem):** For i.i.d. samples, as $N \rightarrow \infty$, the empirical distribution $\hat{F}(\cdot)$ converges to the true distribution $F(\cdot)$ with probability one:
    $sup_{-\infty<z<\infty}|\hat{F}_{z}(z) - F_{z}(z)| \xrightarrow{N\rightarrow\infty} 0$.

## 9. Error in Resampling Methods

* **Bootstrap Error = Statistical Error + Simulation Error**
    * **Statistical Error:** Difference between true distribution $F(\cdot)$ and empirical distribution $\hat{F}(\cdot)$. Depends on $N$ and the choice of statistic $t(F)$.
        * Rough statistics (unsmooth or unstable, e.g., sample quantiles, median) can make resampling behave wildly.
    * **Simulation Error:** Due to using Monte Carlo to simulate $t(\hat{F})$. Decreases by increasing $B$.
        * $Var_F[\hat{\theta}]$ (true variance) $\leftarrow$ (statistical error, depends on N) $\rightarrow$ $Var_{\hat{F}}[\hat{\theta}]$ (ideal bootstrap variance) $\leftarrow$ (simulation error, depends on B) $\rightarrow$ $Var_{bs}[\hat{\theta}]$ (actual bootstrap estimate).

## 10. Convergence of Bootstrap Estimate

For i.i.d. observations, conditions for convergence of the bootstrap estimate:
1.  **Uniform convergence of $\hat{F}$ to $F$ (Glivenko-Cantelli theorem)** for $N \rightarrow \infty$.
2.  **Plug-in estimator:** $\hat{\theta}$ is the corresponding functional of the empirical distribution ($\theta = t(F) \rightarrow \hat{\theta} = t(\hat{F})$).
    * Satisfied for sample means, standard deviations, variances, medians, other sample quantiles.
3.  **Smoothness condition on the functional**.
    * Not true for extreme order statistics (min, max values).

## 11. When Might the Bootstrap Fail?

Bootstrap assumes $D_N$ is i.i.d. sampled from $F$. It might fail in non-conventional configurations:
* Too few samples (e.g., $N \le 30$).
* Incomplete data (survival data, missing data).
* Dependent data (e.g., variance of a correlated time series).
* Dirty data (outliers).
* Critical view: "Exploring the limits of bootstrap" (Le Page and Billard).

## 12. Parametric Bootstrap

* **Nonparametric Bootstrap (discussed so far):** No assumption about the distribution $F_z$ is made. Samples $D_{(b)}$ are obtained by resampling $D_N$ with replacement.
* **Parametric Bootstrap:**
    * Assumes the parametric shape of the distribution $F_z(\cdot, \theta)$ is known.
    * Samples $D_{(b)}$ are obtained by sampling from $F_z(\cdot, \hat{\theta})$, where $\hat{\theta}$ is estimated from the original data $D_N$.

## 13. Exercise Example

* Estimate $\theta = \mu^2$ from an i.i.d. dataset $D_N$, where $E[z]=\mu$ and $Var[z]=\sigma^2$.
* Consider three estimators:
    1.  $\hat{\theta}_{1} = (\frac{\sum_{i=1}^{N}Z_{i}}{N})^{2}$
    2.  $\hat{\theta}_{2} = \frac{\sum_{i=1}^{N}z_{i}^{2}}{N}$
    3.  $\hat{\theta}_{3} = \frac{(\sum_{i=1}^{N}z_{i})^{2}}{N}$
* Task: Estimate the bias of these three estimators by bootstrap and compare with previous analytical estimations.

## 14. Combination of Two Estimators

* Consider two unbiased estimators $\hat{\theta}_1$ and $\hat{\theta}_2$ of the same parameter $\theta$:
    * $E[\hat{\theta}_1] = \theta$, $E[\hat{\theta}_2] = \theta$
    * $Var[\hat{\theta}_1] = Var[\hat{\theta}_2] = v$
    * Uncorrelated: $Cov[\hat{\theta}_1, \hat{\theta}_2] = 0$.
* Combined estimator: $\hat{\theta}_{cm} = \frac{\hat{\theta}_1 + \hat{\theta}_2}{2}$.
* Properties:
    * Unbiased: $E[\hat{\theta}_{cm}] = \frac{E[\hat{\theta}_1]+E[\hat{\theta}_2]}{2} = \theta$.
    * Reduced Variance: $Var[\hat{\theta}_{cm}] = \frac{1}{4}Var[\hat{\theta}_1+\hat{\theta}_2] = \frac{Var[\hat{\theta}_1]+Var[\hat{\theta}_2]}{4} = \frac{v}{2}$.
* Conclusion: Averaging two unbiased estimators (with non-zero variance) reduces the variance of the combined estimator.

## 15. Regularisation of an Estimator

* Let $\hat{\theta}$ be an unbiased estimator of $\theta$.
* Regularised version: $\hat{\theta}_r = \lambda\theta_0 + (1-\lambda)\hat{\theta}$, where:
    * $\theta_0 \neq \theta$ is a constant a-priori estimator.
    * $0 < \lambda < 1$ is the regularisation parameter.
* **Bias:** $Bias[\hat{\theta}_r] = E_D[\hat{\theta}_r] - \theta = \lambda(\theta_0 - \theta) \neq 0 = Bias[\hat{\theta}]$.
* **Variance:** $Var[\hat{\theta}_r] = (1-\lambda)^{2}Var[\hat{\theta}] < Var[\hat{\theta}]$.
* **Mean Squared Error (MSE):** $MSE[\hat{\theta}_r] = Bias[\hat{\theta}_r]^2 + Var[\hat{\theta}_r] = \lambda^{2}(\theta_0-\theta)^{2}+(1-\lambda)^{2}Var[\hat{\theta}]$.
* $MSE[\hat{\theta}_r] \le MSE[\hat{\theta}]$ if $\lambda \le 2\frac{Var[\hat{\theta}]}{(\theta_{0}-\theta)^{2}+Var[\hat{\theta}]}$.
* Regularisation can reduce MSE by trading some bias for a larger reduction in variance.