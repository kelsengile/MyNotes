[Previous](./[7]-Validation-And-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[9]-Routers-And-Project-Structure.md)

*Core Features*

# Lesson 8 - Forms, Files, And Headers

## 8.1 Form data

Not every client sends JSON. HTML forms send **form data** (`application/x-www-form-urlencoded`, or `multipart/form-data` when files are included). To read form fields you first need the `python-multipart` package. It is already included if you installed `fastapi[standard]`; otherwise run:

```bash
pip install python-multipart
```

Declare fields with `Form()`:

```python
from typing import Annotated

from fastapi import FastAPI, Form

app = FastAPI()


@app.post("/login")
def login(
    username: Annotated[str, Form()],
    password: Annotated[str, Form()],
):
    return {"username": username}
```

Important rules:

- A single request can use **either JSON or form encoding for the body, not both**. If you use `Form()` fields you cannot also declare a JSON body model in the same endpoint.
- Form fields support the same validation options as other parameters: `min_length`, `pattern`, and so on.

```python
username: Annotated[str, Form(min_length=3, max_length=20)]
```

**Form models.** You can group form fields into a Pydantic model:

```python
from pydantic import BaseModel


class LoginForm(BaseModel):
    username: str
    password: str


@app.post("/login-model")
def login_model(data: Annotated[LoginForm, Form()]):
    return {"username": data.username}
```

Use `model_config = {"extra": "forbid"}` on the model if you want to reject unexpected form fields.

---

## 8.2 File uploads

File uploads are sent as `multipart/form-data`, so `python-multipart` is required here too.

FastAPI offers two ways to receive files:

| Type | Behavior |
|---|---|
| `bytes` with `File()` | The whole file is read into memory. Fine for small files only |
| `UploadFile` | Spooled file object: stays in memory up to a size limit, then moves to disk. **Preferred** |

```python
from typing import Annotated

from fastapi import FastAPI, File, UploadFile

app = FastAPI()


@app.post("/upload-small")
def upload_small(file: Annotated[bytes, File()]):
    return {"size": len(file)}


@app.post("/upload")
async def upload(file: UploadFile):
    contents = await file.read()
    return {
        "filename": file.filename,
        "content_type": file.content_type,
        "size": len(contents),
    }
```

`UploadFile` attributes and methods:

| Member | Purpose |
|---|---|
| `filename` | Original file name sent by the client (never trust it blindly) |
| `content_type` | MIME type, such as `image/png` (also provided by the client) |
| `file` | The underlying file-like object |
| `read(size)` | Read bytes (async) |
| `write(data)` | Write bytes (async) |
| `seek(offset)` | Move the cursor (async) |
| `close()` | Close the file (async) |

**Multiple files:**

```python
@app.post("/upload-many")
async def upload_many(files: list[UploadFile]):
    return {"filenames": [f.filename for f in files]}
```

**Saving a file safely** in chunks, so large files do not fill memory:

```python
import shutil
import uuid
from pathlib import Path

UPLOAD_DIR = Path("uploads")
UPLOAD_DIR.mkdir(exist_ok=True)


@app.post("/save")
def save(file: UploadFile):
    extension = Path(file.filename or "").suffix
    safe_name = f"{uuid.uuid4().hex}{extension}"   # never use the client's name as a path
    destination = UPLOAD_DIR / safe_name

    with destination.open("wb") as buffer:
        shutil.copyfileobj(file.file, buffer)

    return {"saved_as": safe_name}
```

Security notes for uploads:

1. Generate your own file names; client names can contain `../` tricks.
2. Check size limits (also at your reverse proxy) and allowed types.
3. Do not rely on `content_type` alone, because it can be faked.
4. Store files outside any directory that is served as code.

Files and form fields can be sent together in one request:

```python
@app.post("/profile")
async def profile(
    avatar: UploadFile,
    display_name: Annotated[str, Form()],
):
    return {"name": display_name, "avatar": avatar.filename}
```

---

## 8.3 Headers and cookies

Read request headers with `Header()` and cookies with `Cookie()`.

```python
from typing import Annotated

from fastapi import Cookie, FastAPI, Header

app = FastAPI()


@app.get("/info")
def info(
    user_agent: Annotated[str | None, Header()] = None,
    session_id: Annotated[str | None, Cookie()] = None,
):
    return {"user_agent": user_agent, "session_id": session_id}
```

**Name conversion:** HTTP headers use hyphens (`User-Agent`, `X-Token`), but Python names cannot. `Header()` automatically converts underscores to hyphens and ignores case, so `user_agent` reads the `User-Agent` header.

If you ever need to disable that conversion:

```python
strange: Annotated[str | None, Header(convert_underscores=False)] = None
```

**Repeated headers** arrive as a list:

```python
@app.get("/tokens")
def tokens(x_token: Annotated[list[str] | None, Header()] = None):
    return {"X-Token values": x_token}
```

**Header and cookie models** group related values with validation:

```python
from pydantic import BaseModel


class CommonHeaders(BaseModel):
    host: str
    save_data: bool
    x_tracker: list[str] = []


@app.get("/with-model")
def with_model(headers: Annotated[CommonHeaders, Header()]):
    return headers
```

Remember that headers and cookies are **client input**. Treat them like any other untrusted data and validate them.

Setting headers and cookies on the response is covered in Lesson 5.4.

---

## 8.4 Streaming responses

Use a `StreamingResponse` when the response is large or produced gradually: big files, generated CSV exports, or text produced step by step. The client starts receiving data **before** the whole response is ready.

It accepts any **generator** (sync or async):

```python
import time

from fastapi import FastAPI
from fastapi.responses import StreamingResponse

app = FastAPI()


def number_stream():
    for i in range(1, 6):
        yield f"line {i}\n"
        time.sleep(0.5)


@app.get("/stream")
def stream():
    return StreamingResponse(number_stream(), media_type="text/plain")
```

**Streaming a large file from disk without loading it fully:**

```python
def iter_file(path: str, chunk_size: int = 1024 * 1024):
    with open(path, "rb") as f:
        while chunk := f.read(chunk_size):
            yield chunk


@app.get("/download")
def download():
    return StreamingResponse(
        iter_file("big-video.mp4"),
        media_type="video/mp4",
        headers={"Content-Disposition": 'attachment; filename="video.mp4"'},
    )
```

**Generating a CSV on the fly:**

```python
import csv
import io


def csv_rows():
    buffer = io.StringIO()
    writer = csv.writer(buffer)
    writer.writerow(["id", "name"])
    yield buffer.getvalue()
    buffer.seek(0)
    buffer.truncate(0)

    for i in range(1, 1001):
        writer.writerow([i, f"user-{i}"])
        yield buffer.getvalue()
        buffer.seek(0)
        buffer.truncate(0)


@app.get("/export.csv")
def export_csv():
    return StreamingResponse(
        csv_rows(),
        media_type="text/csv",
        headers={"Content-Disposition": 'attachment; filename="export.csv"'},
    )
```

Choosing between response types for files:

| Need | Use |
|---|---|
| Send an existing file with correct headers automatically | `FileResponse` |
| Produce data gradually, or file is not on disk | `StreamingResponse` |

Tips:

- An `async def` generator is best for async data sources; a plain generator in a `def` endpoint runs in a thread pool, so blocking calls inside it are safe.
- Streaming bypasses `response_model` validation, since FastAPI cannot validate data it has not seen yet.
- Proxies sometimes buffer responses. If you see streaming not working in production, check your reverse proxy buffering settings.

---

[Previous](./[7]-Validation-And-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[9]-Routers-And-Project-Structure.md)
