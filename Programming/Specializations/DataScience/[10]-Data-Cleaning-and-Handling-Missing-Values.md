*Data Collection & Cleaning*

# Lesson 10 - Data Cleaning & Handling Missing Values

[Previous](./[9]-Web-Scraping-and-APIs-for-Data-Collection.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[11]-Data-Wrangling-and-Transformation.md)

---

## 10.1 Detecting Missing Data

```python
df.isnull().sum()          # count of missing values per column
df.isnull().mean() * 100     # percentage missing per column
df[df["age"].isnull()]         # rows where age is missing
```

Missing values in Pandas usually appear as `NaN` (Not a Number). Understanding *why* data is missing matters: it could be missing completely at random, or systematically (e.g. a survey question skipped by a specific group), which affects how safely you can handle it.

---

## 10.2 Strategies for Handling Missing Values

```python
df.dropna()                             # drop rows with any missing value
df.dropna(subset=["age"])                 # drop rows missing a specific column
df.dropna(thresh=3)                        # keep rows with at least 3 non-null values

df["age"].fillna(df["age"].mean())            # fill with the mean
df["city"].fillna(df["city"].mode()[0])         # fill with the most common value
df["age"].fillna(method="ffill")                  # forward-fill from the previous row
```

Dropping data is simplest but loses information; filling ("imputing") preserves rows but can introduce bias if done carelessly — always document which strategy you used and why.

---

## 10.3 Fixing Inconsistent Data

```python
df["city"] = df["city"].str.strip().str.lower()          # remove whitespace, standardize case
df["city"] = df["city"].replace({"nyc": "new york"})        # standardize known variants
df["age"] = pd.to_numeric(df["age"], errors="coerce")          # force to numeric, invalid -> NaN
df = df.drop_duplicates()                                        # remove exact duplicate rows
```

Inconsistent categorical labels (`"NYC"`, `"nyc"`, `"New York"` all meaning the same city) are one of the most common real-world data quality problems.

---

## 10.4 Detecting Data Type and Range Errors

```python
df.dtypes                                  # check each column's data type
df[df["age"] < 0]                            # find impossible values
df[df["age"] > 120]                            # find implausible values
```

Cleaning is rarely a one-shot process — it's normal to move back and forth between cleaning and exploration (Lessons 12-15) as you discover new issues in the data.

---

[Previous](./[9]-Web-Scraping-and-APIs-for-Data-Collection.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[11]-Data-Wrangling-and-Transformation.md)
