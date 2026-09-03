[Previous](./[4]-Probability-Distributions.md) | [Table of Contents](./[0]-Introduction-to-Statistics.md) | [Next](./[6]-Correlation-And-Regression.md)

# Lesson 5 - Inferential Statistics

Descriptive statistics summarizes the data you *have*. **Inferential statistics** goes further: it uses a sample to draw conclusions about a larger population you *don't* have complete data for — and, critically, it quantifies how confident you should be in those conclusions.

---

## 5.1 Sampling Distributions & the Central Limit Theorem

A **sampling distribution** is the probability distribution of a statistic (like the mean) computed from repeated random samples of the same size, drawn from the same population. It's a distribution *of sample statistics*, not of the raw data itself.

**Example:** Suppose you repeatedly draw samples of 50 people from a city and compute the average height for each sample. Each sample gives a slightly different average — the distribution of *those averages* across many samples is the sampling distribution of the mean.

**The Central Limit Theorem (CLT)** is one of the most important results in all of statistics. It states: for a sufficiently large sample size (n), the sampling distribution of the sample mean will be approximately **normal**, regardless of the shape of the original population's distribution — as long as the population has a finite mean and variance.

**Formally**, if X̄ is the mean of a sample of size n drawn from a population with mean μ and standard deviation σ:

X̄ ~ approximately Normal(μ, σ²/n), for large n (commonly, n ≥ 30 is used as a rule of thumb)

**Why this is remarkable:** the *original* population can be skewed, bimodal, or any shape at all — dice rolls, income data, arrival times — and the distribution of sample *means* will still smooth out into a bell curve as sample size grows. This is exactly why the normal distribution shows up so often in practice: it's not that individual measurements are always normal, it's that *averages* of many things tend to be.

**Standard error:** the standard deviation of the sampling distribution itself, given by SE = σ/√n. Notice that as sample size n increases, the standard error shrinks — larger samples give more precise (less variable) estimates of the population mean, which is the mathematical reason "bigger samples are better."

---

## 5.2 Confidence Intervals

A **confidence interval** gives a range of plausible values for an unknown population parameter (like the true mean), rather than a single point estimate, along with a stated confidence level (commonly 95%).

**General form:**

point estimate ± (critical value) × (standard error)

**Example — a 95% confidence interval for a population mean:** Suppose a sample of 100 students has a mean test score of x̄ = 78, with a sample standard deviation of s = 10. Using the standard error SE = s/√n = 10/√100 = 1, and a critical value of approximately 1.96 for a 95% confidence level (from the standard normal distribution):

CI = 78 ± (1.96)(1) = 78 ± 1.96 = **(76.04, 79.96)**

**What "95% confidence" actually means (a common misconception to avoid):** it does **not** mean "there's a 95% probability the true mean falls in this specific interval." The true population mean is a fixed number — it either is or isn't in this particular interval. What 95% confidence actually means is: if you repeated this entire sampling and interval-construction process many times, about 95% of the resulting intervals would contain the true population mean. The confidence is in the *procedure*, not in any single computed interval.

**What affects interval width:**

| Factor | Effect on interval width |
|---|---|
| Larger sample size (n) | narrower interval (more precision) |
| Higher confidence level (e.g. 99% vs 95%) | wider interval (more certainty requires more room) |
| Larger variability in the data (s) | wider interval (noisier data, less precision) |

---

## 5.3 Hypothesis Testing

**Hypothesis testing** is a formal procedure for deciding whether evidence from a sample is strong enough to reject a default assumption about a population.

**The two hypotheses:**

- **Null hypothesis (H₀):** the default assumption, typically "no effect" or "no difference." E.g., "this new drug has no effect on blood pressure."
- **Alternative hypothesis (H₁ or Hₐ):** what you're trying to find evidence *for*. E.g., "this new drug does affect blood pressure."

**The general procedure:**

1. State H₀ and H₁.
2. Choose a significance level α (commonly 0.05), which is the threshold probability for how much risk of a wrong conclusion you're willing to accept.
3. Collect data and compute a test statistic (e.g. a Z-score or t-score) summarizing how far the sample result is from what H₀ predicts.
4. Compute the **p-value**: the probability of observing a result at least as extreme as yours, *assuming H₀ is true*.
5. If p-value < α, **reject H₀** (the evidence is strong enough to conclude something is going on). Otherwise, **fail to reject H₀** (not enough evidence — this is *not* the same as proving H₀ is true).

**Example:** A company claims their website's average load time is 2 seconds (H₀: μ = 2). You suspect it's actually slower and test H₁: μ > 2. You sample 40 page loads and get a sample mean of 2.3 seconds with a p-value of 0.02 for that result under H₀. Since 0.02 < 0.05 (your chosen α), you **reject H₀** — there's statistically significant evidence the load time exceeds 2 seconds.

**Two ways a hypothesis test can go wrong:**

| | H₀ is actually true | H₀ is actually false |
|---|---|---|
| You reject H₀ | Type I Error (false positive) | Correct decision |
| You fail to reject H₀ | Correct decision | Type II Error (false negative) |

The significance level α is exactly the probability of a **Type I Error** you're willing to accept — choosing α = 0.05 means you accept a 5% chance of concluding there's an effect when there really isn't one, purely from random sample noise. This is precisely the same reasoning trap illustrated by the medical testing example in Lesson 2's Bayes' Theorem section: a "statistically significant" result is not the same as "definitely true," especially when many tests are run and some false positives are expected by chance alone.

---

[Previous](./[4]-Probability-Distributions.md) | [Table of Contents](./[0]-Introduction-to-Statistics.md) | [Next](./[6]-Correlation-And-Regression.md)
