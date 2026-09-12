[Previous](./[6]-Knowledge-Representation-And-Reasoning.md) | [Table of Contents](./[0]-Introduction-to-ArtificialIntelligence.md) | [Next](./[8]-Supervised-Learning.md)

*Machine Learning*

# Lesson 7 - Introduction To Machine Learning

## 7.1 What Is Machine Learning

**Machine learning (ML)** is a subfield of AI in which systems improve their performance on a task by learning patterns from data, rather than following explicitly programmed rules. Formally, a program is said to "learn" if its performance at some task improves with experience, as measured by some performance metric. Machine learning is the technique behind most of the AI systems in everyday use today, from spam filters to voice assistants to recommendation engines.

---

## 7.2 The Machine Learning Workflow

A typical machine learning project follows a repeatable sequence of steps: **collecting data** relevant to the task, **cleaning and preparing** that data (see Lesson 4), **choosing a model** and set of features, **training** the model on the data, **evaluating** its performance, and **deploying** it for real use (see Lesson 16). This workflow is rarely a straight line — poor evaluation results often send you back to adjust features, gather more data, or try a different model, so most real projects loop through these steps multiple times before arriving at something worth deploying.

```mermaid
flowchart LR
    A[Collect Data] --> B[Clean & Prepare]
    B --> C[Choose Model]
    C --> D[Train]
    D --> E[Evaluate]
    E -->|Not good enough| B
    E -->|Good enough| F[Deploy]
```

> 💡 **Analogy:** This loop is a lot like editing an essay: draft, get feedback, revise, get feedback again. Very few essays — or models — are perfect on the first try.

---

## 7.3 Training, Validation, And Testing

Machine learning practitioners typically split their data into three sets: a **training set** used to actually fit the model's parameters, a **validation set** used to tune choices about the model (like how complex it should be) without touching the final test data, and a **test set** used only once, at the end, to get an honest estimate of how the model will perform on new, unseen data. Keeping these sets separate is essential — evaluating a model on the same data it was trained on gives a falsely optimistic picture of how well it will actually perform in the real world.

**🔍 Quick Example:** Think of studying for an exam. The **training set** is your homework problems, the **validation set** is a practice exam you use to see what to study more, and the **test set** is the real final exam — one you should never have seen the answers to beforehand.

| Split | Purpose | Used How Often |
|---|---|---|
| Training | Fit the model's parameters | Repeatedly |
| Validation | Tune model choices | Repeatedly |
| Test | Final honest performance check | Once, at the end |

---

## 7.4 Overfitting And Underfitting

**Overfitting** happens when a model learns the training data too closely, including its noise and quirks, so that it performs very well on training data but poorly on new data — like a student who memorizes exam answers instead of understanding the material. **Underfitting** happens when a model is too simple to capture the real pattern in the data at all, performing poorly even on the training set. Good machine learning practice aims for a balance between these two: a model complex enough to capture real patterns, but not so complex that it memorizes noise, often achieved through techniques like regularization, more training data, or careful model selection.

```
Underfitting          Just Right          Overfitting
   ___                 ___                  /\/\/\
  /   \      vs.      /   \       vs.      /      \  <- chases every
 low accuracy       good accuracy         noisy point
 everywhere                               (fails on new data)
```

[Previous](./[6]-Knowledge-Representation-And-Reasoning.md) | [Table of Contents](./[0]-Introduction-to-ArtificialIntelligence.md) | [Next](./[8]-Supervised-Learning.md)
