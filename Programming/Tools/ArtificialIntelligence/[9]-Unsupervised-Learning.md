[Previous](./[8]-Supervised-Learning.md) | [Table of Contents](./[0]-Introduction-to-ArtificialIntelligence.md) | [Next](./[10]-Reinforcement-Learning.md)

*Machine Learning*

# Lesson 9 - Unsupervised Learning

## 9.1 What Is Unsupervised Learning

**Unsupervised learning** works with data that has no labels at all — the model isn't told the "correct" answer for any example, and instead must find structure or patterns in the data on its own. This is useful in situations where labeling data would be too expensive or simply isn't available, and it's also a way to explore a dataset and discover patterns a human might not have thought to look for.

---

## 9.2 Clustering

**Clustering** is the task of grouping data points so that points in the same group ("cluster") are more similar to each other than to points in other groups, without being told in advance what the groups should be. A common algorithm, **k-means clustering**, works by repeatedly assigning points to the nearest of *k* cluster centers and then recalculating those centers, until the groupings stabilize. Clustering is widely used for customer segmentation, grouping similar documents, and identifying naturally occurring categories in scientific data.

---

## 9.3 Dimensionality Reduction

Real-world datasets often have many features (dimensions), which can make them slow to process and hard to visualize. **Dimensionality reduction** techniques compress data into fewer dimensions while preserving as much important information as possible. **Principal Component Analysis (PCA)**, one of the most common techniques, finds the directions along which the data varies the most and represents the data using those directions instead of the original features. Dimensionality reduction is often used as a preprocessing step before other machine learning tasks, and to visualize high-dimensional data in two or three dimensions.

---

## 9.4 Anomaly Detection

**Anomaly detection** identifies data points that differ significantly from the majority of the data — an unusual pattern of transactions that might indicate credit card fraud, or a sensor reading that might indicate equipment failure. Because true anomalies are, by definition, rare, this is often approached as an unsupervised problem: the system learns what "normal" data looks like and flags anything that deviates substantially from that norm, rather than being trained on labeled examples of every possible type of anomaly (which may not even exist yet).

[Previous](./[8]-Supervised-Learning.md) | [Table of Contents](./[0]-Introduction-to-ArtificialIntelligence.md) | [Next](./[10]-Reinforcement-Learning.md)
