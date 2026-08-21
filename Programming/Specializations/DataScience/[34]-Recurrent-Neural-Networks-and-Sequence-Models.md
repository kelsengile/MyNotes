*Deep Learning*

# Lesson 34 - Recurrent Neural Networks & Sequence Models

[Previous](./[33]-Convolutional-Neural-Networks.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[35]-Text-Preprocessing-and-Tokenization.md)

---

## 34.1 Why Sequence Models?

Some data has an inherent order where earlier values influence later ones — text, time series, audio, sensor readings over time. Standard feedforward networks (Lesson 31) and CNNs (Lesson 33) have no built-in concept of sequence order. **Recurrent Neural Networks (RNNs)** are designed to process sequences step by step, carrying forward a "memory" of what came before.

---

## 34.2 How RNNs Work

An RNN processes one element of a sequence at a time, maintaining a **hidden state** that gets updated at each step and carries information from previous steps forward:

```
hidden_state[t] = f(input[t], hidden_state[t-1])
```

This lets an RNN, in theory, use context from earlier in a sequence (e.g. earlier words in a sentence) to inform predictions about later elements.

---

## 34.3 LSTMs and GRUs

Plain RNNs struggle to retain information over long sequences, a problem known as the **vanishing gradient problem**, where the influence of early inputs fades out over many steps. Two improved architectures solve this with gating mechanisms that control what information to keep or forget:

- **LSTM (Long Short-Term Memory)** — uses input, forget, and output gates to carefully manage what's remembered over long sequences.
- **GRU (Gated Recurrent Unit)** — a simpler, faster alternative to LSTM with fewer gates, often achieving similar performance.

```python
from tensorflow.keras import layers, Sequential

model = Sequential([
    layers.Embedding(input_dim=10000, output_dim=64),
    layers.LSTM(64),
    layers.Dense(1, activation="sigmoid"),
])
```

---

## 34.4 Applications and the Shift to Transformers

RNNs and LSTMs were long the standard for tasks like language modeling, machine translation, and time series forecasting. However, because they must process sequences step by step, they're slow to train on long sequences and struggle to capture very long-range dependencies. This limitation motivated the **Transformer** architecture (Lesson 37), which processes entire sequences in parallel using an "attention" mechanism, and has now largely replaced RNNs for most large-scale language tasks — though RNNs remain useful for smaller-scale or resource-constrained sequence problems.

---

[Previous](./[33]-Convolutional-Neural-Networks.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[35]-Text-Preprocessing-and-Tokenization.md)
