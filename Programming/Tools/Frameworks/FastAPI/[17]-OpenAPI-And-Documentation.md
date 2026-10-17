[Previous](./[16]-Testing-FastAPI-Applications.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[18]-Deployment.md)

*Quality And Delivery*

# Lesson 17 - OpenAPI And Documentation

## 17.1 Swagger UI and ReDoc

**OpenAPI** is an industry standard (a JSON/YAML format) for describing HTTP APIs: paths, parameters, request bodies, responses, and security. FastAPI builds this description **automatically from your code**, and that description powers everything in this lesson.

| URL | What it is |
|---|---|
| `/openapi.json` | The raw OpenAPI schema |
| `/docs` | **Swagger UI**: interactive, lets you call endpoints and authorize |
| `/redoc` | **ReDoc**: tidy, read-only reference documentation |

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/hello")
def hello(name: str = "world"):
    return {"message": f"Hello {name}"}
```

Start the app and open `/openapi.json` to see the generated schema. The interactive pages simply render it.

Changing or disabling the documentation URLs:

```python
app = FastAPI(
    docs_url="/documentation",     # Swagger UI moved
    redoc_url=None,                # ReDoc disabled
    openapi_url="/api/openapi.json",
)
```

Setting `openapi_url=None` disables the schema and **both** doc pages. This is common for private or internal production services:

```python
import os

app = FastAPI(
    docs_url="/docs" if os.getenv("ENV") != "production" else None,
    redoc_url=None,
    openapi_url="/openapi.json" if os.getenv("ENV") != "production" else None,
)
```

Where does the information come from?

| In your code | Appears in the docs as |
|---|---|
| Function name and docstring | Summary and description |
| Type hints and `Query`/`Path`/`Field` options | Parameters, types, constraints |
| Pydantic models | Request and response schemas |
| `status_code`, `responses` | Documented status codes |
| `Depends(oauth2_scheme)` | The **Authorize** button and lock icons |

---

## 17.2 Customizing metadata

Give the whole API a name, description, version, and contact details when you create the app:

```python
description = """
Shop API helps you manage products and orders.

## Items

You can **read items** and **create items**.

## Users

You will be able to:

* **Create users** (_not implemented_).
* **Read users** (_not implemented_).
"""

tags_metadata = [
    {
        "name": "items",
        "description": "Operations with items. The **login** logic is also here.",
    },
    {
        "name": "users",
        "description": "Manage users.",
        "externalDocs": {
            "description": "User guide",
            "url": "https://example.com/docs/users",
        },
    },
]

app = FastAPI(
    title="Shop API",
    summary="A small API for a demo shop.",
    description=description,
    version="1.2.0",
    terms_of_service="https://example.com/terms/",
    contact={
        "name": "API Support",
        "url": "https://example.com/support",
        "email": "support@example.com",
    },
    license_info={
        "name": "Apache 2.0",
        "url": "https://www.apache.org/licenses/LICENSE-2.0.html",
    },
    openapi_tags=tags_metadata,
)
```

Notes:

- `description` supports **Markdown**.
- `openapi_tags` controls the **order** of tag groups in the docs and lets you describe each one. Tags not listed still work and are placed after.
- Servers can be declared with `servers=[{"url": "https://api.example.com", "description": "Production"}]`, which is useful when the app is behind a proxy with a path prefix (also see `root_path`).

Per-endpoint documentation:

```python
@app.get(
    "/items/{item_id}",
    tags=["items"],
    summary="Get one item",
    response_description="The requested item",
    operation_id="get_item",
)
def get_item(item_id: int):
    """
    Get an item by its ID.

    - **item_id**: the numeric identifier of the item

    Returns the item or a 404 error if it does not exist.
    """
    return {"item_id": item_id}
```

- The **docstring** becomes the description (Markdown supported). Use `\f` (form feed) in a docstring to cut off text that should not appear in the docs.
- Mark old endpoints with `deprecated=True`; they appear crossed out.
- Hide an endpoint from the docs while keeping it working with `include_in_schema=False`.

---

## 17.3 Tags and examples

**Tags** organize endpoints into groups. Using Enums prevents typos:

```python
from enum import Enum


class Tags(Enum):
    items = "items"
    users = "users"


@app.get("/items", tags=[Tags.items])
def list_items():
    return ["Portal gun", "Plumbus"]


@app.get("/users", tags=[Tags.users])
def list_users():
    return ["Rick", "Morty"]
```

With routers, set tags once (Lesson 9): `APIRouter(prefix="/items", tags=["items"])`.

**Documenting extra responses** (errors and alternative outcomes):

```python
from pydantic import BaseModel


class Message(BaseModel):
    message: str


class Item(BaseModel):
    id: str
    value: str


@app.get(
    "/items/{item_id}",
    response_model=Item,
    responses={
        404: {"model": Message, "description": "The item was not found"},
        403: {"model": Message, "description": "Not enough privileges"},
    },
)
def read_item(item_id: str):
    if item_id == "foo":
        return {"id": "foo", "value": "there goes my hero"}
    return {"message": "Item not found"}
```

This only **documents** the responses. Your code still decides what to actually return or raise.

**Examples** make schemas easier to understand. Lesson 4 showed model-level examples; you can also add them to parameters:

```python
from typing import Annotated

from fastapi import Body, Query


@app.get("/search")
def search(
    q: Annotated[str, Query(description="Search text", examples=["keyboard"])],
):
    return {"q": q}
```

Multiple named examples for a request body:

```python
@app.post("/items")
def create_item(
    item: Annotated[
        Item,
        Body(
            openapi_examples={
                "basic": {"summary": "Basic item", "value": {"id": "k1", "value": "Keyboard"}},
                "long": {"summary": "Longer value", "value": {"id": "k2", "value": "Mechanical keyboard, 75%"}},
            }
        ),
    ],
):
    return item
```

Writing good docs:

1. Describe **why** and **when**, not only what the parameter is called.
2. Use realistic example values.
3. Document error responses clients should expect.
4. Keep summaries short; put details in the description.

---

## 17.4 Generating client code

Because the schema is machine-readable, tools can **generate API client code** and keep it in sync with your backend, eliminating hand-written fetch calls and mismatched types.

Popular generators:

| Tool | Output |
|---|---|
| **OpenAPI Generator** (`openapi-generator-cli`) | Clients in dozens of languages (Java, Python, Go, C#, ...) |
| **openapi-typescript** | TypeScript types from the schema |
| **@hey-api/openapi-ts** | A typed TypeScript client (fetch/axios based), widely used with FastAPI |
| **Swagger Codegen** | Clients and server stubs |

**Step 1: export the schema** (useful in CI too):

```python
import json

from main import app

with open("openapi.json", "w") as f:
    json.dump(app.openapi(), f, indent=2)
```

or simply download `http://localhost:8000/openapi.json`.

**Step 2: generate a TypeScript client** (example):

```bash
npm install --save-dev @hey-api/openapi-ts
npx @hey-api/openapi-ts -i http://localhost:8000/openapi.json -o src/client
```

Afterwards the front end can call your API with full type safety, for example `ItemsService.getItem({ itemId: 1 })`, and the compiler will flag mismatches whenever the backend changes.

**Nicer method names.** By default FastAPI builds operation IDs from the function name, path, and method (something like `get_item_items__item_id__get`), which makes ugly generated method names. Generate cleaner ones from the tag and function name:

```python
from fastapi import FastAPI
from fastapi.routing import APIRoute


def custom_generate_unique_id(route: APIRoute) -> str:
    return f"{route.tags[0]}-{route.name}"


app = FastAPI(generate_unique_id_function=custom_generate_unique_id)
```

Now `@app.get("/items", tags=["items"])` on a function named `list_items` produces `items-list_items`. Every route needs at least one tag for this function to work, and the combination must stay unique.

Workflow suggestion:

1. Backend changes an endpoint.
2. CI exports `openapi.json` and regenerates the client.
3. Front end type-checks; any breaking change shows up as a compile error instead of a production bug.

Also remember that OpenAPI can feed **contract testing**, mock servers, and API gateways.

---

[Previous](./[16]-Testing-FastAPI-Applications.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[18]-Deployment.md)
