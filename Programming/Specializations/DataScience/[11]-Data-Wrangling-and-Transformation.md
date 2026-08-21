*Data Collection & Cleaning*

# Lesson 11 - Data Wrangling & Transformation

[Previous](./[10]-Data-Cleaning-and-Handling-Missing-Values.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[12]-Descriptive-Statistics.md)

---

## 11.1 Reshaping Data: Wide vs Long

Data can be organized "wide" (one row per subject, many columns) or "long" (one row per observation). Many tools expect one or the other.

```python
# Wide -> Long
long_df = wide_df.melt(id_vars="name", var_name="month", value_name="sales")

# Long -> Wide
wide_df = long_df.pivot(index="name", columns="month", values="sales")

# Pivot table with aggregation
pd.pivot_table(df, index="city", columns="year", values="sales", aggfunc="sum")
```

---

## 11.2 Binning and Encoding

```python
# Binning a continuous variable into categories
df["age_group"] = pd.cut(df["age"], bins=[0, 18, 35, 60, 100],
                          labels=["child", "young_adult", "adult", "senior"])

# One-hot encoding categorical variables (needed for many ML models, see Lesson 23)
df_encoded = pd.get_dummies(df, columns=["city"])

# Mapping categories to numbers
df["size_num"] = df["size"].map({"S": 1, "M": 2, "L": 3})
```

---

## 11.3 String and Date Wrangling

```python
df["email_domain"] = df["email"].str.split("@").str[1]      # extract text
df["name"] = df["name"].str.title()                              # standardize case

df["order_date"] = pd.to_datetime(df["order_date"])
df["year"] = df["order_date"].dt.year
df["day_of_week"] = df["order_date"].dt.day_name()
```

Extracting features like day-of-week or month from a date column is a common and powerful wrangling step before modeling.

---

## 11.4 Combining and Aggregating

```python
df.groupby(["city", "year"]).agg(
    total_sales=("sales", "sum"),
    avg_order=("sales", "mean"),
    orders=("order_id", "count")
).reset_index()
```

`.agg()` with named aggregations produces a clean, readable summary table in one step — this pattern is used constantly when preparing a dataset for both analysis and modeling.

---

[Previous](./[10]-Data-Cleaning-and-Handling-Missing-Values.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[12]-Descriptive-Statistics.md)
