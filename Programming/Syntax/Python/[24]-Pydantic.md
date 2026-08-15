[Previous](./[23]-Dataclasses.md) | [Table of Contents](./[0]-Introduction-to-Python.md)

*Object-Oriented Programming*

# Lesson 24 - Data Validation with Pydantic

## 24.1 What is Pydantic and Why Use It?

[Pydantic](https://docs.pydantic.dev) is a third-party library for data validation and settings management using Python type annotations. Where a `dataclass` (Lesson 7) simply *stores* typed fields without checking them, Pydantic actively **validates** data at runtime — rejecting or coercing values that don't match the declared types. This makes it extremely popular for validating data coming from outside your program: API requests, config files, user input, and JSON payloads.

Install it with:

```bash
pip install pydantic
```

---

## 24.2 Defining a BaseModel

Pydantic models subclass `BaseModel` and declare fields with type annotations, just like a dataclass:

```python
from pydantic import BaseModel

class User(BaseModel):
    name: str
    age: int
    is_active: bool = True   # default value, like a dataclass

user = User(name="Ada", age=36)
user.name        # "Ada"
user.is_active    # True

# Pydantic COERCES compatible types automatically:
user2 = User(name="Bo", age="22")   # "22" (a string) is converted to int 22
user2.age   # 22, as an actual int
```

---

## 24.3 Validation and Error Handling

If the provided data doesn't match (and can't be coerced to) the declared types, Pydantic raises a `ValidationError` with a detailed, structured explanation of exactly what went wrong:

```python
from pydantic import ValidationError

try:
    User(name="Ada", age="not a number")
except ValidationError as e:
    print(e)
# 1 validation error for User
# age
#   Input should be a valid integer... [type=int_parsing, ...]
```

This is especially useful when validating untrusted input, like a JSON body submitted to a web API — a single `ValidationError` tells you exactly which field(s) failed and why.

---

## 24.4 Field Constraints and Defaults

The `Field()` function adds extra validation rules and metadata beyond the basic type:

```python
from pydantic import BaseModel, Field

class Product(BaseModel):
    name: str = Field(min_length=1, max_length=100)
    price: float = Field(gt=0)              # must be greater than 0
    quantity: int = Field(default=0, ge=0)   # must be >= 0

Product(name="Widget", price=9.99)   # quantity defaults to 0
Product(name="Widget", price=-5)      # ValidationError — price must be > 0
```

---

## 24.5 Nested Models

Models can be nested inside one another, and Pydantic validates the entire structure recursively — this is what makes it so effective for validating complex, nested JSON data:

```python
class Address(BaseModel):
    city: str
    zip_code: str

class User(BaseModel):
    name: str
    address: Address   # a model nested inside another model

user = User(
    name="Ada",
    address={"city": "London", "zip_code": "SW1A 1AA"}   # a dict is validated into an Address
)

user.address.city   # "London" — accessed like a normal attribute
```

If `address` is missing a required field or has the wrong type, Pydantic reports the exact nested path (e.g. `address -> zip_code`) in the `ValidationError`.

[Previous](./[23]-Dataclasses.md) | [Table of Contents](./[0]-Introduction-to-Python.md)
