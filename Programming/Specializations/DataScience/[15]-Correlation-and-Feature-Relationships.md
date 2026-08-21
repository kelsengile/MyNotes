*Exploratory Data Analysis*

# Lesson 15 - Correlation & Feature Relationships

[Previous](./[14]-Outlier-Detection.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[16]-Probability-Fundamentals.md)

---

## 15.1 What is Correlation?

**Correlation** measures the strength and direction of a linear relationship between two numeric variables, expressed as a value from -1 to +1:

- **+1** — perfect positive relationship (as one goes up, so does the other).
- **-1** — perfect negative relationship (as one goes up, the other goes down).
- **0** — no linear relationship.

```python
df["age"].corr(df["income"])      # correlation between two columns
df.corr()                            # full correlation matrix (all numeric columns)
```

The most common measure is **Pearson correlation**, which only captures *linear* relationships — two variables can be strongly related in a non-linear way and still show a Pearson correlation near 0.

---

## 15.2 Visualizing Correlation

```python
import seaborn as sns

sns.heatmap(df.corr(), annot=True, cmap="coolwarm")
sns.scatterplot(data=df, x="age", y="income")
sns.pairplot(df)   # scatter plots for every pair of numeric columns at once
```

Always pair a correlation number with a scatter plot — a single correlation coefficient can hide important structure (like a strong relationship that only holds within subgroups).

---

## 15.3 Correlation Is Not Causation

A high correlation between two variables does not mean one causes the other. Classic explanations for a correlation include:

- **Direct causation** (A causes B).
- **Reverse causation** (B actually causes A).
- **A confounding variable** causes both (e.g. ice cream sales and drowning deaths both rise in summer because of hot weather, not because of each other).
- **Coincidence**, especially in small samples.

This distinction matters enormously for decision-making — it's why controlled experiments and careful hypothesis testing (Lesson 18) matter, not just observed correlations.

---

## 15.4 Multicollinearity

When two or more input features are highly correlated with each other (not just with the outcome), this is called **multicollinearity**. It can make some machine learning models (especially linear regression, Lesson 24) unstable and hard to interpret, since it becomes difficult to tell which feature is really driving the outcome. Checking a correlation matrix of your features is a standard step before feature selection (Lesson 23).

---

[Previous](./[14]-Outlier-Detection.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[16]-Probability-Fundamentals.md)
