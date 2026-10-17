[Previous](./[10]-Middleware-And-Background-Tasks.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[12]-SQL-Databases-With-SQLModel-And-SQLAlchemy.md)

*Core Features*

# Lesson 11 - WebSockets

## 11.1 Connecting clients

Normal HTTP is **request → response**: the client asks, the server answers, and the connection is finished. A **WebSocket** is a **persistent, two-way connection**: once opened, either side can send messages at any time.

| HTTP | WebSocket |
|---|---|
| Client always starts the exchange | Both sides can send whenever they want |
| New request for every interaction | One long-lived connection |
| Ideal for CRUD APIs | Ideal for chat, live updates, games, dashboards |

A WebSocket starts as an HTTP request that is "upgraded". The URL scheme is `ws://` (or `wss://` for secure connections).

FastAPI supports WebSockets through Starlette. Install the `websockets` library (included with `fastapi[standard]`) if you use Uvicorn directly:

```bash
pip install websockets
```

A minimal echo server:

```python
from fastapi import FastAPI, WebSocket

app = FastAPI()


@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    while True:
        data = await websocket.receive_text()
        await websocket.send_text(f"You said: {data}")
```

Steps:

1. Declare the route with `@app.websocket(path)` (not `@app.get`).
2. Call `await websocket.accept()` to complete the handshake.
3. Loop, receiving and sending messages.

A tiny HTML page to test it (save it as `index.html`, or return it from a route with `HTMLResponse`):

```html
<input id="msg" /> <button onclick="send()">Send</button>
<ul id="log"></ul>

<script>
  const ws = new WebSocket("ws://localhost:8000/ws");
  ws.onmessage = (event) => {
    const li = document.createElement("li");
    li.textContent = event.data;
    document.getElementById("log").appendChild(li);
  };
  function send() {
    const input = document.getElementById("msg");
    ws.send(input.value);
    input.value = "";
  }
</script>
```

> Open the HTML file served from your API (for example through an `HTMLResponse` route) rather than from `file://` to avoid browser origin restrictions.

WebSocket routes **do not appear** in the Swagger docs, because OpenAPI does not describe them.

---

## 11.2 Sending and receiving messages

`WebSocket` methods:

| Method | Purpose |
|---|---|
| `await websocket.accept()` | Accept the connection |
| `await websocket.receive_text()` | Receive a text message |
| `await websocket.receive_bytes()` | Receive binary data |
| `await websocket.receive_json()` | Receive and parse JSON |
| `await websocket.send_text(str)` | Send text |
| `await websocket.send_bytes(bytes)` | Send binary data |
| `await websocket.send_json(obj)` | Send an object as JSON |
| `await websocket.close(code=1000)` | Close the connection |

JSON messages are the most practical format:

```python
@app.websocket("/ws/json")
async def json_socket(websocket: WebSocket):
    await websocket.accept()
    while True:
        message = await websocket.receive_json()
        reply = {"type": "echo", "payload": message, "length": len(str(message))}
        await websocket.send_json(reply)
```

**Handling disconnects.** When the client leaves, `receive_*` raises `WebSocketDisconnect`. Always catch it:

```python
from fastapi import WebSocketDisconnect


@app.websocket("/ws/safe")
async def safe_socket(websocket: WebSocket):
    await websocket.accept()
    try:
        while True:
            text = await websocket.receive_text()
            await websocket.send_text(text.upper())
    except WebSocketDisconnect:
        print("Client disconnected")
```

**Path and query parameters** work as in normal endpoints:

```python
@app.websocket("/ws/rooms/{room_id}")
async def room_socket(websocket: WebSocket, room_id: str, user: str = "guest"):
    await websocket.accept()
    await websocket.send_text(f"{user} joined room {room_id}")
    await websocket.close()
```

Connect with `ws://localhost:8000/ws/rooms/general?user=ana`.

**Dependencies** also work (`Depends`, `Query`, `Cookie`, `Header`), but body-related things like `Form` do not apply. Raise `WebSocketException` for rule violations:

```python
from fastapi import Depends, Query, WebSocketException, status


async def get_token(token: str | None = Query(default=None)):
    if token != "secret":
        raise WebSocketException(code=status.WS_1008_POLICY_VIOLATION)
    return token


@app.websocket("/ws/private")
async def private_socket(websocket: WebSocket, token: str = Depends(get_token)):
    await websocket.accept()
    await websocket.send_text("Welcome!")
    await websocket.close()
```

---

## 11.3 Managing connections

One connection echoing messages is easy. Real apps such as chat rooms need to **keep track of many connections** and **broadcast** to them. A common pattern is a `ConnectionManager` class.

```python
from fastapi import FastAPI, WebSocket, WebSocketDisconnect

app = FastAPI()


class ConnectionManager:
    def __init__(self):
        self.active: list[WebSocket] = []

    async def connect(self, websocket: WebSocket):
        await websocket.accept()
        self.active.append(websocket)

    def disconnect(self, websocket: WebSocket):
        self.active.remove(websocket)

    async def send_personal(self, message: str, websocket: WebSocket):
        await websocket.send_text(message)

    async def broadcast(self, message: str):
        for connection in list(self.active):
            try:
                await connection.send_text(message)
            except Exception:
                # Connection was already broken; drop it
                if connection in self.active:
                    self.active.remove(connection)


manager = ConnectionManager()


@app.websocket("/ws/chat/{client_id}")
async def chat(websocket: WebSocket, client_id: str):
    await manager.connect(websocket)
    await manager.broadcast(f"Client #{client_id} joined the chat")
    try:
        while True:
            data = await websocket.receive_text()
            await manager.send_personal(f"You wrote: {data}", websocket)
            await manager.broadcast(f"Client #{client_id} says: {data}")
    except WebSocketDisconnect:
        manager.disconnect(websocket)
        await manager.broadcast(f"Client #{client_id} left the chat")
```

Things to understand:

- The manager holds the **list of live connections** in memory.
- `broadcast` loops over a **copy** of the list so removing dead connections while iterating is safe.
- Every connected client is served by its own running copy of the `chat` function.

Limitations of this simple approach:

1. State lives **inside one process**. If you run several workers or servers, clients connected to different workers will not see each other's messages.
2. To scale out, use a message broker such as **Redis Pub/Sub** (or similar) to share broadcasts between workers.
3. Consider heartbeats (periodic ping messages) to detect dead connections, and set limits on message size and number of connections per user.

---

## 11.4 Use cases

WebSockets are the right tool when the server needs to **push** data or when low-latency two-way communication matters.

| Use case | Why WebSockets fit |
|---|---|
| Chat and messaging | Messages arrive instantly for everyone |
| Live dashboards | Server pushes metrics as they change |
| Notifications | Alert users without constant polling |
| Collaborative editing | Many users share changes in real time |
| Multiplayer games | Frequent small two-way messages |
| Live progress updates | Long jobs report percentages as they run |
| Streaming data feeds | Stock prices, sensor data |

When **not** to use them:

- Simple request/response operations: use normal HTTP endpoints.
- One-way server-to-client updates: **Server-Sent Events (SSE)** can be simpler and work well over regular HTTP using `StreamingResponse` with media type `text/event-stream`.
- Infrequent updates: periodic polling may be adequate.

Production checklist:

1. **Authenticate** before or immediately after connecting. Browsers cannot set custom headers on WebSocket connections, so use a token in the query string, a cookie, or send credentials as the first message. Prefer short-lived tokens, because URLs may appear in logs.
2. **Validate every message**, just as you would a request body (for example with a Pydantic model: `Message.model_validate(data)`).
3. **Limit** connections and message sizes to resist abuse.
4. Use `wss://` (TLS) in production.
5. Configure your reverse proxy to allow the `Upgrade` and `Connection` headers, and to keep long-lived connections open.
6. Plan for **reconnection** on the client side; networks drop connections regularly.

Testing a WebSocket with the built-in test client:

```python
from fastapi.testclient import TestClient

client = TestClient(app)


def test_echo():
    with client.websocket_connect("/ws") as websocket:
        websocket.send_text("hello")
        assert websocket.receive_text() == "You said: hello"
```

(This assumes the echo endpoint from lesson 11.1 is defined in `app`.)

---

[Previous](./[10]-Middleware-And-Background-Tasks.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[12]-SQL-Databases-With-SQLModel-And-SQLAlchemy.md)
