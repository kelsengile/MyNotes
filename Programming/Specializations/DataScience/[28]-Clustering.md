*Unsupervised Learning*

# Lesson 28 - Clustering (K-Means, Hierarchical)

[Previous](./[27]-Model-Evaluation-Metrics.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[29]-Dimensionality-Reduction.md)

---

## 28.1 What is Clustering?

**Clustering** groups similar data points together based on their features, without using any predefined labels. It's commonly used for customer segmentation, grouping similar documents, or discovering natural categories in data during EDA (Lesson 13).

---

## 28.2 K-Means Clustering

**K-Means** is the most widely used clustering algorithm. Given a chosen number of clusters *k*, it:

1. Randomly places *k* cluster centers ("centroids").
2. Assigns each point to its nearest centroid.
3. Recalculates each centroid as the mean of its assigned points.
4. Repeats steps 2-3 until assignments stop changing.

```python
from sklearn.cluster import KMeans

model = KMeans(n_clusters=3, random_state=42)
labels = model.fit_predict(X_scaled)   # cluster assignment for each row
```

Because K-Means relies on distance calculations, features should always be scaled first (Lesson 23).

---

## 28.3 Choosing the Number of Clusters

Since *k* must be chosen in advance, two common techniques help:

- **The elbow method** — plot the within-cluster variance ("inertia") against different values of *k*, and look for the point where adding more clusters stops helping much (the "elbow" of the curve).
- **Silhouette score** — measures how similar each point is to its own cluster compared to other clusters, ranging from -1 to 1 (higher is better).

```python
from sklearn.metrics import silhouette_score

score = silhouette_score(X_scaled, labels)
```

---

## 28.4 Hierarchical Clustering

**Hierarchical clustering** builds a tree of nested clusters (a **dendrogram**) instead of requiring you to pick *k* upfront. The most common form, **agglomerative clustering**, starts with every point as its own cluster and repeatedly merges the closest pair of clusters until only one remains — you then choose how many clusters to keep by "cutting" the tree at a chosen height.

```python
from scipy.cluster.hierarchy import dendrogram, linkage
from sklearn.cluster import AgglomerativeClustering

model = AgglomerativeClustering(n_clusters=3)
labels = model.fit_predict(X_scaled)
```

Hierarchical clustering is more computationally expensive than K-Means but doesn't require guessing *k* in advance and provides a rich view of how clusters relate to one another.

---

[Previous](./[27]-Model-Evaluation-Metrics.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[29]-Dimensionality-Reduction.md)
