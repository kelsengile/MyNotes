*Model Deployment & MLOps*

# Lesson 39 - Building a Simple ML API

[Previous](./[38]-Saving-and-Loading-Models.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[40]-Introduction-to-MLOps.md)

---

## 39.1 Why Wrap a Model in an API?

A trained model saved to disk (Lesson 38) isn't useful to other applications on its own. Wrapping it in a **web API** lets other software (a website, a mobile app, another service) send new data and receive predictions back over standard HTTP requests, without needing to know anything about how the model works internally.

---

## 39.2 Building an API with FastAPI

**FastAPI** is a popular, lightweight Python framework for building APIs, well suited to serving ML models:

```python
from fastapi import FastAPI
from pydantic import BaseModel
import joblib

app = FastAPI()
model = joblib.load("model.joblib")

class InputData(BaseModel):
    age: float
    income: float

@app.post("/predict")
def predict(data: InputData):
    features = [[data.age, data.income]]
    prediction = model.predict(features)
    return {"prediction": prediction[0]}
```

Running `uvicorn main:app --reload` starts a local server; sending a POST request to `/predict` with JSON input returns a prediction in response.

---

## 39.3 Testing the API

```bash
curl -X POST "http://127.0.0.1:8000/predict" \
     -H "Content-Type: application/json" \
     -d '{"age": 30, "income": 55000}'
```

FastAPI also auto-generates interactive API documentation (usually at `/docs`), which makes it easy to test endpoints directly from a browser during development.

---

## 39.4 Basic Deployment Considerations

Before an API goes to real users, a few things matter:

- **Input validation** — reject malformed requests early (frameworks like FastAPI handle much of this automatically via type hints).
- **Error handling** — return clear error messages rather than crashing on unexpected input.
- **Containerization** — packaging the API and its dependencies with **Docker** so it runs identically across different machines and environments.
- **Scaling** — running multiple copies of the API behind a load balancer to handle higher traffic.

These deployment concerns are the entry point into the broader discipline of MLOps, covered next.

---

[Previous](./[38]-Saving-and-Loading-Models.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[40]-Introduction-to-MLOps.md)
