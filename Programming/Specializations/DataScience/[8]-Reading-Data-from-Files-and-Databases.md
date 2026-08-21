*Data Collection & Cleaning*

# Lesson 8 - Reading Data from Files & Databases

[Previous](./[7]-Data-Visualization-Basics.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[9]-Web-Scraping-and-APIs-for-Data-Collection.md)

---

## 8.1 Reading Common File Formats

```python
import pandas as pd

csv_df = pd.read_csv("data.csv")
excel_df = pd.read_excel("data.xlsx", sheet_name="Sheet1")
json_df = pd.read_json("data.json")
parquet_df = pd.read_parquet("data.parquet")  # a compressed, columnar format popular for big data
```

**CSV** (comma-separated values) is the most common interchange format. **Parquet** is more efficient for large datasets since it stores data by column and supports compression, which matters when working with the big data tools in Lesson 42.

---

## 8.2 Handling Messy File Reads

Real-world files are rarely perfectly clean. Common arguments to `read_csv` handle this:

```python
pd.read_csv("data.csv", sep=";")                 # non-comma delimiter
pd.read_csv("data.csv", encoding="latin-1")        # non-UTF-8 encoding
pd.read_csv("data.csv", na_values=["N/A", "--"])     # custom missing-value markers
pd.read_csv("data.csv", parse_dates=["order_date"])    # parse a column as datetime
```

---

## 8.3 Connecting to Databases with SQL

Pandas can query relational databases directly using SQL (covered in depth in Lesson 43):

```python
import sqlite3
import pandas as pd

conn = sqlite3.connect("sales.db")
df = pd.read_sql("SELECT * FROM orders WHERE amount > 100", conn)
conn.close()
```

For production systems, connection libraries like `sqlalchemy` or `psycopg2` (PostgreSQL) are used similarly to connect to larger database servers rather than a local file.

---

## 8.4 Saving Data Back Out

```python
df.to_csv("cleaned_data.csv", index=False)
df.to_parquet("cleaned_data.parquet")
df.to_sql("cleaned_orders", conn, if_exists="replace")
```

Setting `index=False` when saving to CSV avoids writing an extra unnamed column for the DataFrame's row index — a very common beginner mistake.

---

[Previous](./[7]-Data-Visualization-Basics.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[9]-Web-Scraping-and-APIs-for-Data-Collection.md)
