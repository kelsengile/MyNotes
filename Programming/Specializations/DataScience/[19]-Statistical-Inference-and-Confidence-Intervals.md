*Statistics & Probability*

# Lesson 19 - Statistical Inference & Confidence Intervals

[Previous](./[18]-Hypothesis-Testing.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[20]-Introduction-to-Machine-Learning.md)

---

## 19.1 Population vs Sample

A **population** is the entire group you want to draw conclusions about (e.g. all customers). A **sample** is the subset you actually observe. **Statistical inference** is the process of drawing conclusions about a population using only a sample — since measuring an entire population is usually impossible or impractical.

- Population mean is denoted μ (usually unknown).
- Sample mean is denoted x̄ (what we actually calculate).

---

## 19.2 Sampling Error and Standard Error

Because a sample is only part of the population, any statistic computed from it (like a sample mean) will differ somewhat from the true population value — this natural variation is **sampling error**. The **standard error** measures how much a sample statistic would be expected to vary from sample to sample, and shrinks as sample size grows:

```
standard_error = sample_std / sqrt(sample_size)
```

---

## 19.3 Confidence Intervals

A **confidence interval (CI)** gives a range of plausible values for a population parameter, along with a confidence level (commonly 95%). A 95% confidence interval means: *if we repeated this sampling process many times, about 95% of the intervals we'd construct would contain the true population value.*

```python
import scipy.stats as stats
import numpy as np

sample = df["age"].dropna()
mean = sample.mean()
sem = stats.sem(sample)   # standard error of the mean

ci = stats.t.interval(confidence=0.95, df=len(sample) - 1, loc=mean, scale=sem)
print(ci)   # e.g. (34.2, 38.6)
```

A common misconception: a 95% CI does *not* mean "there's a 95% chance the true value is in this specific interval" — the true value is fixed, it's the sampling procedure that has the 95% success rate over the long run.

---

## 19.4 Why This Matters for Data Science

Inference is what lets you generalize beyond the specific rows you happened to collect: estimating a true conversion rate from a test group, comparing two product designs, or reporting how confident you are in a business metric. It's also the statistical foundation underneath machine learning evaluation (Lesson 27) — a model's reported accuracy is itself a sample statistic with its own uncertainty.

---

[Previous](./[18]-Hypothesis-Testing.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[20]-Introduction-to-Machine-Learning.md)
