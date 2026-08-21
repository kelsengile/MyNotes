*Deep Learning*

# Lesson 32 - Building Neural Networks with TensorFlow/PyTorch

[Previous](./[31]-Introduction-to-Neural-Networks.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[33]-Convolutional-Neural-Networks.md)

---

## 32.1 TensorFlow (Keras) vs PyTorch

The two dominant deep learning frameworks:

- **TensorFlow** (usually via its high-level **Keras** API) emphasizes simplicity and is popular in production and industry settings.
- **PyTorch** emphasizes flexibility and a more "Pythonic" feel, and is dominant in research settings.

Both provide the same core building blocks: tensors (multi-dimensional arrays similar to NumPy arrays), automatic differentiation for backpropagation, and prebuilt layers.

---

## 32.2 Building a Simple Network in Keras

```python
from tensorflow import keras
from tensorflow.keras import layers

model = keras.Sequential([
    layers.Dense(64, activation="relu", input_shape=(10,)),
    layers.Dense(32, activation="relu"),
    layers.Dense(1, activation="sigmoid"),   # binary classification output
])

model.compile(optimizer="adam", loss="binary_crossentropy", metrics=["accuracy"])
model.fit(X_train, y_train, epochs=20, batch_size=32, validation_split=0.2)
```

---

## 32.3 Building the Same Network in PyTorch

```python
import torch
import torch.nn as nn

class SimpleNet(nn.Module):
    def __init__(self):
        super().__init__()
        self.layer1 = nn.Linear(10, 64)
        self.layer2 = nn.Linear(64, 32)
        self.output = nn.Linear(32, 1)

    def forward(self, x):
        x = torch.relu(self.layer1(x))
        x = torch.relu(self.layer2(x))
        return torch.sigmoid(self.output(x))

model = SimpleNet()
optimizer = torch.optim.Adam(model.parameters(), lr=0.001)
loss_fn = nn.BCELoss()
```

PyTorch requires writing out the training loop manually (forward pass, loss, `.backward()`, optimizer step), giving more control at the cost of more boilerplate compared to Keras's `.fit()`.

---

## 32.4 Key Training Concepts

- **Epoch** — one full pass through the entire training dataset.
- **Batch size** — how many samples are processed before the model's weights are updated once.
- **Learning rate** — how large a step the optimizer takes when updating weights; too high can cause unstable training, too low can make training very slow.
- **Optimizer** — the algorithm that updates weights based on gradients (Adam is the most widely used default today).

Monitoring training and validation loss/accuracy over epochs (often plotted as curves) is the standard way to spot overfitting during deep learning training, just as with the models in Lesson 22.

---

[Previous](./[31]-Introduction-to-Neural-Networks.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[33]-Convolutional-Neural-Networks.md)
