*Exploratory Data Analysis*

# Lesson 12 - Descriptive Statistics

[Previous](./[11]-Data-Wrangling-and-Transformation.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[13]-Exploratory-Data-Analysis-Techniques.md)

---

## 12.1 Measures of Central Tendency

These describe the "typical" value in a dataset:

- **Mean** — the arithmetic average. Sensitive to outliers.
- **Median** — the middle value when sorted. Robust to outliers.
- **Mode** — the most frequently occurring value.

```python
df["age"].mean()
df["age"].median()
df["age"].mode()[0]
```

If mean and median differ a lot, that's a sign the data is skewed or has outliers.

---

## 12.2 Measures of Spread

These describe how much the data varies:

- **Range** — max minus min.
- **Variance** — the average squared distance from the mean.
- **Standard deviation** — the square root of variance, in the same units as the data (easier to interpret than variance).
- **Interquartile range (IQR)** — the range of the middle 50% of the data (75th percentile minus 25th percentile), robust to outliers.

```python
df["age"].std()
df["age"].var()
df["age"].quantile(0.25)
df["age"].quantile(0.75)
df.describe()   # generates count, mean, std, min, quartiles, max all at once
```

---

## 12.3 Distribution Shape

- **Skewness** measures asymmetry — a positive skew means a long tail toward high values (e.g. income data).
- **Kurtosis** measures how heavy the tails are compared to a normal distribution (more on the normal distribution in Lesson 17).

```python
df["age"].skew()
df["age"].kurt()
```

---

## 12.4 Summarizing Categorical Data

```python
df["city"].value_counts()               # frequency of each category
df["city"].value_counts(normalize=True)   # as proportions instead of counts
df.groupby("city")["age"].describe()        # numeric summary within each category
```

Descriptive statistics are the foundation for everything that follows — they're the first thing to compute on any new dataset, before any visualization or modeling.

---

[Previous](./[11]-Data-Wrangling-and-Transformation.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[13]-Exploratory-Data-Analysis-Techniques.md)
