*Big Data & Tools*

# Lesson 42 - Introduction to Big Data (Spark Basics)

[Previous](./[41]-Model-Monitoring-and-Drift.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[43]-Working-with-SQL-for-Data-Science.md)

---

## 42.1 What Counts as "Big Data"?

**Big data** refers to datasets too large, fast-moving, or complex to process efficiently with standard tools like Pandas on a single machine. It's often described by the "3 Vs": **Volume** (size), **Velocity** (speed of arrival), and **Variety** (mix of structured and unstructured formats). When a dataset no longer fits comfortably in a single computer's memory, distributed computing tools become necessary.

---

## 42.2 What is Apache Spark?

**Apache Spark** is the most widely used framework for distributed data processing — it splits data and computation across many machines (a "cluster") so large datasets can be processed in parallel, far faster than on a single machine.

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("DataScienceCourse").getOrCreate()
df = spark.read.csv("large_dataset.csv", header=True, inferSchema=True)
df.show(5)
```

---

## 42.3 Spark DataFrames vs Pandas

Spark's DataFrame API deliberately resembles Pandas, making the transition easier:

```python
df.filter(df["income"] > 50000).select("name", "income").show()
df.groupBy("city").agg({"income": "mean"}).show()
```

The key difference is **lazy evaluation**: Spark doesn't actually run these operations immediately — it builds up a plan of transformations and only executes them when an "action" (like `.show()` or `.collect()`) is called, allowing it to optimize the entire computation before running it across the cluster.

---

## 42.4 When to Reach for Spark

Spark (and similar distributed tools) becomes worthwhile once data no longer fits comfortably in memory on one machine, or when processing needs to be distributed across many machines for speed. For datasets that fit comfortably in memory — the large majority of everyday data science work — Pandas remains simpler and faster to work with; reaching for Spark too early adds unnecessary complexity.

---

[Previous](./[41]-Model-Monitoring-and-Drift.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[43]-Working-with-SQL-for-Data-Science.md)
