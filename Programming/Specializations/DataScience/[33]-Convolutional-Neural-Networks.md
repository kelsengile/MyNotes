*Deep Learning*

# Lesson 33 - Convolutional Neural Networks (CNNs)

[Previous](./[32]-Building-Neural-Networks-with-TensorFlow-PyTorch.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[34]-Recurrent-Neural-Networks-and-Sequence-Models.md)

---

## 33.1 Why CNNs for Images?

A standard fully-connected network (Lesson 31) treats every pixel independently, ignoring the fact that nearby pixels are related and that the same visual pattern (like an edge or a shape) can appear anywhere in an image. **Convolutional Neural Networks (CNNs)** are designed specifically to exploit this spatial structure, making them the dominant architecture for image-related tasks.

---

## 33.2 Convolutional and Pooling Layers

- **Convolutional layers** slide small filters (kernels) across the image, each learning to detect a specific local pattern (an edge, a texture, eventually more complex shapes in deeper layers). This is far more parameter-efficient than connecting every pixel to every neuron.
- **Pooling layers** (commonly **max pooling**) shrink the spatial size of the data by keeping only the strongest signal in each small region, reducing computation and making the model more robust to small shifts in the image.

```python
from tensorflow.keras import layers, Sequential

model = Sequential([
    layers.Conv2D(32, (3, 3), activation="relu", input_shape=(64, 64, 3)),
    layers.MaxPooling2D((2, 2)),
    layers.Conv2D(64, (3, 3), activation="relu"),
    layers.MaxPooling2D((2, 2)),
    layers.Flatten(),
    layers.Dense(64, activation="relu"),
    layers.Dense(10, activation="softmax"),   # 10-class image classification
])
```

---

## 33.3 How CNNs Build Understanding in Layers

Early convolutional layers tend to learn simple features like edges and colors. Deeper layers combine these into increasingly complex patterns — textures, parts of objects, and eventually whole objects. This hierarchical feature learning is a major reason CNNs outperform traditional hand-engineered image features on most vision tasks.

---

## 33.4 Transfer Learning

Training a large CNN from scratch requires huge datasets and computing power. **Transfer learning** instead starts from a model already pretrained on a massive dataset (like ImageNet), and fine-tunes it on your smaller, specific dataset:

```python
from tensorflow.keras.applications import MobileNetV2

base_model = MobileNetV2(weights="imagenet", include_top=False, input_shape=(224, 224, 3))
base_model.trainable = False   # freeze the pretrained layers

model = Sequential([
    base_model,
    layers.GlobalAveragePooling2D(),
    layers.Dense(1, activation="sigmoid"),
])
```

Transfer learning is now the standard starting point for most real-world image classification projects, since it dramatically reduces the amount of data and training time needed.

---

[Previous](./[32]-Building-Neural-Networks-with-TensorFlow-PyTorch.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[34]-Recurrent-Neural-Networks-and-Sequence-Models.md)
