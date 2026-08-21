*Best Practices*

# Lesson 46 - Reproducibility & Experiment Tracking

[Previous](./[45]-Data-Ethics-and-Bias-in-ML.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[47]-Communicating-Results-and-Data-Storytelling.md)

---

## 46.1 Why Reproducibility Matters

A **reproducible** analysis or model is one that someone else (or you, months later) can rerun and get the same results. Without it, findings can't be verified, bugs are hard to trace, and models can't be reliably retrained or audited. Reproducibility is a prerequisite for trustworthy data science, not an optional nicety.

---

## 46.2 Setting Random Seeds

Many algorithms involve randomness (random train/test splits, random forest tree sampling, neural network weight initialization). Setting a fixed **random seed** ensures the same "random" choices happen every time the code runs:

```python
import numpy as np
import random

np.random.seed(42)
random.seed(42)

# scikit-learn's random_state parameter serves the same purpose
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, random_state=42)
```

---

## 46.3 Environment and Dependency Management

Code that works today can break in the future if library versions change. Recording exact dependencies avoids this:

```bash
pip freeze > requirements.txt          # captures exact installed package versions
pip install -r requirements.txt          # recreates the same environment elsewhere

conda env export > environment.yml         # equivalent for Conda environments
```

Combined with Git for code (Lesson 3) and a fixed random seed, this covers the three pillars of reproducibility: same code, same environment, same randomness.

---

## 46.4 Experiment Tracking

As introduced in Lesson 40, tools like **MLflow** or **Weights & Biases** systematically log the parameters, code version, metrics, and resulting model for every training run, making it possible to compare experiments and reproduce the exact conditions that produced any past result:

```python
import mlflow

with mlflow.start_run():
    mlflow.log_param("model_type", "random_forest")
    mlflow.log_param("n_estimators", 200)
    mlflow.log_metric("f1_score", 0.84)
```

Over the life of a project, this log becomes an invaluable record of what was tried, what worked, and why — invaluable both for the current team and for anyone auditing the model later.

---

[Previous](./[45]-Data-Ethics-and-Bias-in-ML.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[47]-Communicating-Results-and-Data-Storytelling.md)
