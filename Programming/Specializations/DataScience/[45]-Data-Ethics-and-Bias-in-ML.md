*Best Practices*

# Lesson 45 - Data Ethics & Bias in ML

[Previous](./[44]-Data-Pipelines-and-ETL-Basics.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[46]-Reproducibility-and-Experiment-Tracking.md)

---

## 45.1 Where Bias Comes From

Machine learning models learn patterns from historical data — and if that data reflects existing societal biases or unequal treatment, the model can learn and even amplify them. Common sources of bias include:

- **Sampling bias** — the training data doesn't represent the population the model will actually be used on.
- **Historical bias** — the data accurately reflects a real, but unfair, historical pattern (e.g. past hiring decisions shaped by discrimination).
- **Label bias** — the "correct answers" used for training were themselves subjectively or unfairly assigned.

---

## 45.2 Measuring Fairness

There's no single universal definition of "fairness" — different, mathematically incompatible definitions are appropriate in different contexts. Common approaches include checking whether a model's error rates, precision, or approval rates are comparable across different demographic groups.

```python
# Example: comparing false positive rates across groups
group_a_fpr = false_positives_a / (false_positives_a + true_negatives_a)
group_b_fpr = false_positives_b / (false_positives_b + true_negatives_b)
```

The right fairness metric to prioritize depends heavily on the specific application and its real-world consequences — this is a decision that requires domain expertise and stakeholder input, not just a technical calculation.

---

## 45.3 Privacy Considerations

Data science work often involves personal or sensitive data, raising responsibilities around:

- **Anonymization/pseudonymization** — removing or masking directly identifying information.
- **Consent** — using data only in ways people agreed to when it was collected.
- **Regulation compliance** — laws like GDPR (Europe) and various regional data protection laws impose specific requirements on how personal data can be collected, stored, and used.

---

## 45.4 Building Responsible Practices

Practical habits that reduce ethical risk:

- Examine training data for representation gaps before modeling, not after deployment.
- Test model performance across relevant subgroups, not just in aggregate (recall Lesson 27's point that aggregate accuracy can hide serious problems).
- Document model limitations and intended use clearly, so it isn't applied in contexts it wasn't designed or tested for.
- Keep a human in the loop for high-stakes decisions (lending, hiring, medical diagnosis) rather than fully automating them.

---

[Previous](./[44]-Data-Pipelines-and-ETL-Basics.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[46]-Reproducibility-and-Experiment-Tracking.md)
