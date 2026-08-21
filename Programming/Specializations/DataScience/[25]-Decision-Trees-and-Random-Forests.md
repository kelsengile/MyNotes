*Supervised Learning*

# Lesson 25 - Decision Trees & Random Forests

[Previous](./[24]-Linear-and-Logistic-Regression.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[26]-Support-Vector-Machines.md)

---

## 25.1 Decision Trees

A **decision tree** predicts an outcome by asking a sequence of yes/no questions about the features, splitting the data at each step to make the resulting groups as "pure" as possible (i.e. as similar as possible with respect to the target).

```python
from sklearn.tree import DecisionTreeClassifier, plot_tree
import matplotlib.pyplot as plt

model = DecisionTreeClassifier(max_depth=4)
model.fit(X_train, y_train)

plot_tree(model, feature_names=X_train.columns, filled=True)
plt.show()
```

Trees are trained by choosing, at each split, the feature and threshold that best separates the classes — commonly measured with **Gini impurity** or **entropy**. Trees are highly interpretable (you can trace the exact path of decisions), but a single deep tree easily overfits the training data.

---

## 25.2 Random Forests

A **random forest** is an **ensemble** method — it trains many decision trees on random subsets of the data and features, then combines their predictions (by voting for classification, or averaging for regression). This reduces overfitting substantially compared to a single tree.

```python
from sklearn.ensemble import RandomForestClassifier

model = RandomForestClassifier(n_estimators=200, max_depth=6, random_state=42)
model.fit(X_train, y_train)
predictions = model.predict(X_test)
```

---

## 25.3 Why Ensembles Work

The core idea is that combining many "weak," slightly-different models tends to produce a stronger, more stable model overall — individual trees' errors tend to cancel out when averaged, as long as the trees are sufficiently diverse (achieved here through random sampling of rows and features per tree). This same ensemble principle underlies **gradient boosting** methods (like XGBoost and LightGBM), which build trees sequentially, each one correcting the errors of the previous ones, and are extremely popular in real-world applications and competitions.

---

## 25.4 Key Hyperparameters

- `n_estimators` — how many trees to build (more trees generally help, up to diminishing returns).
- `max_depth` — how deep each tree can grow (controls overfitting).
- `min_samples_split` — the minimum number of samples required to split a node further.
- `max_features` — how many features each tree considers at each split.

Tuning these (often via cross-validation, Lesson 22) is a standard part of building a strong random forest model.

---

[Previous](./[24]-Linear-and-Logistic-Regression.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[26]-Support-Vector-Machines.md)
