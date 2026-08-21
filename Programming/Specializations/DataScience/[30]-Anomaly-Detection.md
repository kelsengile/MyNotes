*Unsupervised Learning*

# Lesson 30 - Anomaly Detection

[Previous](./[29]-Dimensionality-Reduction.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[31]-Introduction-to-Neural-Networks.md)

---

## 30.1 What is Anomaly Detection?

**Anomaly detection** (also called outlier detection at the modeling level) identifies data points that differ significantly from the norm. Unlike the simple statistical outlier rules in Lesson 14, anomaly detection algorithms can find unusual patterns across many features at once. Common applications: fraud detection, network intrusion detection, manufacturing defect detection, and equipment failure prediction.

---

## 30.2 Isolation Forest

**Isolation Forest** detects anomalies based on a simple insight: anomalies are "few and different," so they're easier to isolate with fewer random splits than normal points. It builds many random decision trees and measures how quickly each point gets isolated — points isolated in fewer steps are flagged as more anomalous.

```python
from sklearn.ensemble import IsolationForest

model = IsolationForest(contamination=0.05, random_state=42)  # assume ~5% of data is anomalous
predictions = model.fit_predict(X)   # returns 1 for normal, -1 for anomaly
```

---

## 30.3 Other Approaches

- **Statistical methods** — using z-scores or IQR (Lesson 14) on individual features, useful for simple, single-variable cases.
- **DBSCAN** — a clustering algorithm that naturally labels points in low-density regions as noise/outliers, useful when anomalies form no clear structure.
- **Autoencoders** — a type of neural network (Lesson 31) trained to reconstruct normal data; when it's given anomalous data, its reconstruction error is much higher, flagging the anomaly.

```python
from sklearn.cluster import DBSCAN

model = DBSCAN(eps=0.5, min_samples=5)
labels = model.fit_predict(X_scaled)   # points labeled -1 are considered noise/outliers
```

---

## 30.4 Evaluating Anomaly Detection

Anomaly detection is often an unsupervised problem — you may not have confirmed labels for what counts as anomalous. When you do have some labeled examples (even a small number), the same classification metrics from Lesson 27 (precision, recall) apply, and **recall** is frequently prioritized, since missing a real anomaly (a fraudulent transaction, a failing machine) is usually far costlier than a false alarm.

---

[Previous](./[29]-Dimensionality-Reduction.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[31]-Introduction-to-Neural-Networks.md)
