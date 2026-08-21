*Model Deployment & MLOps*

# Lesson 41 - Model Monitoring & Drift

[Previous](./[40]-Introduction-to-MLOps.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[42]-Introduction-to-Big-Data.md)

---

## 41.1 Why Monitor a Deployed Model?

A model's performance at deployment time isn't guaranteed to last. The real world changes — customer behavior shifts, new products launch, external events occur — and a model trained on past data can quietly become less accurate over time without any errors or crashes to signal the problem. Monitoring is how teams catch this before it causes real harm.

---

## 41.2 Data Drift and Concept Drift

- **Data drift** — the statistical properties of the input data change over time (e.g. average customer age shifts, a new product category appears) even though the true relationship between inputs and outcomes hasn't changed.
- **Concept drift** — the actual relationship between inputs and the outcome changes (e.g. what indicated fraud last year no longer indicates fraud this year, because fraud tactics evolved).

Both cause a model's real-world performance to degrade even if nothing about the model itself was changed.

---

## 41.3 Detecting Drift

```python
from scipy.stats import ks_2samp

# Compare the distribution of a feature in training data vs new incoming data
statistic, p_value = ks_2samp(training_data["age"], live_data["age"])
if p_value < 0.05:
    print("Significant distribution shift detected")
```

This uses the same hypothesis testing logic from Lesson 18 — comparing whether two samples (training vs live data) appear to come from the same distribution. In production, teams typically track this automatically for key features and set up alerts when drift crosses a threshold.

---

## 41.4 Responding to Drift

Once drift is detected, common responses include:

- **Retraining** the model on more recent data, ideally through an automated pipeline (Lesson 40).
- **Investigating root cause** — sometimes drift signals a genuine upstream data problem (e.g. a broken data feed) rather than a real-world shift.
- **Re-evaluating features** — a feature that was predictive before may lose its signal, requiring the feature engineering process (Lesson 23) to be revisited.

Monitoring closes the loop on the entire data science workflow introduced in Lesson 1 — a model is never really "done," it requires ongoing attention like any other production system.

---

[Previous](./[40]-Introduction-to-MLOps.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[42]-Introduction-to-Big-Data.md)
