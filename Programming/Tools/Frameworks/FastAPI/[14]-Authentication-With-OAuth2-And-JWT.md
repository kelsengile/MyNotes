[Previous](./[13]-Migrations-And-Async-Databases.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[15]-CORS-And-API-Security.md)

*Security*

# Lesson 14 - Authentication With OAuth2 And JWT

## 14.1 Security utilities

Two words that are often confused:

- **Authentication**: proving **who you are** (logging in).
- **Authorization**: deciding **what you are allowed to do** (permissions).

FastAPI ships helpers in `fastapi.security` that plug into the dependency system and the interactive docs (they add the **Authorize** button to Swagger UI).

| Utility | Purpose |
|---|---|
| `OAuth2PasswordBearer` | Reads a `Bearer` token from the `Authorization` header |
| `OAuth2PasswordRequestForm` | Parses the `username` and `password` form fields on login |
| `HTTPBasic` | HTTP Basic authentication |
| `APIKeyHeader`, `APIKeyQuery`, `APIKeyCookie` | API keys in headers, query strings, or cookies |
| `HTTPBearer` | Generic bearer token reader |

The most common flow for a first-party app (**OAuth2 password flow with Bearer tokens**) works like this:

```
1. Client  → POST /token  (username + password as form data)
2. Server  → { "access_token": "<JWT>", "token_type": "bearer" }
3. Client  → GET /users/me   with header  Authorization: Bearer <JWT>
4. Server  → verifies the token and returns the user
```

Install what we need:

```bash
pip install "pwdlib[argon2]" pyjwt python-multipart
```

The basic wiring:

```python
from typing import Annotated

from fastapi import Depends, FastAPI
from fastapi.security import OAuth2PasswordBearer

app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


@app.get("/items")
def read_items(token: Annotated[str, Depends(oauth2_scheme)]):
    return {"token": token}
```

- `tokenUrl="token"` tells Swagger UI which endpoint to call to log in.
- If the header is missing, FastAPI automatically returns `401 Not authenticated`.
- At this stage nothing is *verified*; the scheme only extracts the token. The next sections add real checks.

---

## 14.2 Password hashing

**Never store passwords in plain text.** Store a **hash**: a one-way transformation. When a user logs in, hash the entered password and compare it to the stored hash.

Use a slow, purpose-built algorithm such as **Argon2** (or bcrypt). Do not use plain `sha256` or `md5` for passwords. The `pwdlib` library provides a safe, easy interface:

```python
from pwdlib import PasswordHash

password_hash = PasswordHash.recommended()   # uses Argon2


def hash_password(password: str) -> str:
    return password_hash.hash(password)


def verify_password(plain_password: str, hashed_password: str) -> bool:
    return password_hash.verify(plain_password, hashed_password)
```

```python
hashed = hash_password("my-secret-password")
print(hashed)                                   # $argon2id$v=19$m=65536,...
print(verify_password("my-secret-password", hashed))   # True
print(verify_password("wrong", hashed))                # False
```

Each hash includes a random **salt**, so hashing the same password twice gives different strings; `verify` knows how to handle that.

A small fake user database for the examples in this lesson:

```python
from pydantic import BaseModel


class User(BaseModel):
    username: str
    email: str | None = None
    disabled: bool = False


class UserInDB(User):
    hashed_password: str


fake_users_db = {
    "ana": {
        "username": "ana",
        "email": "ana@example.com",
        "hashed_password": hash_password("ana-password"),
        "disabled": False,
    }
}


def get_user(username: str) -> UserInDB | None:
    data = fake_users_db.get(username)
    return UserInDB(**data) if data else None


def authenticate_user(username: str, password: str) -> UserInDB | None:
    user = get_user(username)
    if not user or not verify_password(password, user.hashed_password):
        return None
    return user
```

Notes:

- `UserInDB` (with the hash) is for internal use only. Return `User` to clients so the hash never leaves the server.
- Use the **same error** whether the username or the password is wrong, so attackers cannot learn which usernames exist.
- Enforce a reasonable minimum password length at signup.

---

## 14.3 JWT tokens

A **JWT** (JSON Web Token) is a signed string with three parts: `header.payload.signature`. The payload holds **claims** such as who the user is and when the token expires. The server signs it with a secret key; anyone can *read* the payload, but nobody can *change* it without invalidating the signature.

| Claim | Meaning |
|---|---|
| `sub` | Subject: the user identifier |
| `exp` | Expiration time |
| `iat` | Issued-at time |

**Never put secrets or passwords inside a JWT**, because the payload is only encoded, not encrypted.

Generate a strong secret key once and keep it in an environment variable, **not in code**:

```bash
openssl rand -hex 32
```

Creating tokens with **PyJWT**:

```python
from datetime import datetime, timedelta, timezone

import jwt

SECRET_KEY = "replace-with-a-long-random-secret-from-env"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30


def create_access_token(subject: str, expires_delta: timedelta | None = None) -> str:
    expire = datetime.now(timezone.utc) + (
        expires_delta or timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    )
    payload = {"sub": subject, "exp": expire}
    return jwt.encode(payload, SECRET_KEY, algorithm=ALGORITHM)
```

The login endpoint that returns the token:

```python
from typing import Annotated

from fastapi import Depends, FastAPI, HTTPException, status
from fastapi.security import OAuth2PasswordRequestForm
from pydantic import BaseModel

app = FastAPI()


class Token(BaseModel):
    access_token: str
    token_type: str


@app.post("/token")
def login(form_data: Annotated[OAuth2PasswordRequestForm, Depends()]) -> Token:
    user = authenticate_user(form_data.username, form_data.password)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Incorrect username or password",
            headers={"WWW-Authenticate": "Bearer"},
        )
    token = create_access_token(subject=user.username)
    return Token(access_token=token, token_type="bearer")
```

`OAuth2PasswordRequestForm` expects **form data** with `username` and `password` (OAuth2 requires exactly these names, even if you log in with email).

Token lifetime guidance: keep access tokens **short-lived** (minutes), and use a separate **refresh token** flow if users need long sessions. JWTs cannot be revoked before they expire unless you add a deny-list, which is another reason to keep them short.

---

## 14.4 Protecting endpoints

Create a dependency that **decodes the token and loads the user**, then require it on any endpoint that needs authentication.

```python
import jwt
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


async def get_current_user(token: Annotated[str, Depends(oauth2_scheme)]) -> UserInDB:
    credentials_error = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username = payload.get("sub")
        if username is None:
            raise credentials_error
    except jwt.InvalidTokenError:       # covers bad signatures and expired tokens
        raise credentials_error

    user = get_user(username)
    if user is None:
        raise credentials_error
    return user


async def get_current_active_user(
    current_user: Annotated[UserInDB, Depends(get_current_user)],
) -> UserInDB:
    if current_user.disabled:
        raise HTTPException(status_code=400, detail="Inactive user")
    return current_user
```

Use it in endpoints:

```python
@app.get("/users/me", response_model=User)
async def read_me(current_user: Annotated[UserInDB, Depends(get_current_active_user)]):
    return current_user


@app.get("/users/me/items")
async def read_my_items(current_user: Annotated[UserInDB, Depends(get_current_active_user)]):
    return [{"item_id": "Foo", "owner": current_user.username}]
```

Important details:

- Always pass `algorithms=[...]` explicitly when decoding. Never trust the algorithm named inside the token.
- `jwt.decode` automatically checks the signature **and** the `exp` claim.
- Return `401` for missing or invalid credentials and `403` when the user is known but not allowed.
- To protect a whole group of endpoints, put `Depends(get_current_active_user)` in a router's `dependencies=[...]` (Lesson 9).
- Try it in `/docs`: click **Authorize**, enter `ana` / `ana-password`, then call `/users/me`.

Production checklist:

1. Serve everything over **HTTPS**; bearer tokens are credentials.
2. Load `SECRET_KEY` from the environment (Lesson 18).
3. Keep tokens short-lived and rate-limit the login endpoint (Lesson 15).
4. Do not log tokens or passwords.
5. For very large or complex systems consider an identity provider (Auth0, Keycloak, Cognito, etc.) instead of writing your own.

---

[Previous](./[13]-Migrations-And-Async-Databases.md) | [Table of Contents](./[0]-Introduction-to-FastAPI.md) | [Next](./[15]-CORS-And-API-Security.md)
