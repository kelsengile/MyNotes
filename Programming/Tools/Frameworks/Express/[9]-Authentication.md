[Previous](./[8]-Working-With-Databases.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[10]-Views-Static-Files-And-Uploads.md)

*Building APIs*

# Lesson 9 - Authentication

Authentication answers the question "who are you?". Authorization answers "what are you allowed to do?". This lesson covers storing passwords safely, keeping users logged in with sessions or tokens, and using Passport.js to organize it all.

---

## 9.1 Password hashing

**Never store passwords as plain text.** If your database leaks, every account is exposed. Instead, store a **hash**: a one-way transformation that cannot be reversed. To check a login, hash the submitted password and compare.

A good password hash is deliberately **slow** and includes a random **salt**, so identical passwords produce different hashes and brute-force guessing is expensive. Do not use fast hashes like MD5 or SHA-256 for passwords.

| Library | Algorithm | Notes |
|---|---|---|
| `bcrypt` | bcrypt | Very common and well understood |
| `argon2` | Argon2id | Modern recommendation, memory-hard |

### Using bcrypt

```bash
npm install bcrypt
```

```js
import bcrypt from 'bcrypt';

const SALT_ROUNDS = 12;   // higher = slower and safer

// Registering
const hash = await bcrypt.hash(plainPassword, SALT_ROUNDS);
// store `hash` in the database, never the plain password

// Logging in
const ok = await bcrypt.compare(plainPassword, user.passwordHash);
```

The salt is stored inside the hash string, so you do not manage it yourself.

### Registration and login routes

```js
// services/authService.js
import bcrypt from 'bcrypt';
import * as userRepo from '../repositories/userRepository.js';
import { ConflictError, UnauthorizedError } from '../errors/AppError.js';

export async function register({ email, password, name }) {
  if (await userRepo.findByEmail(email)) throw new ConflictError('Email already registered');

  const passwordHash = await bcrypt.hash(password, 12);
  const user = await userRepo.create({ email, name, passwordHash });

  const { passwordHash: _omit, ...safeUser } = user;   // never return the hash
  return safeUser;
}

export async function verifyCredentials(email, password) {
  const user = await userRepo.findByEmail(email);
  const ok = user && (await bcrypt.compare(password, user.passwordHash));

  // Same message either way, so attackers cannot tell which emails exist
  if (!ok) throw new UnauthorizedError('Invalid email or password');

  const { passwordHash: _omit, ...safeUser } = user;
  return safeUser;
}
```

### Password rules worth following

- Enforce a minimum length (at least 8, ideally 12) with your validation schema from Lesson 7.
- Use the **same generic error** for "wrong email" and "wrong password".
- Rate limit login attempts (see Lesson 11).
- Never log passwords, and never include them in error messages.

---

## 9.2 Sessions and cookies

A **session** stores login state on the server. After a successful login the server creates a session record and sends the browser a cookie holding only a random **session id**. On every later request the browser sends that cookie, and the server looks up the session.

```
Browser                          Server
  | -- POST /login ------------->  verify password
  | <-- Set-Cookie: sid=abc123 --  create session { userId: 5 }
  | -- GET /profile (sid=abc123)-> look up session abc123 → user 5
```

### Using express-session

```bash
npm install express-session
```

```js
import session from 'express-session';

app.use(
  session({
    secret: process.env.SESSION_SECRET,   // long random string
    resave: false,                        // do not save unchanged sessions
    saveUninitialized: false,             // do not create sessions for anonymous visitors
    cookie: {
      httpOnly: true,                     // JavaScript cannot read the cookie
      secure: process.env.NODE_ENV === 'production',   // HTTPS only in production
      sameSite: 'lax',
      maxAge: 1000 * 60 * 60 * 24,        // 1 day
    },
  })
);
```

Login, a protected route, and logout:

```js
app.post('/login', async (req, res) => {
  const user = await verifyCredentials(req.body.email, req.body.password);

  req.session.regenerate((err) => {        // new session id prevents session fixation
    if (err) throw err;
    req.session.userId = user.id;
    res.json({ message: 'Logged in' });
  });
});

function requireLogin(req, res, next) {
  if (!req.session.userId) return res.status(401).json({ error: 'Please log in' });
  next();
}

app.get('/profile', requireLogin, (req, res) => {
  res.json({ userId: req.session.userId });
});

app.post('/logout', (req, res) => {
  req.session.destroy(() => {
    res.clearCookie('connect.sid');
    res.json({ message: 'Logged out' });
  });
});
```

> **Warning:** express-session keeps sessions in memory by default. That store leaks memory and loses everything on restart, so it is for development only. In production use a real store such as Redis (`connect-redis`) or your database.

> **Behind a proxy:** If your app runs behind a reverse proxy that handles HTTPS (see Lesson 15), set `app.set('trust proxy', 1)` or `secure` cookies will not be sent.

### CSRF

Because browsers attach cookies automatically, cookie-based sessions can be abused by other sites (**Cross-Site Request Forgery**). `sameSite: 'lax'` blocks most cases. For forms that change data, also use a CSRF token strategy appropriate to your app.

### Sessions vs tokens

| | Sessions | JWT tokens |
|---|---|---|
| State stored | On the server | In the token itself |
| Revoking access | Easy (delete the session) | Harder (token valid until it expires) |
| Scaling | Needs a shared store | No lookup needed |
| Best for | Traditional websites | APIs, mobile apps, multiple services |

---

## 9.3 JWT authentication

A **JSON Web Token (JWT)** is a signed string that carries claims, for example `{ "sub": "5", "role": "admin" }`. The server signs it with a secret. Later it verifies the signature, so it can trust the contents **without** a database lookup.

A JWT has three dot-separated parts: `header.payload.signature`.

> **Important:** The payload is encoded, **not encrypted**. Anyone can read it. Never put passwords or private data inside a JWT.

```bash
npm install jsonwebtoken
```

### Issuing a token at login

```js
import jwt from 'jsonwebtoken';

function signAccessToken(user) {
  return jwt.sign(
    { sub: String(user.id), role: user.role },
    process.env.JWT_SECRET,
    { expiresIn: '15m' }       // short-lived
  );
}

app.post('/auth/login', async (req, res) => {
  const user = await verifyCredentials(req.body.email, req.body.password);
  res.json({ accessToken: signAccessToken(user) });
});
```

### Verifying the token in middleware

The client sends the token in the `Authorization` header: `Authorization: Bearer <token>`.

```js
// middleware/authenticate.js
import jwt from 'jsonwebtoken';
import { UnauthorizedError } from '../errors/AppError.js';

export function authenticate(req, res, next) {
  const header = req.get('Authorization') ?? '';
  const [scheme, token] = header.split(' ');

  if (scheme !== 'Bearer' || !token) throw new UnauthorizedError('Missing token');

  try {
    const payload = jwt.verify(token, process.env.JWT_SECRET);
    req.user = { id: Number(payload.sub), role: payload.role };
    next();
  } catch {
    throw new UnauthorizedError('Invalid or expired token');
  }
}
```

```js
app.get('/me', authenticate, (req, res) => res.json(req.user));
app.delete('/users/:id', authenticate, requireRole('admin'), deleteUser);   // requireRole from Lesson 4
```

### Access and refresh tokens

Short-lived tokens limit the damage if one is stolen, but forcing users to log in every 15 minutes is annoying. The common solution is two tokens:

| Token | Lifetime | Purpose | Where to keep it |
|---|---|---|---|
| **Access token** | Minutes | Sent with every API request | Memory in the client |
| **Refresh token** | Days | Used only to get a new access token | `httpOnly` cookie |

```js
app.post('/auth/refresh', (req, res) => {
  const token = req.cookies.refreshToken;
  if (!token) throw new UnauthorizedError('No refresh token');

  try {
    const payload = jwt.verify(token, process.env.JWT_REFRESH_SECRET);
    const accessToken = jwt.sign({ sub: payload.sub }, process.env.JWT_SECRET, { expiresIn: '15m' });
    res.json({ accessToken });
  } catch {
    throw new UnauthorizedError('Invalid refresh token');
  }
});
```

For real production use, store refresh tokens (or their hashes) in the database so you can revoke them on logout or when theft is suspected.

### JWT safety checklist

- Use a long, random secret kept in an environment variable.
- Always set `expiresIn`.
- Pin the algorithm when verifying if you can: `jwt.verify(token, secret, { algorithms: ['HS256'] })`.
- Send tokens only over HTTPS.
- Do not store tokens in `localStorage` if you can avoid it, since page scripts can read it. An `httpOnly` cookie is safer against XSS.

---

## 9.4 Passport.js

**Passport** is middleware that standardizes authentication. Its idea is the **strategy**: a plug-in for one way of logging in (username and password, JWT, Google, GitHub, and hundreds more). You configure strategies once and use the same Passport calls for all of them.

```bash
npm install passport passport-local
```

### The local strategy (email and password)

```js
// config/passport.js
import passport from 'passport';
import { Strategy as LocalStrategy } from 'passport-local';
import { verifyCredentials } from '../services/authService.js';
import * as userRepo from '../repositories/userRepository.js';

passport.use(
  new LocalStrategy({ usernameField: 'email' }, async (email, password, done) => {
    try {
      const user = await verifyCredentials(email, password);
      done(null, user);                       // success
    } catch {
      done(null, false, { message: 'Invalid email or password' });   // failure
    }
  })
);

// Sessions: what to store in the session, and how to turn it back into a user
passport.serializeUser((user, done) => done(null, user.id));

passport.deserializeUser(async (id, done) => {
  try {
    done(null, await userRepo.findById(id));
  } catch (err) {
    done(err);
  }
});

export default passport;
```

### Wiring it into the app

Passport needs a session in front of it when you use session-based login:

```js
import session from 'express-session';
import passport from './config/passport.js';

app.use(session({ /* options from 9.2 */ }));
app.use(passport.initialize());
app.use(passport.session());

app.post('/login', passport.authenticate('local'), (req, res) => {
  res.json({ message: 'Logged in', user: req.user });   // runs only if login succeeded
});

app.get('/dashboard', (req, res) => {
  if (!req.isAuthenticated()) return res.status(401).json({ error: 'Please log in' });
  res.json({ user: req.user });
});

app.post('/logout', (req, res, next) => {
  req.logout((err) => {                      // logout needs a callback in current Passport versions
    if (err) return next(err);
    res.json({ message: 'Logged out' });
  });
});
```

A failed `passport.authenticate('local')` responds with `401` automatically.

### JWT with Passport

For APIs without sessions, use `passport-jwt`:

```bash
npm install passport-jwt
```

```js
import { Strategy as JwtStrategy, ExtractJwt } from 'passport-jwt';

passport.use(
  new JwtStrategy(
    {
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      secretOrKey: process.env.JWT_SECRET,
    },
    async (payload, done) => {
      const user = await userRepo.findById(Number(payload.sub));
      done(null, user ?? false);
    }
  )
);

// No session needed
app.get('/me', passport.authenticate('jwt', { session: false }), (req, res) => {
  res.json(req.user);
});
```

### Social login (OAuth)

Strategies such as `passport-google-oauth20` or `passport-github2` let users log in with an existing account. The flow is always the same:

1. The user clicks "Log in with Google" and your route redirects them to Google.
2. The user approves, and Google redirects back to your **callback URL** with a code.
3. The strategy exchanges the code for the user's profile and calls your `verify` function, where you find or create a local user.

### Authorization after authentication

Once you know who the user is, check what they may do:

| Approach | Example |
|---|---|
| **Role-based** | `requireRole('admin')` middleware |
| **Ownership check** | A user may edit a post only if `post.authorId === req.user.id` |

Always check ownership in the service or controller. Hiding a button in the front end is not security.

### Which approach should I choose?

| Situation | Suggestion |
|---|---|
| Server-rendered website | Sessions (with or without Passport) |
| API for a mobile app or single-page app | JWT access plus refresh tokens |
| Login with Google or GitHub | Passport with the matching OAuth strategy |

---

[Previous](./[8]-Working-With-Databases.md) | [Table of Contents](./[0]-Introduction-to-Express.md) | [Next](./[10]-Views-Static-Files-And-Uploads.md)
