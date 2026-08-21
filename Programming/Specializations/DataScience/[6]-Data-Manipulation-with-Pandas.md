*Core Programming for Data Science*

# Lesson 6 - Data Manipulation with Pandas

[Previous](./[5]-Working-with-NumPy-Arrays.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[7]-Data-Visualization-Basics.md)

---

## 6.1 Series and DataFrames

**Pandas** is the standard library for working with tabular (spreadsheet-like) data in Python. It has two core structures:

- A **Series** is a single labeled column of data.
- A **DataFrame** is a full table — a collection of Series sharing the same row index.

```python
import pandas as pd

data = {
    "name": ["Ada", "Grace", "Alan"],
    "age": [30, 45, 41],
    "city": ["NYC", "DC", "London"],
}
df = pd.DataFrame(data)
print(df.head())     # first 5 rows
print(df.shape)       # (3, 3)
print(df.columns)      # Index(['name', 'age', 'city'])
print(df.dtypes)        # data type of each column
```

---

## 6.2 Selecting and Filtering Data

```python
df["age"]                      # select one column (returns a Series)
df[["name", "age"]]             # select multiple columns

df.loc[0]                        # select row by label
df.iloc[0]                        # select row by integer position

df[df["age"] > 40]                 # filter rows where age > 40
df[(df["age"] > 30) & (df["city"] == "NYC")]  # combine conditions with & / |
```

Always use parentheses around each condition when combining filters with `&` (and) or `|` (or) — Python's operator precedence requires it.

---

## 6.3 Transforming Data

```python
df["age_next_year"] = df["age"] + 1        # create a new column
df["is_senior"] = df["age"] >= 40            # boolean column

df.sort_values("age", ascending=False)         # sort rows
df.rename(columns={"name": "full_name"})         # rename columns

df.groupby("city")["age"].mean()                   # average age per city
df.apply(lambda row: row["age"] * 2, axis=1)         # custom row-wise logic
```

`groupby` is one of the most powerful tools in Pandas — it splits data into groups, applies a function to each group, and combines the results, following the "split-apply-combine" pattern.

---

## 6.4 Combining DataFrames

```python
pd.concat([df1, df2])                                    # stack rows together
pd.merge(orders, customers, on="customer_id", how="left")  # SQL-style join
```

Join types (`how=`) mirror SQL: `"inner"` keeps only matching rows, `"left"`/`"right"` keep all rows from one side, and `"outer"` keeps everything. Choosing the right join type is essential to avoid silently dropping or duplicating data.

---

[Previous](./[5]-Working-with-NumPy-Arrays.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[7]-Data-Visualization-Basics.md)
