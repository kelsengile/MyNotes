*Deep Learning*

# Lesson 31 - Introduction to Neural Networks

[Previous](./[30]-Anomaly-Detection.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[32]-Building-Neural-Networks-with-TensorFlow-PyTorch.md)

---

## 31.1 What is a Neural Network?

A **neural network** is a machine learning model loosely inspired by the brain's structure, made up of layers of connected "neurons." Each connection has a **weight**, and each neuron applies an **activation function** to a weighted sum of its inputs before passing a value onward. Networks with many layers between the input and output are called **deep neural networks**, hence "deep learning."

---

## 31.2 The Structure of a Network

- **Input layer** — receives the raw features.
- **Hidden layers** — intermediate layers that learn increasingly abstract representations of the data.
- **Output layer** — produces the final prediction (e.g. a probability for classification, a number for regression).

```
input -> [hidden layer 1] -> [hidden layer 2] -> ... -> output
```

Each connection between neurons has a learnable weight, and each neuron typically has a learnable bias term, similar to the intercept in linear regression (Lesson 24).

---

## 31.3 Activation Functions

Without a non-linear **activation function**, stacking layers would collapse into nothing more than a single linear model. Common activation functions:

- **ReLU** (Rectified Linear Unit) — outputs the input directly if positive, otherwise 0. The most common choice for hidden layers due to its simplicity and training efficiency.
- **Sigmoid** — squashes values to between 0 and 1, often used in the output layer for binary classification.
- **Softmax** — converts a set of numbers into probabilities that sum to 1, used in the output layer for multi-class classification.

---

## 31.4 Training: Forward Pass, Loss, and Backpropagation

Training a neural network involves:

1. **Forward pass** — input data flows through the network to produce a prediction.
2. **Loss calculation** — a loss function measures how wrong the prediction was compared to the actual label.
3. **Backpropagation** — the error is propagated backward through the network to calculate how much each weight contributed to the error.
4. **Gradient descent** — weights are adjusted slightly in the direction that reduces the loss, and the cycle repeats over many iterations ("epochs").

This training loop — forward pass, loss, backpropagation, weight update — is the foundation for every deep learning architecture covered in the rest of this section, including CNNs (Lesson 33) and RNNs (Lesson 34).

---

[Previous](./[30]-Anomaly-Detection.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[32]-Building-Neural-Networks-with-TensorFlow-PyTorch.md)
