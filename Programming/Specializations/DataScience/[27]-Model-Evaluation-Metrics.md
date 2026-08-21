*Supervised Learning*

# Lesson 27 - Model Evaluation Metrics

[Previous](./[26]-Support-Vector-Machines.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[28]-Clustering.md)

---

## 27.1 Classification Metrics: The Confusion Matrix

A **confusion matrix** breaks down predictions into four categories: True Positives (TP), True Negatives (TN), False Positives (FP), and False Negatives (FN).

```python
from sklearn.metrics import confusion_matrix

cm = confusion_matrix(y_test, predictions)
```

From these four counts, several key metrics are derived:

- **Accuracy** — `(TP + TN) / total`: overall proportion correct. Misleading on imbalanced datasets (e.g. 99% accuracy is meaningless if 99% of cases are the same class).
- **Precision** — `TP / (TP + FP)`: of everything predicted positive, what fraction was actually positive. High precision means few false alarms.
- **Recall (Sensitivity)** — `TP / (TP + FN)`: of everything actually positive, what fraction was caught. High recall means few missed cases.
- **F1 Score** — the harmonic mean of precision and recall, useful when you need a single balanced number.

```python
from sklearn.metrics import classification_report

print(classification_report(y_test, predictions))
```

---

## 27.2 Choosing the Right Metric

The right metric depends on the cost of different mistakes: in cancer screening, missing a real case (a false negative) is far worse than a false alarm, so **recall** matters more. In spam filtering, wrongly blocking a legitimate email (a false positive) may be worse than missing some spam, so **precision** matters more. Always tie the metric choice back to the real-world cost of each type of error.

---

## 27.3 ROC Curves and AUC

The **ROC curve** plots the true positive rate against the false positive rate across every possible classification threshold, showing the tradeoff between catching positives and avoiding false alarms. **AUC** (Area Under the Curve) summarizes this into a single number from 0.5 (no better than random guessing) to 1.0 (perfect separation).

```python
from sklearn.metrics import roc_auc_score

auc = roc_auc_score(y_test, model.predict_proba(X_test)[:, 1])
```

---

## 27.4 Regression Metrics

For regression models (Lesson 24), common metrics include:

- **MAE (Mean Absolute Error)** — the average absolute difference between predicted and actual values, in the original units.
- **MSE (Mean Squared Error)** — the average squared difference; penalizes large errors more heavily than MAE.
- **RMSE (Root Mean Squared Error)** — the square root of MSE, back in the original units.
- **R² (R-squared)** — the proportion of variance in the target explained by the model, from 0 to 1 (higher is better).

```python
from sklearn.metrics import mean_absolute_error, mean_squared_error, r2_score

mae = mean_absolute_error(y_test, predictions)
r2 = r2_score(y_test, predictions)
```

---

[Previous](./[26]-Support-Vector-Machines.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[28]-Clustering.md)
