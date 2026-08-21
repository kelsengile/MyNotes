*Supervised Learning*

# Lesson 26 - Support Vector Machines

[Previous](./[25]-Decision-Trees-and-Random-Forests.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[27]-Model-Evaluation-Metrics.md)

---

## 26.1 The Core Idea

A **Support Vector Machine (SVM)** is a classification algorithm that finds the best boundary (called a **hyperplane**) separating classes in feature space. "Best" specifically means the boundary that maximizes the **margin** — the distance between the boundary and the nearest data points of each class, called the **support vectors**, since they're the points that most directly determine where the boundary is drawn.

```python
from sklearn.svm import SVC

model = SVC(kernel="linear")
model.fit(X_train, y_train)
predictions = model.predict(X_test)
```

---

## 26.2 The Kernel Trick

Many real datasets aren't linearly separable — no straight line/plane can cleanly divide the classes. SVMs handle this with the **kernel trick**: a mathematical shortcut that implicitly maps the data into a higher-dimensional space where a linear boundary *can* separate the classes, without ever explicitly computing that transformation.

```python
model = SVC(kernel="rbf", C=1.0, gamma="scale")   # radial basis function kernel, handles non-linear boundaries
```

Common kernels: `"linear"` (straight boundary), `"poly"` (polynomial curves), `"rbf"` (radial basis function, very flexible, the most commonly used default).

---

## 26.3 Key Hyperparameters

- **C** — controls the tradeoff between a wider margin and correctly classifying every training point. A small C allows more misclassifications for a wider, more generalizable margin; a large C fits the training data more tightly (higher risk of overfitting).
- **gamma** (for the RBF kernel) — controls how far the influence of a single training example reaches; a small gamma means smoother, simpler boundaries, while a large gamma allows the boundary to fit the data very tightly.

Because SVMs are sensitive to feature scale, it's essential to standardize features (Lesson 23) before training one.

---

## 26.4 Strengths and Limitations

SVMs perform well in high-dimensional spaces and when there's a clear margin of separation, and remain effective even with relatively small datasets. However, they scale poorly to very large datasets, require careful tuning of C and gamma, and — unlike decision trees or linear regression — don't naturally produce easily interpretable explanations of individual predictions.

---

[Previous](./[25]-Decision-Trees-and-Random-Forests.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[27]-Model-Evaluation-Metrics.md)
