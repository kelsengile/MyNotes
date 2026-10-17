[Previous](./[14]-Authentication-With-OAuth2-And-JWT.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[16]-Testing-FastAPI-Applications.md)

*Security*

# Lesson 15 - CORS And API Security

## 15.1 CORS configuration

Lesson 10 introduced CORS. Here we focus on configuring it **safely**.

Browsers enforce the **same-origin policy**: JavaScript on `https://app.example.com` may not read responses from `https://api.example.com` unless the API opts in with special headers. An **origin** is the combination of scheme, host, and port.

The flow for a "non-simple" request (for example `PUT`, or a request with an `Authorization` header):

```
1. Browser → OPTIONS /items   (preflight: "may I send this request?")
   Origin: https://app.example.com
   Access-Control-Request-Method: PUT
2. API → 200 with Access-Control-Allow-Origin / -Methods / -Headers
3. Browser → the real PUT request
```

`CORSMiddleware` handles all of this:

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://app.example.com"],
    allow_credentials=True,
    allow_methods=["GET", "POST", "PUT", "DELETE"],
    allow_headers=["Authorization", "Content-Type"],
    expose_headers=["X-Request-ID"],
    max_age=600,
)
```

| Setting | Guidance |
|---|---|
| `allow_origins` | List **exact** origins. Avoid `["*"]` for APIs that use cookies or sensitive data |
| `allow_origin_regex` | Use for patterns like `r"https://.*\.example\.com"`; keep the regex strict |
| `allow_credentials` | `True` allows cookies and auth headers. It is **not valid** with a wildcard origin |
| `allow_methods` / `allow_headers` | List only what you need |
| `expose_headers` | Response headers JavaScript is allowed to read (by default only a few are visible) |
| `max_age` | Cache the preflight result so browsers send fewer `OPTIONS` requests |

Use different values per environment, loaded from configuration:

```python
import os

origins = os.getenv("CORS_ORIGINS", "http://localhost:3000").split(",")
```

Common misunderstandings:

1. **CORS is not authentication.** It only tells *browsers* which sites may read responses. `curl`, scripts, and other servers ignore it entirely.
2. A CORS error in the browser console often means the **request did succeed** but the browser refused to hand the response to the page. Check the response headers.
3. If an error response (such as a 500) lacks CORS headers, the browser reports it as a CORS problem and hides the real status. Make sure unhandled errors are caught inside the app.

---

## 15.2 Scopes and permissions

**Scopes** are named permissions stored in the token, such as `items:read` or `items:write`. They let you issue tokens with limited power and check them per endpoint.

Declare available scopes on the security scheme (they show up in Swagger's Authorize dialog):

```python
from typing import Annotated

from fastapi import Depends, FastAPI, HTTPException, Security, status
from fastapi.security import OAuth2PasswordBearer, SecurityScopes

oauth2_scheme = OAuth2PasswordBearer(
    tokenUrl="token",
    scopes={
        "items:read": "Read items",
        "items:write": "Create and change items",
    },
)
```

When creating the token, put the granted scopes in the payload:

```python
payload = {
    "sub": user.username,
    "scopes": ["items:read"],
    "exp": expire,
}
```

A dependency that checks the required scopes using `SecurityScopes`:

```python
import jwt


async def get_current_user(
    security_scopes: SecurityScopes,
    token: Annotated[str, Depends(oauth2_scheme)],
):
    authenticate_value = (
        f'Bearer scope="{security_scopes.scope_str}"' if security_scopes.scopes else "Bearer"
    )
    credentials_error = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": authenticate_value},
    )

    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
    except jwt.InvalidTokenError:
        raise credentials_error

    username = payload.get("sub")
    if username is None:
        raise credentials_error

    token_scopes = payload.get("scopes", [])
    for scope in security_scopes.scopes:
        if scope not in token_scopes:
            raise HTTPException(
                status_code=status.HTTP_403_FORBIDDEN,
                detail="Not enough permissions",
                headers={"WWW-Authenticate": authenticate_value},
            )

    user = get_user(username)
    if user is None:
        raise credentials_error
    return user
```

(`SECRET_KEY`, `ALGORITHM`, and `get_user` are the same as in Lesson 14.)

Require scopes on endpoints with `Security()` (a variant of `Depends` that accepts scopes):

```python
@app.get("/items")
async def read_items(user: Annotated[object, Security(get_current_user, scopes=["items:read"])]):
    return [{"item_id": "Foo"}]


@app.post("/items")
async def create_item(user: Annotated[object, Security(get_current_user, scopes=["items:write"])]):
    return {"created": True}
```

**Role-based access control (RBAC)** is a simpler alternative for many apps: store a `role` (such as `admin`, `editor`, `viewer`) on the user and write a dependency factory:

```python
def require_role(*allowed: str):
    def checker(user: Annotated[UserInDB, Depends(get_current_active_user)]):
        if user.role not in allowed:
            raise HTTPException(status_code=403, detail="Insufficient role")
        return user
    return checker


@app.delete("/users/{user_id}", dependencies=[Depends(require_role("admin"))])
def delete_user(user_id: int):
    return {"deleted": user_id}
```

Remember: also check **ownership**. Having permission to read orders does not mean a user may read *other people's* orders. Filter by the current user's ID in your queries.

---

## 15.3 HTTPS and headers

**HTTPS** encrypts traffic between client and server. Without it, passwords, cookies, and bearer tokens can be stolen on the network.

In production, TLS is usually handled **outside** your FastAPI process, by a reverse proxy or load balancer (Nginx, Caddy, Traefik, a cloud load balancer). The app then receives plain HTTP from the proxy. Make sure the app knows the original scheme and client address:

```bash
fastapi run app/main.py --proxy-headers
# or
uvicorn app.main:app --proxy-headers --forwarded-allow-ips="10.0.0.5"
```

Only trust forwarded headers from proxies you control.

Helpful built-in middleware:

```python
from fastapi.middleware.httpsredirect import HTTPSRedirectMiddleware
from fastapi.middleware.trustedhost import TrustedHostMiddleware

app.add_middleware(HTTPSRedirectMiddleware)   # redirect http:// to https://
app.add_middleware(
    TrustedHostMiddleware,
    allowed_hosts=["api.example.com", "*.example.com"],
)
```

(Skip `HTTPSRedirectMiddleware` when TLS is terminated at a proxy that already redirects, otherwise you may create redirect loops.)

**Security headers** add extra browser protections. A small middleware sets them on every response:

```python
from fastapi import Request


@app.middleware("http")
async def security_headers(request: Request, call_next):
    response = await call_next(request)
    response.headers["X-Content-Type-Options"] = "nosniff"
    response.headers["X-Frame-Options"] = "DENY"
    response.headers["Referrer-Policy"] = "no-referrer"
    response.headers["Strict-Transport-Security"] = "max-age=31536000; includeSubDomains"
    return response
```

| Header | Protects against |
|---|---|
| `X-Content-Type-Options: nosniff` | Browsers guessing (and wrongly executing) content types |
| `X-Frame-Options: DENY` | Clickjacking by embedding your page in a frame |
| `Referrer-Policy` | Leaking URLs to other sites |
| `Strict-Transport-Security` | Forces browsers to use HTTPS on future visits |
| `Content-Security-Policy` | Limits where scripts and resources may load from (mostly for HTML pages) |

Other good practices:

1. Mark cookies `secure=True`, `httponly=True`, and a suitable `samesite` value.
2. Disable docs on private APIs (`FastAPI(docs_url=None, redoc_url=None)`) or protect them.
3. Never return stack traces, SQL errors, or internal paths to clients.
4. Keep dependencies updated and scan them for known vulnerabilities (for example with `pip-audit`).
5. Validate **everything** that comes from the client, which is what Pydantic is for.
6. Limit request body sizes at the proxy.

---

## 15.4 Rate limiting

**Rate limiting** restricts how many requests a client can make in a period. It protects against brute-force logins, scraping, and accidental overload.

FastAPI has no built-in rate limiter. Common options:

| Approach | Notes |
|---|---|
| **Reverse proxy / API gateway** (Nginx, Traefik, Cloudflare, cloud gateways) | Best first line of defense; works before requests reach Python |
| **A library such as `slowapi`** | Decorator-based limits inside the app |
| **Custom logic with Redis** | Full control, shared across workers and servers |

Example with `slowapi`:

```bash
pip install slowapi
```

```python
from fastapi import FastAPI, Request
from slowapi import Limiter, _rate_limit_exceeded_handler
from slowapi.errors import RateLimitExceeded
from slowapi.util import get_remote_address

limiter = Limiter(key_func=get_remote_address)

app = FastAPI()
app.state.limiter = limiter
app.add_exception_handler(RateLimitExceeded, _rate_limit_exceeded_handler)


@app.get("/public")
@limiter.limit("10/minute")
async def public(request: Request):
    return {"message": "Limited to 10 requests per minute per IP"}


@app.post("/token")
@limiter.limit("5/minute")
async def login(request: Request):
    return {"message": "Strict limit for login attempts"}
```

Rules for this library: the endpoint **must** accept a `request: Request` parameter, and the `@limiter.limit` decorator must be placed **below** the route decorator.

When the limit is exceeded the client gets **`429 Too Many Requests`**.

A tiny hand-written limiter as a dependency, to show the idea (in-memory, single process only):

```python
import time
from collections import defaultdict, deque

from fastapi import Depends, HTTPException, Request

hits: dict[str, deque[float]] = defaultdict(deque)


def rate_limit(max_requests: int = 5, window_seconds: int = 60):
    def dependency(request: Request):
        key = request.client.host if request.client else "unknown"
        now = time.monotonic()
        queue = hits[key]

        while queue and now - queue[0] > window_seconds:
            queue.popleft()

        if len(queue) >= max_requests:
            raise HTTPException(
                status_code=429,
                detail="Too many requests",
                headers={"Retry-After": str(window_seconds)},
            )
        queue.append(now)

    return dependency


@app.get("/limited", dependencies=[Depends(rate_limit(3, 10))])
def limited():
    return {"ok": True}
```

Important points:

- In-memory counters are **per process**. With several workers or servers use **Redis** (or limits at the gateway) so counts are shared.
- If you are behind a proxy, the client IP is the proxy's address unless you configure proxy headers correctly (15.3).
- Limit **by user or API key** when possible, not only by IP, because many users can share one IP.
- Apply the strictest limits to login, password reset, and signup endpoints.
- Return `Retry-After` so well-behaved clients know when to try again.

---

[Previous](./[14]-Authentication-With-OAuth2-And-JWT.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[16]-Testing-FastAPI-Applications.md)
