*Exploratory Data Analysis*

# Lesson 13 - Exploratory Data Analysis (EDA) Techniques

[Previous](./[12]-Descriptive-Statistics.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[14]-Outlier-Detection.md)

---

## 13.1 What is EDA?

**Exploratory Data Analysis (EDA)** is the process of investigating a dataset — using summary statistics and visualizations — to understand its structure, spot problems, and generate hypotheses before formal modeling. It's usually the first thing you do after loading and cleaning data.

---

## 13.2 A Typical EDA Checklist

1. **Shape and types** — `df.shape`, `df.info()`, `df.dtypes`.
2. **Missing values** — `df.isnull().sum()` (Lesson 10).
3. **Summary statistics** — `df.describe()` (Lesson 12).
4. **Univariate exploration** — the distribution of each variable alone.
5. **Bivariate/multivariate exploration** — relationships between variables.
6. **Outliers and anomalies** — anything unusual (Lesson 14).

---

## 13.3 Univariate and Bivariate Analysis

```python
import seaborn as sns

# Univariate: one variable at a time
sns.histplot(df["age"])
df["city"].value_counts().plot(kind="bar")

# Bivariate: relationships between two variables
sns.scatterplot(data=df, x="age", y="income")
sns.boxplot(data=df, x="city", y="income")
pd.crosstab(df["city"], df["is_senior"])   # cross-tabulation of two categorical variables
```

---

## 13.4 Forming Hypotheses

Good EDA isn't just running code — it's asking questions of the data and following up: *Does income vary meaningfully by city? Is there a pattern between age and purchase amount? Are there subgroups that behave differently?*

These observations directly inform later steps: which features to engineer (Lesson 23), which statistical tests to run (Lesson 18), and which model type might fit best (Lessons 20-30). Treat EDA as detective work, not a checkbox — the patterns you notice here shape every decision downstream.

---

[Previous](./[12]-Descriptive-Statistics.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[14]-Outlier-Detection.md)
