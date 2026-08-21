*Big Data & Tools*

# Lesson 44 - Data Pipelines & ETL Basics

[Previous](./[43]-Working-with-SQL-for-Data-Science.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[45]-Data-Ethics-and-Bias-in-ML.md)

---

## 44.1 What is ETL?

**ETL** stands for **Extract, Transform, Load** — the standard pattern for moving data from source systems into a place where it can be analyzed:

- **Extract** — pull raw data from its source (databases, APIs, files — Lessons 8-9).
- **Transform** — clean, reshape, and combine the data (Lessons 10-11).
- **Load** — write the processed data into its destination (a data warehouse, a database table, a file store).

A related pattern, **ELT** (Extract, Load, Transform), loads raw data first and transforms it afterward, often directly inside a powerful data warehouse — increasingly common as cloud data warehouses have become powerful enough to handle transformation themselves.

---

## 44.2 Data Pipelines

A **data pipeline** is the automated sequence of steps that moves data through an ETL/ELT process on a recurring schedule, rather than being run manually each time. A simple pipeline might run nightly to pull yesterday's sales data, clean it, and load it into a reporting table.

```python
def run_pipeline():
    raw_data = extract_from_database()
    cleaned_data = transform(raw_data)
    load_to_warehouse(cleaned_data)

# Scheduled to run automatically, e.g. via a cron job or an orchestration tool
```

---

## 44.3 Orchestration Tools

Real pipelines usually involve many interdependent steps (extract from three sources, then join, then clean, then load), and need to handle failures and retries gracefully. **Orchestration tools** like **Apache Airflow** manage this complexity by defining pipelines as a graph of tasks with dependencies:

```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime

with DAG("daily_sales_pipeline", start_date=datetime(2026, 1, 1), schedule_interval="@daily") as dag:
    extract_task = PythonOperator(task_id="extract", python_callable=extract_from_database)
    transform_task = PythonOperator(task_id="transform", python_callable=transform)
    load_task = PythonOperator(task_id="load", python_callable=load_to_warehouse)

    extract_task >> transform_task >> load_task   # defines the order of execution
```

---

## 44.4 Data Warehouses and Data Lakes

- A **data warehouse** stores structured, cleaned data optimized for fast querying and reporting (e.g. Snowflake, BigQuery, Redshift).
- A **data lake** stores raw data in its original format (structured or unstructured), offering flexibility at the cost of requiring more processing before it's analysis-ready.

Many organizations use both together: a data lake for raw storage, and a warehouse for the cleaned, query-ready version that analysts and data scientists actually work with day to day.

---

[Previous](./[43]-Working-with-SQL-for-Data-Science.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[45]-Data-Ethics-and-Bias-in-ML.md)
