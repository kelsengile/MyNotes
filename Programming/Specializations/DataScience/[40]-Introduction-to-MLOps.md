*Model Deployment & MLOps*

# Lesson 40 - Introduction to MLOps

[Previous](./[39]-Building-a-Simple-ML-API.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[41]-Model-Monitoring-and-Drift.md)

---

## 40.1 What is MLOps?

**MLOps** (Machine Learning Operations) applies software engineering and DevOps practices to the machine learning lifecycle — covering everything from training and versioning to deployment and ongoing monitoring. Its goal is to make ML systems reliable, reproducible, and maintainable in production, not just accurate in a notebook.

---

## 40.2 The MLOps Lifecycle

1. **Data and model versioning** — tracking exactly which data and code produced a given model.
2. **Automated training pipelines** — retraining models on a schedule or when triggered by new data, rather than manually rerunning notebooks.
3. **CI/CD for ML** — automatically testing and deploying new model versions, similar to standard software CI/CD.
4. **Serving** — exposing the model for use (Lesson 39).
5. **Monitoring** — tracking model performance and data quality over time (Lesson 41).

---

## 40.3 Experiment Tracking

As teams train many model versions with different features, hyperparameters, and data, it becomes essential to track what was tried and what worked. Tools like **MLflow** log parameters, metrics, and model artifacts for every training run:

```python
import mlflow

with mlflow.start_run():
    mlflow.log_param("max_depth", 6)
    mlflow.log_metric("accuracy", 0.87)
    mlflow.sklearn.log_model(model, "model")
```

This is covered further, alongside reproducibility practices more broadly, in Lesson 46.

---

## 40.4 Why MLOps Matters

A model that works well in a notebook can fail silently in production for many reasons: the input data format changes upstream, the real-world data distribution shifts over time (see "drift" in Lesson 41), or a deployment introduces a subtle bug. MLOps practices exist specifically to catch these problems early and keep models reliable long after the initial project "ends" — in production, a model's lifecycle is ongoing, not a one-time deliverable.

---

[Previous](./[39]-Building-a-Simple-ML-API.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[41]-Model-Monitoring-and-Drift.md)
