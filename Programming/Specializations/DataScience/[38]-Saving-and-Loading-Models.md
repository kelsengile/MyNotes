*Model Deployment & MLOps*

# Lesson 38 - Saving & Loading Models

[Previous](./[37]-Introduction-to-Transformers-and-LLMs.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[39]-Building-a-Simple-ML-API.md)

---

## 38.1 Why Serialize a Model?

Training a model can take significant time and compute. **Serialization** saves a trained model's learned parameters to disk so it can be reloaded and reused later — for further evaluation, deployment, or sharing with others — without retraining from scratch each time.

---

## 38.2 Saving scikit-learn Models

The standard tool for scikit-learn models is `joblib`, which efficiently handles the NumPy arrays scikit-learn models are built from:

```python
import joblib

joblib.dump(model, "model.joblib")          # save
loaded_model = joblib.load("model.joblib")    # load
predictions = loaded_model.predict(X_new)
```

Python's built-in `pickle` module works similarly and is more general-purpose, but `joblib` is typically faster for large NumPy-based objects.

---

## 38.3 Saving Deep Learning Models

Deep learning frameworks provide their own save formats:

```python
# Keras / TensorFlow
model.save("my_model.keras")
loaded = keras.models.load_model("my_model.keras")

# PyTorch — commonly save just the learned weights ("state dict")
torch.save(model.state_dict(), "model_weights.pth")
model.load_state_dict(torch.load("model_weights.pth"))
```

For PyTorch, you need the original model class definition available when loading, since only the weights (not the architecture) are typically saved this way.

---

## 38.4 What to Save Alongside a Model

A saved model file alone is rarely enough for reliable reuse. It's important to also save (or clearly document):

- The exact **preprocessing steps** (scalers, encoders) used before training, so new data can be transformed identically.
- The **library versions** used (scikit-learn, TensorFlow, etc.), since model formats can break across versions.
- **Metadata** — training date, dataset version, and evaluation metrics, so the model's provenance is always traceable.

This groundwork is what makes it possible to reliably deploy the model as an API in the next lesson.

---

[Previous](./[37]-Introduction-to-Transformers-and-LLMs.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[39]-Building-a-Simple-ML-API.md)
