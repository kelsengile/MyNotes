*Machine Learning Fundamentals*

# Lesson 20 - Introduction to Machine Learning

[Previous](./[19]-Statistical-Inference-and-Confidence-Intervals.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[21]-Supervised-vs-Unsupervised-Learning.md)

---

## 20.1 What is Machine Learning?

**Machine Learning (ML)** is a set of techniques that let computers learn patterns from data and make predictions or decisions without being explicitly programmed with rules for every case. Instead of writing `if/else` logic by hand, you provide examples, and an algorithm learns the underlying pattern from them.

Traditional programming: `rules + data -> output`.
Machine learning: `data + output -> rules (the model)`.

---

## 20.2 Types of Machine Learning

- **Supervised learning** — learning from labeled examples (input/output pairs) to predict outputs for new inputs (Lesson 21).
- **Unsupervised learning** — finding structure in unlabeled data, like grouping similar items (Lesson 21, 28-30).
- **Reinforcement learning** — an agent learns by taking actions in an environment and receiving rewards or penalties, used in robotics, games, and recommendation systems.

This course focuses primarily on supervised and unsupervised learning, which cover the large majority of real-world business applications.

---

## 20.3 Core Machine Learning Vocabulary

- **Features** — the input variables used to make a prediction (also called predictors or independent variables).
- **Label/Target** — the value being predicted (also called the dependent variable).
- **Model** — the mathematical representation learned from data that maps features to a prediction.
- **Training** — the process of fitting a model to data.
- **Inference/Prediction** — using a trained model to make predictions on new data.

```python
from sklearn.linear_model import LinearRegression

model = LinearRegression()
model.fit(X_train, y_train)          # training
predictions = model.predict(X_test)     # inference
```

---

## 20.4 When (and When Not) to Use Machine Learning

ML is a great fit when there's a clear pattern to learn from a reasonable amount of historical data, and the problem is too complex for simple hand-written rules (e.g. image recognition, fraud detection). It's often *not* the right tool when a simple rule or formula already works well, when there's too little data, or when a decision needs to be fully explainable and auditable by law or policy — in those cases, simpler statistical or rule-based approaches may be more appropriate.

---

[Previous](./[19]-Statistical-Inference-and-Confidence-Intervals.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[21]-Supervised-vs-Unsupervised-Learning.md)
