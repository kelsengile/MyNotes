*Machine Learning Fundamentals*

# Lesson 21 - Supervised vs Unsupervised Learning

[Previous](./[20]-Introduction-to-Machine-Learning.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[22]-The-Machine-Learning-Workflow.md)

---

## 21.1 Supervised Learning

In **supervised learning**, the training data includes both the input features and the correct answer (the label). The model learns a mapping from inputs to outputs, then applies that mapping to new, unseen inputs. Supervised learning splits into two main problem types:

- **Regression** — predicting a continuous number (e.g. house price, temperature). Covered in Lesson 24.
- **Classification** — predicting a category (e.g. spam or not spam, which customer segment). Covered in Lessons 24-27.

---

## 21.2 Unsupervised Learning

In **unsupervised learning**, the training data has no labels — the algorithm must find structure or patterns on its own. Common tasks:

- **Clustering** — grouping similar data points together (Lesson 28).
- **Dimensionality reduction** — compressing many features into fewer, while preserving important structure (Lesson 29).
- **Anomaly detection** — identifying unusual data points that don't fit expected patterns (Lesson 30).

Unsupervised learning is often used for exploration, or as a preprocessing step before supervised learning.

---

## 21.3 Semi-Supervised and Self-Supervised Learning

- **Semi-supervised learning** uses a small amount of labeled data alongside a larger pool of unlabeled data — useful when labeling is expensive.
- **Self-supervised learning** generates its own labels from the structure of the data itself (e.g. predicting a masked word from surrounding text). This approach underlies the large language models covered in Lesson 37.

---

## 21.4 Choosing Between Them

The deciding question is simple: **do you have labeled outcomes to learn from?**

- If you have historical examples with known correct answers (e.g. past loan applications with "defaulted" or "repaid" labels) → supervised learning.
- If you're exploring data to find natural groupings or patterns with no predefined "correct answer" (e.g. segmenting customers by behavior) → unsupervised learning.

Many real projects use both: unsupervised techniques to explore and engineer features, then supervised learning to make the final prediction.

---

[Previous](./[20]-Introduction-to-Machine-Learning.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[22]-The-Machine-Learning-Workflow.md)
