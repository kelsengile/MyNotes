*Statistics & Probability*

# Lesson 17 - Probability Distributions

[Previous](./[16]-Probability-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[18]-Hypothesis-Testing.md)

---

## 17.1 What is a Probability Distribution?

A **probability distribution** describes how the values of a random variable are spread out — which values are likely, and which are rare. Distributions are either **discrete** (countable outcomes, like the number of customer complaints per day) or **continuous** (any value in a range, like height or temperature).

---

## 17.2 Common Discrete Distributions

- **Bernoulli** — a single trial with two outcomes (success/failure), like one coin flip.
- **Binomial** — the number of successes across a fixed number of independent Bernoulli trials (e.g. number of heads in 10 coin flips).
- **Poisson** — the number of events occurring in a fixed interval of time or space, given a known average rate (e.g. number of website visits per hour).

```python
from scipy import stats

stats.binom.pmf(k=6, n=10, p=0.5)      # probability of exactly 6 heads in 10 flips
stats.poisson.pmf(k=3, mu=5)              # probability of exactly 3 events, average rate 5
```

---

## 17.3 The Normal Distribution

The **normal distribution** (or "bell curve") is the most important continuous distribution in statistics — it's symmetric around its mean, and many natural and measurement processes approximate it. It's fully described by two parameters: mean (μ) and standard deviation (σ).

The **68-95-99.7 rule**: roughly 68% of values fall within 1 standard deviation of the mean, 95% within 2, and 99.7% within 3.

```python
import numpy as np
from scipy import stats

sample = np.random.normal(loc=100, scale=15, size=1000)   # mean=100, std=15
stats.norm.cdf(115, loc=100, scale=15)   # probability a value is <= 115
```

---

## 17.4 The Central Limit Theorem

The **Central Limit Theorem (CLT)** states that if you repeatedly take random samples from *any* distribution and compute their means, those sample means will approximately follow a normal distribution, as long as the sample size is large enough — even if the original data isn't normally distributed at all.

This is one of the most important ideas in all of statistics: it's the reason the normal distribution appears so often in practice, and it's the theoretical foundation for the confidence intervals and hypothesis tests covered in the next two lessons.

---

[Previous](./[16]-Probability-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[18]-Hypothesis-Testing.md)
