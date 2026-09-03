[Previous](./[6]-Correlation-And-Regression.md) | [Table of Contents](./[0]-Introduction-to-Statistics.md)

# Lesson 7 - Statistics In Computer Science

This final lesson connects everything in this topic to two areas where statistics is used daily in computer science: building and evaluating machine learning models, and running controlled experiments to make product decisions.

---

## 7.1 Statistics in Data Science and Machine Learning

Nearly every stage of a typical machine learning workflow leans directly on concepts already covered in this topic:

**Descriptive statistics (Lesson 1)** is usually the very first step in any data science project — computing means, medians, and standard deviations per column, and plotting histograms, to understand a dataset before doing anything else with it ("exploratory data analysis," or EDA).

**Probability distributions (Lesson 4)** describe the assumptions many models are built on. For example, linear regression models typically assume the *residuals* (prediction errors) are normally distributed, and many statistical tests used to evaluate a model's coefficients rely on this assumption holding at least approximately.

**Train/test splitting is a direct application of sampling (Lesson 5):** a model is fit on a "training" subset of data and evaluated on a separate, unseen "test" subset, specifically to estimate how well it will generalize to new data — this mirrors the logic of a sampling distribution: you want a reliable estimate of performance on the broader population of possible future inputs, not just a description of the exact data the model has already memorized.

**Overfitting is a statistics problem in disguise.** A model that fits its training data almost perfectly but performs poorly on new data has essentially mistaken random noise in the training sample for a genuine pattern — the model-building equivalent of over-trusting a single confidence interval or a single small sample's quirks.

**Regression (Lesson 6) is the direct ancestor of many ML models.** Linear regression is itself a foundational machine learning algorithm, and logistic regression (used for classification, e.g. spam vs. not-spam) extends the same core idea — fitting coefficients to a dataset — to predict probabilities rather than continuous numbers. Evaluation metrics for classifiers, like precision and recall, are themselves built from the same true-positive/false-positive language introduced with hypothesis testing errors in Lesson 5.

**Example — feature correlation in practice:** before training a model, data scientists routinely compute a correlation matrix across all input features. Two features that are extremely highly correlated with each other (not just with the target) provide largely redundant information, and are often flagged so one can be dropped — improving model interpretability and sometimes stability, without meaningfully hurting predictive power.

---

## 7.2 A/B Testing Basics

**A/B testing** is hypothesis testing (Lesson 5), applied directly to product decisions. Instead of testing an abstract population parameter, an A/B test compares two versions of a product feature (commonly "A," the current version, and "B," a proposed change) to see which performs better on some measurable outcome.

**The typical A/B test workflow:**

1. **Formulate hypotheses.** H₀: "there's no difference in conversion rate between version A and version B." H₁: "version B has a different (or specifically higher) conversion rate than version A."
2. **Randomly assign users** to see version A or version B — random assignment is what allows any observed difference to be attributed to the change itself, rather than to some underlying difference between the groups (a confounding variable, as discussed in Lesson 6).
3. **Collect data** on the outcome metric (e.g. click-through rate, purchase rate) for both groups over a predetermined period.
4. **Run a hypothesis test** (commonly a two-proportion Z-test or a t-test) comparing the two groups, and compute a p-value.
5. **Decide** based on the significance level chosen in advance: if p-value < α, conclude the observed difference is statistically significant and adopt (or reject) version B accordingly.

**Example:** An e-commerce site tests a new checkout button color. 10,000 users see the old (blue) button, with a 4.0% conversion rate. Another 10,000 users see the new (green) button, with a 4.5% conversion rate. A two-proportion hypothesis test produces a p-value of 0.03. Since 0.03 < 0.05, the team concludes the improvement is statistically significant and rolls out the green button broadly.

**Common pitfalls specific to A/B testing (each a direct real-world consequence of ideas from Lesson 5):**

- **Peeking / stopping early:** checking results repeatedly and stopping the test as soon as it *looks* significant inflates the true Type I error rate far above the stated α — the test needs its full pre-determined sample size to keep the promised significance level valid.
- **Multiple comparisons:** running many simultaneous tests (e.g. testing 20 different button colors at once) means that, purely by chance, roughly one in twenty tests is expected to appear "significant" at α = 0.05 even if none of the changes have any real effect — a direct consequence of what a Type I error rate actually means.
- **Underpowered tests:** samples that are too small (small n means larger standard error, per Lesson 5) may fail to detect a real effect at all, leading to a false "no difference" conclusion — a Type II error — simply from insufficient data rather than the absence of a real effect.
- **Novelty effects:** users may temporarily react differently to *any* change simply because it's new, inflating short-term results in a way that fades once the novelty wears off — a reminder that the assumptions behind a clean statistical test (like stable, independent behavior) don't automatically hold in messy real-world settings.

**Why this lesson closes the topic:** A/B testing is, in miniature, the entire arc of this material — describe the data (Lesson 1), model it with probability (Lessons 2–4), draw a rigorous conclusion about a population from a sample (Lesson 5), and often relate two variables to understand *why* an effect happened (Lesson 6). Statistics isn't a collection of isolated formulas — it's a single, connected way of reasoning carefully under uncertainty.

---

[Previous](./[6]-Correlation-And-Regression.md) | [Table of Contents](./[0]-Introduction-to-Statistics.md)
