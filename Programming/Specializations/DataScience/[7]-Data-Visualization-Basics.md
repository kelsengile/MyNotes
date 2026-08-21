*Core Programming for Data Science*

# Lesson 7 - Data Visualization Basics (Matplotlib, Seaborn)

[Previous](./[6]-Data-Manipulation-with-Pandas.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[8]-Reading-Data-from-Files-and-Databases.md)

---

## 7.1 Why Visualize Data?

Charts reveal patterns — trends, outliers, distributions, relationships — that are hard to spot in raw numbers. Visualization is used at two different points in a project: **exploratory** (quick charts to understand your data, covered more in Lesson 13) and **explanatory** (polished charts to communicate findings to others, covered in Lesson 47).

---

## 7.2 Matplotlib Basics

**Matplotlib** is the foundational plotting library in Python — most other visualization libraries (including Seaborn) are built on top of it.

```python
import matplotlib.pyplot as plt

x = [1, 2, 3, 4, 5]
y = [10, 20, 15, 25, 30]

plt.plot(x, y)                  # line chart
plt.scatter(x, y)                # scatter plot
plt.bar(["A", "B", "C"], [5, 8, 3])  # bar chart
plt.hist(y, bins=5)                    # histogram

plt.title("Sales Over Time")
plt.xlabel("Month")
plt.ylabel("Revenue")
plt.show()
```

---

## 7.3 Seaborn for Statistical Plots

**Seaborn** builds on Matplotlib to provide attractive, statistically-aware charts with less code, and integrates directly with Pandas DataFrames.

```python
import seaborn as sns

sns.histplot(data=df, x="age")                       # distribution of one column
sns.scatterplot(data=df, x="age", y="income")           # relationship between two columns
sns.boxplot(data=df, x="city", y="age")                   # distribution by category
sns.heatmap(df.corr(), annot=True)                          # correlation matrix
sns.pairplot(df)                                              # all pairwise relationships at once
```

---

## 7.4 Choosing the Right Chart

- **Line chart** — trends over time or an ordered sequence.
- **Bar chart** — comparing quantities across categories.
- **Histogram** — the distribution/spread of a single numeric variable.
- **Scatter plot** — the relationship between two numeric variables.
- **Box plot** — comparing distributions (and spotting outliers) across groups.
- **Heatmap** — a matrix of values, often correlations, shown by color intensity.

A good habit: always label your axes and give the chart a title — a chart that requires guessing what it shows has failed at its job.

---

[Previous](./[6]-Data-Manipulation-with-Pandas.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[8]-Reading-Data-from-Files-and-Databases.md)
