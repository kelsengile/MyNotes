*Unsupervised Learning*

# Lesson 29 - Dimensionality Reduction (PCA)

[Previous](./[28]-Clustering.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[30]-Anomaly-Detection.md)

---

## 29.1 Why Reduce Dimensionality?

When a dataset has many features (high dimensionality), it can suffer from the **curse of dimensionality**: models become harder to train, distance-based calculations become less meaningful, visualization becomes impossible beyond 2-3 dimensions, and the risk of overfitting increases. **Dimensionality reduction** techniques compress many features into fewer, while trying to preserve as much of the meaningful structure as possible.

---

## 29.2 Principal Component Analysis (PCA)

**PCA** is the most common dimensionality reduction technique. It finds new axes (called **principal components**) that are combinations of the original features, ordered so that the first component captures the most variance (spread) in the data, the second captures the next most (while being uncorrelated with the first), and so on.

```python
from sklearn.decomposition import PCA

pca = PCA(n_components=2)
X_reduced = pca.fit_transform(X_scaled)

print(pca.explained_variance_ratio_)   # e.g. [0.65, 0.20] — 85% of variance kept in 2 dimensions
```

Because PCA is based on variance and distances, features must be scaled (Lesson 23) before applying it.

---

## 29.3 Interpreting Principal Components

Each principal component is a weighted combination of the original features. Looking at these weights (loadings) can reveal which original features contribute most to each component — for example, a "size" component might combine height, weight, and shoe size, since they tend to vary together.

```python
import pandas as pd

loadings = pd.DataFrame(pca.components_, columns=X.columns)
```

A common use is reducing data to 2 dimensions purely for visualization purposes, to see if natural groupings appear before running a formal clustering algorithm (Lesson 28).

---

## 29.4 Other Dimensionality Reduction Techniques

- **t-SNE** and **UMAP** are non-linear techniques especially popular for visualizing high-dimensional data (like image or text embeddings) in 2D, preserving local neighborhood structure better than PCA, though they're less suited for general-purpose feature reduction before modeling.
- Dimensionality reduction is also used as a preprocessing step to speed up training and reduce noise before feeding data into a supervised model.

---

[Previous](./[28]-Clustering.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[30]-Anomaly-Detection.md)
