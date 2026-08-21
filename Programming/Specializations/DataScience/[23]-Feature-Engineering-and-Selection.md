*Machine Learning Fundamentals*

# Lesson 23 - Feature Engineering & Selection

[Previous](./[22]-The-Machine-Learning-Workflow.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[24]-Linear-and-Logistic-Regression.md)

---

## 23.1 What is Feature Engineering?

**Feature engineering** is the process of creating new input variables (features) from raw data to help a model learn better. It's frequently the single highest-leverage step in a machine learning project — a well-engineered feature can matter more than the choice of model itself.

Examples: extracting day-of-week from a timestamp (Lesson 11), computing a "days since last purchase" feature, or combining "total spend" and "number of orders" into "average order value."

---

## 23.2 Encoding Categorical Features

Most ML algorithms require numeric input, so categorical variables must be encoded:

```python
import pandas as pd

# One-hot encoding: each category becomes its own binary column
pd.get_dummies(df, columns=["city"])

# Ordinal encoding: for categories with a natural order
size_map = {"S": 1, "M": 2, "L": 3}
df["size_encoded"] = df["size"].map(size_map)
```

Use one-hot encoding for unordered categories, and ordinal encoding only when categories have a genuine rank order (otherwise the model may wrongly assume "L" is "greater than" "M" in a meaningful numeric sense beyond ranking).

---

## 23.3 Scaling Numeric Features

Some algorithms (like those relying on distances, e.g. SVMs or K-means) are sensitive to the scale of features:

```python
from sklearn.preprocessing import StandardScaler, MinMaxScaler

scaler = StandardScaler()          # rescales to mean 0, std 1
X_scaled = scaler.fit_transform(X)

minmax = MinMaxScaler()               # rescales to a 0-1 range
X_minmax = minmax.fit_transform(X)
```

Always fit the scaler on training data only, then apply (`.transform()`) the same scaling to validation/test data — never re-fit on test data, or you'll leak information from it.

---

## 23.4 Feature Selection

Not every feature helps — some add noise, redundancy (see multicollinearity, Lesson 15), or even hurt performance. Common approaches:

- **Filter methods** — rank features by a statistical measure (like correlation with the target) and keep the top ones.
- **Wrapper methods** — try different subsets of features and keep whichever set performs best in cross-validation.
- **Embedded methods** — some models (like tree-based models, Lesson 25) naturally compute feature importance as part of training.

```python
from sklearn.ensemble import RandomForestClassifier

model = RandomForestClassifier().fit(X_train, y_train)
importances = pd.Series(model.feature_importances_, index=X_train.columns)
importances.sort_values(ascending=False)
```

---

[Previous](./[22]-The-Machine-Learning-Workflow.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[24]-Linear-and-Logistic-Regression.md)
