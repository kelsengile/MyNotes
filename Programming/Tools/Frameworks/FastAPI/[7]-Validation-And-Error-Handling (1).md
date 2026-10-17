[Previous](./[6]-Dependency-Injection.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[8]-Forms-Files-And-Headers.md)

*Core Features*

# Lesson 7 - Validation And Error Handling

## 7.1 Automatic validation errors

When incoming data does not match your declarations, FastAPI automatically returns a **`422 Unprocessable Entity`** response. You write no error-handling code for this.

```python
from fastapi import FastAPI
from pydantic import BaseModel, Field

app = FastAPI()


class Item(BaseModel):
    name: str
    price: float = Field(gt=0)


@app.post("/items")
def create_item(item: Item):
    return item
```

Sending `{"name": "Pen", "price": -5}` returns:

```json
{
  "detail": [
    {
      "type": "greater_than",
      "loc": ["body", "price"],
      "msg": "Input should be greater than 0",
      "input": -5,
      "ctx": { "gt": 0 }
    }
  ]
}
```

Reading the error object:

| Key | Meaning |
|---|---|
| `type` | A machine-friendly error type |
| `loc` | **Where** the error is: `body`, `query`, `path`, `header`, or `cookie`, followed by the field name (and list index if relevant) |
| `msg` | Human-readable message |
| `input` | The value that failed |
| `ctx` | Extra context such as the limit that was violated |

Behind the scenes FastAPI raises a `RequestValidationError` (a subclass of Pydantic's `ValidationError`) before your function is called. That means **your code only runs with valid data**.

Validation also covers the other direction: if you declare a `response_model` and your function returns invalid data, FastAPI raises a `500` error because that is a bug in your server, not the client's fault.

---

## 7.2 HTTPException

For errors that **you** decide to raise (not found, forbidden, conflict), use `HTTPException`.

```python
from fastapi import FastAPI, HTTPException, status

app = FastAPI()

items = {"foo": "The Foo Item"}


@app.get("/items/{item_id}")
def read_item(item_id: str):
    if item_id not in items:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="Item not found",
        )
    return {"item": items[item_id]}
```

The client receives status `404` and this body:

```json
{ "detail": "Item not found" }
```

Key facts:

- You **raise** it (not `return` it). Raising stops the function immediately.
- `detail` can be any JSON-compatible value, such as a `dict` or `list`, not only a string.
- It works anywhere in your request handling code, including dependencies and helper functions.

Adding custom headers, which is required for some authentication schemes:

```python
raise HTTPException(
    status_code=401,
    detail="Not authenticated",
    headers={"WWW-Authenticate": "Bearer"},
)
```

Common error codes and when to use them:

| Code | Use when |
|---|---|
| `400` | The request is malformed in a way validation can't express |
| `401` | The client is not authenticated |
| `403` | Authenticated, but not allowed |
| `404` | The resource does not exist |
| `409` | Conflict, such as creating a duplicate |
| `422` | Validation failure (automatic) |

---

## 7.3 Custom exception handlers

Sometimes you want your own exception types and a consistent response format. Define the exception, then register a handler with `@app.exception_handler(...)`.

```python
from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse

app = FastAPI()


class OutOfStockError(Exception):
    def __init__(self, product: str):
        self.product = product


@app.exception_handler(OutOfStockError)
async def out_of_stock_handler(request: Request, exc: OutOfStockError):
    return JSONResponse(
        status_code=409,
        content={"message": f"'{exc.product}' is out of stock."},
    )


@app.get("/buy/{product}")
def buy(product: str):
    if product == "laptop":
        raise OutOfStockError(product)
    return {"bought": product}
```

Now `GET /buy/laptop` returns `409` with your custom message.

Why this is useful:

1. Business logic can raise **domain exceptions** without knowing anything about HTTP.
2. One handler controls how that error is presented everywhere.
3. You keep path operation code short and focused.

A handler is an `async def` function that receives the `Request` and the exception, and returns a `Response`.

Handling unexpected errors: to catch all unhandled exceptions, register a handler for `Exception`. Starlette treats this specially (it runs in the outer error middleware), so use it mainly for logging and a clean generic message:

```python
import logging

logger = logging.getLogger("app")


@app.exception_handler(Exception)
async def unhandled_handler(request: Request, exc: Exception):
    logger.exception("Unhandled error on %s", request.url.path)
    return JSONResponse(status_code=500, content={"message": "Internal server error"})
```

Never leak stack traces or internal details to clients in production.

---

## 7.4 Customizing error responses

You can also **override FastAPI's default handlers** to change their format.

**Custom validation error format:**

```python
from fastapi import FastAPI, Request, status
from fastapi.encoders import jsonable_encoder
from fastapi.exceptions import RequestValidationError
from fastapi.responses import JSONResponse

app = FastAPI()


@app.exception_handler(RequestValidationError)
async def validation_handler(request: Request, exc: RequestValidationError):
    errors = [
        {
            "field": ".".join(str(part) for part in err["loc"]),
            "message": err["msg"],
        }
        for err in exc.errors()
    ]
    return JSONResponse(
        status_code=status.HTTP_422_UNPROCESSABLE_ENTITY,
        content=jsonable_encoder({"success": False, "errors": errors}),
    )
```

A failed request now looks like:

```json
{
  "success": false,
  "errors": [{ "field": "body.price", "message": "Input should be greater than 0" }]
}
```

**Custom format for all `HTTPException`s.** Register the handler for Starlette's `HTTPException` so it also catches errors raised by Starlette itself (for example unknown routes):

```python
from starlette.exceptions import HTTPException as StarletteHTTPException


@app.exception_handler(StarletteHTTPException)
async def http_exception_handler(request: Request, exc: StarletteHTTPException):
    return JSONResponse(
        status_code=exc.status_code,
        content={"success": False, "message": exc.detail},
        headers=getattr(exc, "headers", None),
    )
```

Tips for good API errors:

- Use **one consistent shape** for all errors so clients can handle them in one place.
- Include enough information to fix the problem (field names, reasons), but never secrets.
- Keep `headers` such as `WWW-Authenticate` when you wrap authentication errors.
- Document non-obvious errors with the `responses=` parameter so they show in `/docs`.

---

[Previous](./[6]-Dependency-Injection.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[8]-Forms-Files-And-Headers.md)
