*Supervised Learning*

# Lesson 24 - Linear & Logistic Regression

[Previous](./[23]-Feature-Engineering-and-Selection.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[25]-Decision-Trees-and-Random-Forests.md)

---

## 24.1 Linear Regression

**Linear regression** predicts a continuous numeric value by fitting a straight-line (or hyperplane, with multiple features) relationship between inputs and the output:

```
y = b0 + b1*x1 + b2*x2 + ... + bn*xn
```

Here `b0` is the intercept and each `b1...bn` is a coefficient showing how much the output changes per unit increase in that feature (holding others constant).

```python
from sklearn.linear_model import LinearRegression

model = LinearRegression()
model.fit(X_train, y_train)
predictions = model.predict(X_test)

print(model.coef_)         # learned coefficients
print(model.intercept_)      # learned intercept
```

The model is typically fit by minimizing the **sum of squared errors** between predicted and actual values (a method called ordinary least squares).

---

## 24.2 Logistic Regression

Despite the name, **logistic regression** is used for *classification*, not regression. It predicts the probability that an input belongs to a given class, by passing a linear combination of features through the **sigmoid function**, which squashes any number into a range between 0 and 1:

```
probability = 1 / (1 + e^-(b0 + b1*x1 + ... + bn*xn))
```

```python
from sklearn.linear_model import LogisticRegression

model = LogisticRegression()
model.fit(X_train, y_train)
probabilities = model.predict_proba(X_test)   # probability of each class
predictions = model.predict(X_test)              # class with highest probability
```

---

## 24.3 Interpreting Coefficients

In linear regression, a coefficient of 2.5 for "years of experience" means: holding everything else constant, one more year of experience is associated with a 2.5-unit increase in the predicted output. In logistic regression, coefficients relate to the *log-odds* of the outcome, making direct interpretation slightly less intuitive but still directionally meaningful (a positive coefficient increases the probability of the positive class).

---

## 24.4 Assumptions and Limitations

Linear and logistic regression assume a roughly linear relationship between features and the (log-odds of the) target, and can struggle when relationships are highly non-linear or when features interact in complex ways. Their major advantage is **interpretability** — it's easy to explain exactly why the model made a given prediction, which matters in regulated industries like finance and healthcare, even when more complex models (Lessons 25-26, 31-34) might achieve slightly higher raw accuracy.

---

[Previous](./[23]-Feature-Engineering-and-Selection.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[25]-Decision-Trees-and-Random-Forests.md)
