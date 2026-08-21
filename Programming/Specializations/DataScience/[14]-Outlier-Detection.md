*Exploratory Data Analysis*

# Lesson 14 - Outlier Detection

[Previous](./[13]-Exploratory-Data-Analysis-Techniques.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[15]-Correlation-and-Feature-Relationships.md)

---

## 14.1 What Counts as an Outlier?

An **outlier** is a data point that differs substantially from the rest of the dataset. Outliers can be genuine (a legitimate but rare extreme value), or errors (a data entry mistake, sensor glitch, or unit mismatch). Deciding which requires domain knowledge — don't remove a value just because it's extreme without understanding why it's there.

---

## 14.2 The IQR Method

A common statistical rule of thumb: any value more than 1.5 times the interquartile range (IQR) below Q1 or above Q3 is flagged as an outlier.

```python
Q1 = df["income"].quantile(0.25)
Q3 = df["income"].quantile(0.75)
IQR = Q3 - Q1

lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR

outliers = df[(df["income"] < lower_bound) | (df["income"] > upper_bound)]
```

Box plots (Lesson 7) visualize this rule directly — points beyond the "whiskers" are flagged outliers.

---

## 14.3 The Z-Score Method

The **z-score** measures how many standard deviations a value is from the mean. A common threshold flags values with |z| > 3 as outliers — this method assumes the data is roughly normally distributed (Lesson 17).

```python
from scipy import stats
import numpy as np

df["z_score"] = stats.zscore(df["income"])
outliers = df[np.abs(df["z_score"]) > 3]
```

---

## 14.4 Handling Outliers

Once identified, options include:

- **Investigate first** — confirm whether it's a data error or a real extreme case.
- **Remove** — if confirmed to be an error and not meaningful to the analysis.
- **Cap/Winsorize** — clip extreme values to a fixed percentile instead of deleting them.
- **Transform** — apply a log transform to reduce the influence of extreme values.
- **Keep and use robust methods** — some models and statistics (like the median) are naturally less sensitive to outliers.

```python
df["income_capped"] = df["income"].clip(lower=lower_bound, upper=upper_bound)
df["income_log"] = np.log1p(df["income"])   # log(1 + x), handles zero values safely
```

---

[Previous](./[13]-Exploratory-Data-Analysis-Techniques.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[15]-Correlation-and-Feature-Relationships.md)
