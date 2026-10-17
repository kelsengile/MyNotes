[Previous](./[3]-Path-And-Query-Parameters.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[5]-Responses-And-Status-Codes.md)

*Basics*

# Lesson 4 - Request Bodies With Pydantic

## 4.1 Defining models

A **request body** is data the client sends to your API, usually as JSON in a `POST`, `PUT`, or `PATCH` request. In FastAPI you describe the body by creating a **Pydantic model**: a class that inherits from `BaseModel`.

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: str | None = None
    price: float
    tax: float | None = None


@app.post("/items")
def create_item(item: Item):
    total = item.price + (item.tax or 0)
    return {"item": item, "total": total}
```

Sending this JSON:

```json
{ "name": "Keyboard", "price": 49.9 }
```

FastAPI will:

1. Read the request body as JSON.
2. Convert and validate each field against the model.
3. Give you a ready-to-use `Item` object with autocompletion.
4. Return a clear `422` error if something is wrong or missing.
5. Add the model's JSON Schema to the interactive docs.

How FastAPI decides where a parameter comes from:

| Parameter | Source |
|---|---|
| Declared in the path (`/items/{item_id}`) | Path |
| A Pydantic model | **Request body** |
| Anything else (simple types like `int`, `str`) | Query |

You can combine all three in one function:

```python
@app.put("/items/{item_id}")
def update_item(item_id: int, item: Item, q: str | None = None):
    result = {"item_id": item_id, **item.model_dump()}
    if q:
        result["q"] = q
    return result
```

Useful model methods (Pydantic v2):

| Method | Purpose |
|---|---|
| `item.model_dump()` | Convert the model to a `dict` |
| `item.model_dump(exclude_unset=True)` | Only fields the client actually sent |
| `item.model_dump_json()` | Convert to a JSON string |
| `Item.model_validate(data)` | Build a model from a dict |

---

## 4.2 Nested models

Models can contain other models, lists of models, and sets, making complex JSON structures easy to describe.

```python
from pydantic import BaseModel, HttpUrl


class Image(BaseModel):
    url: HttpUrl
    name: str


class Product(BaseModel):
    name: str
    price: float
    tags: set[str] = set()
    images: list[Image] = []


@app.post("/products")
def create_product(product: Product):
    return product
```

Matching JSON:

```json
{
  "name": "Lamp",
  "price": 25.5,
  "tags": ["home", "light", "home"],
  "images": [
    { "url": "https://example.com/lamp.png", "name": "Front view" }
  ]
}
```

Points to notice:

- `set[str]` automatically removes duplicate tags.
- `HttpUrl` validates that the value is a proper URL.
- Validation applies at **every level**, and error messages show the exact path to the problem (for example `images -> 0 -> url`).

Other helpful Pydantic types: `EmailStr` (requires `pip install email-validator`), `UUID`, `datetime`, `date`, `Decimal`.

A body can also be a list or a dictionary directly:

```python
@app.post("/images/bulk")
def create_images(images: list[Image]):
    return images


@app.post("/weights")
def create_weights(weights: dict[int, float]):
    return weights
```

**Multiple body parameters.** If you declare more than one model parameter, FastAPI expects a JSON object whose keys are the parameter names:

```python
class User(BaseModel):
    username: str


@app.put("/orders/{order_id}")
def update_order(order_id: int, product: Product, user: User):
    return {"order_id": order_id, "product": product, "user": user}
```

Expected body:

```json
{ "product": { "name": "Lamp", "price": 25.5 }, "user": { "username": "ana" } }
```

To force this "wrapped" format for a single model, use `Body(embed=True)`:

```python
from typing import Annotated

from fastapi import Body


@app.put("/products/{product_id}")
def update_product(product_id: int, product: Annotated[Product, Body(embed=True)]):
    return {"product_id": product_id, "product": product}
```

---

## 4.3 Field validation

Use `Field()` to add rules and metadata to individual model fields. It works just like `Query()` and `Path()`.

```python
from pydantic import BaseModel, Field


class Item(BaseModel):
    name: str = Field(min_length=2, max_length=50)
    description: str | None = Field(default=None, max_length=300)
    price: float = Field(gt=0, description="Price must be greater than zero")
    quantity: int = Field(default=1, ge=1, le=100)
```

For rules that need custom logic, use **validators**. In Pydantic v2 these are `field_validator` and `model_validator`.

```python
from pydantic import BaseModel, field_validator, model_validator


class UserCreate(BaseModel):
    username: str
    password: str
    password_confirm: str

    @field_validator("username")
    @classmethod
    def username_must_be_clean(cls, value: str) -> str:
        if not value.isalnum():
            raise ValueError("username must contain only letters and numbers")
        return value.lower()

    @model_validator(mode="after")
    def passwords_must_match(self):
        if self.password != self.password_confirm:
            raise ValueError("passwords do not match")
        return self
```

- `field_validator` checks (and may transform) a single field.
- `model_validator(mode="after")` runs after all fields are valid and can compare fields to each other.
- Raise `ValueError` to produce a validation error; FastAPI converts it into a `422` response.

Models can also reject unexpected fields:

```python
from pydantic import BaseModel, ConfigDict


class StrictItem(BaseModel):
    model_config = ConfigDict(extra="forbid")

    name: str
    price: float
```

Now any extra property in the JSON causes a validation error instead of being silently ignored.

---

## 4.4 Examples in docs

Good examples make your interactive docs far more useful. There are several ways to add them.

**1. Inside the model with `Field(examples=[...])`:**

```python
class Item(BaseModel):
    name: str = Field(examples=["Keyboard"])
    price: float = Field(examples=[49.9])
```

**2. For the whole model with `model_config`:**

```python
class Item(BaseModel):
    name: str
    description: str | None = None
    price: float
    tax: float | None = None

    model_config = {
        "json_schema_extra": {
            "examples": [
                {
                    "name": "Keyboard",
                    "description": "Mechanical keyboard",
                    "price": 49.9,
                    "tax": 4.5,
                }
            ]
        }
    }
```

**3. For a specific endpoint with `Body(examples=...)`:**

```python
from typing import Annotated

from fastapi import Body


@app.put("/items/{item_id}")
def update_item(
    item_id: int,
    item: Annotated[
        Item,
        Body(
            openapi_examples={
                "normal": {
                    "summary": "A normal example",
                    "value": {"name": "Foo", "price": 35.4},
                },
                "invalid": {
                    "summary": "Invalid data is rejected",
                    "value": {"name": "Baz", "price": "thirty five"},
                },
            }
        ),
    ],
):
    return {"item_id": item_id, "item": item}
```

`openapi_examples` shows a **dropdown of named examples** in Swagger UI, which is ideal when you want to show several scenarios.

The same `examples=[...]` option exists for `Query`, `Path`, `Header`, and `Cookie` parameters.

Tip: realistic examples are not just decoration. They help front-end developers understand the API without reading your code.

---

[Previous](./[3]-Path-And-Query-Parameters.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[5]-Responses-And-Status-Codes.md)
