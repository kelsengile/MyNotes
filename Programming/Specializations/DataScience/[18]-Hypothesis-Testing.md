*Statistics & Probability*

# Lesson 18 - Hypothesis Testing

[Previous](./[17]-Probability-Distributions.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[19]-Statistical-Inference-and-Confidence-Intervals.md)

---

## 18.1 The Logic of Hypothesis Testing

Hypothesis testing is a formal procedure for deciding whether an observed effect in your data is likely real, or could plausibly have happened by random chance. It starts with two competing statements:

- **Null hypothesis (H₀)** — the "no effect" or "no difference" default assumption (e.g. "this new website design has no effect on conversion rate").
- **Alternative hypothesis (H₁)** — what you're actually trying to find evidence for (e.g. "the new design changes the conversion rate").

You collect data, then ask: *if the null hypothesis were true, how surprising would this result be?*

---

## 18.2 P-values and Significance

The **p-value** is the probability of observing a result at least as extreme as yours, assuming the null hypothesis is true. A small p-value suggests the observed result would be unlikely under the null hypothesis, giving evidence against it.

A common convention is a **significance level (α)** of 0.05: if p < 0.05, the result is called "statistically significant" and the null hypothesis is rejected. This threshold is a convention, not a law of nature — it should be chosen based on context.

---

## 18.3 Common Statistical Tests

```python
from scipy import stats

# t-test: compare the means of two groups
stats.ttest_ind(group_a["conversion"], group_b["conversion"])

# chi-square test: check association between two categorical variables
stats.chi2_contingency(pd.crosstab(df["city"], df["purchased"]))

# ANOVA: compare means across more than two groups
stats.f_oneway(group_a["score"], group_b["score"], group_c["score"])
```

Choosing the right test depends on your data type (numeric vs categorical) and how many groups you're comparing.

---

## 18.4 Type I/II Errors and Common Pitfalls

- **Type I error** — rejecting a true null hypothesis (a "false positive").
- **Type II error** — failing to reject a false null hypothesis (a "false negative").

Common pitfalls to watch for:

- **p-hacking** — running many tests until one happens to be significant by chance.
- Confusing **statistical significance** with **practical significance** — a tiny, meaningless effect can still be "significant" with a large enough sample.
- Ignoring **sample size** — very small samples make it hard to detect real effects reliably.

---

[Previous](./[17]-Probability-Distributions.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[19]-Statistical-Inference-and-Confidence-Intervals.md)
