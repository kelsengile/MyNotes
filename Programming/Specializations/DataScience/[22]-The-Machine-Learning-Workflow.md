*Machine Learning Fundamentals*

# Lesson 22 - The Machine Learning Workflow (Train/Test/Validate)

[Previous](./[21]-Supervised-vs-Unsupervised-Learning.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[23]-Feature-Engineering-and-Selection.md)

---

## 22.1 Why Split Your Data?

If you train and evaluate a model on the exact same data, it can appear to perform great simply by memorizing that data — a problem called **overfitting**. To know how well a model will perform on new, unseen data, you must evaluate it on data it never saw during training.

---

## 22.2 Train, Validation, and Test Sets

- **Training set** — used to fit the model's parameters (typically ~60-80% of the data).
- **Validation set** — used to tune settings (hyperparameters) and compare models during development.
- **Test set** — held out and used only once, at the very end, to report final, unbiased performance.

```python
from sklearn.model_selection import train_test_split

X_train, X_temp, y_train, y_temp = train_test_split(X, y, test_size=0.3, random_state=42)
X_val, X_test, y_val, y_test = train_test_split(X_temp, y_temp, test_size=0.5, random_state=42)
```

Setting `random_state` ensures the split is reproducible each time the code runs.

---

## 22.3 Cross-Validation

With smaller datasets, splitting once wastes data. **K-fold cross-validation** instead splits the data into *k* equal parts ("folds"), trains on k-1 folds, and validates on the remaining fold — repeating this k times so every fold is used for validation once, then averaging the results.

```python
from sklearn.model_selection import cross_val_score
from sklearn.linear_model import LogisticRegression

scores = cross_val_score(LogisticRegression(), X, y, cv=5)   # 5-fold cross-validation
print(scores.mean())
```

---

## 22.4 Overfitting and Underfitting

- **Overfitting** — the model learns the training data too well, including its noise, and performs poorly on new data. Signs: very high training accuracy but much lower validation accuracy.
- **Underfitting** — the model is too simple to capture the real pattern, performing poorly on both training and validation data.

The goal is a model that generalizes well — striking a balance often called the **bias-variance tradeoff**: a model with high bias (too simple) underfits, while a model with high variance (too complex, too sensitive to training data) overfits.

---

[Previous](./[21]-Supervised-vs-Unsupervised-Learning.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[23]-Feature-Engineering-and-Selection.md)
