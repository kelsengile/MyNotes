[Previous](./[14]-Working-with-Files-Data.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[16]-Algorithms-Problem-Solving.md)

# Lesson 15 - Networking & Web Basics

## 15.1 How the Internet Works (IP, DNS, TCP/UDP)

The internet is a global network of networks. Getting a message from your device to a server (and back) relies on several layered protocols, each responsible for a different part of the journey.

### IP (Internet Protocol)

IP is responsible for **addressing and routing** — getting packets of data from a source to a destination across interconnected networks.

- **IP address** — a unique numerical identifier for a device on a network.
  - **IPv4**: 32-bit, written as four decimal numbers (e.g., `192.168.1.1`), giving ~4.3 billion possible addresses (largely exhausted).
  - **IPv6**: 128-bit, written in hexadecimal groups (e.g., `2001:0db8:85a3::8a2e:0370:7334`), designed to solve IPv4 exhaustion with a vastly larger address space.
- **Public vs. private addresses** — private ranges (e.g., `192.168.x.x`, `10.x.x.x`) are used inside local networks and aren't routable on the public internet; **NAT (Network Address Translation)** lets many private devices share one public IP.
- **Packets** — data is broken into small units (packets), each with header information (source, destination, sequence info) that routers use to forward it toward its destination, hop by hop.
- **Routers** — devices that forward packets between networks, using routing tables to decide the next hop.

### DNS (Domain Name System)

DNS translates human-readable domain names (like `example.com`) into IP addresses, since computers route traffic using IP addresses, not names. It's often described as "the phonebook of the internet."

**Resolution process (simplified):**
1. Your browser checks its local cache for a previously resolved address.
2. If not cached, it queries a **recursive resolver** (often provided by your ISP or a public service like `8.8.8.8`).
3. The resolver queries a **root nameserver**, which points to the correct **TLD nameserver** (e.g., for `.com`).
4. The TLD nameserver points to the domain's **authoritative nameserver**, which returns the actual IP address.
5. The result is cached (respecting a **TTL**, time-to-live) for future requests, and returned to your browser.

**Common DNS record types:**

| Record | Purpose |
|---|---|
| `A` | Maps a domain to an IPv4 address |
| `AAAA` | Maps a domain to an IPv6 address |
| `CNAME` | Alias — points one domain name to another |
| `MX` | Specifies mail servers for the domain |
| `TXT` | Arbitrary text, often used for verification or SPF/DKIM email security |
| `NS` | Specifies the authoritative nameservers for a domain |

### TCP vs. UDP

Both are **transport-layer protocols** that sit on top of IP, responsible for getting data between specific applications/processes on two devices (identified by **port numbers**).

**TCP (Transmission Control Protocol)**
- **Connection-oriented** — establishes a connection before sending data (the **three-way handshake**: SYN, SYN-ACK, ACK).
- **Reliable** — guarantees packets arrive, in order, retransmitting lost packets.
- **Flow control & congestion control** — adjusts sending rate based on network conditions and receiver capacity.
- **Trade-off**: reliability adds overhead and latency.
- **Used by**: HTTP/HTTPS, email (SMTP), file transfer (FTP), SSH — anything where data integrity and order matter.

**UDP (User Datagram Protocol)**
- **Connectionless** — no handshake; packets ("datagrams") are just sent.
- **Unreliable** — no guarantee of delivery, order, or duplicate protection; the application must handle any of that if needed.
- **Lower latency** — minimal overhead makes it faster for time-sensitive data.
- **Used by**: video/audio streaming, online gaming, DNS lookups, VoIP — situations where speed matters more than perfect delivery (a dropped video frame is less costly than the delay of waiting for a retransmission).

| | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented (handshake) | Connectionless |
| Reliability | Guaranteed delivery & order | Best-effort, no guarantees |
| Speed | Slower (more overhead) | Faster (minimal overhead) |
| Use cases | Web browsing, email, file transfer | Streaming, gaming, DNS, VoIP |

### Ports

A **port** is a number (0–65535) that identifies a specific process/service on a device, allowing multiple network applications to run simultaneously on the same IP address.

| Port | Service |
|---|---|
| 20/21 | FTP |
| 22 | SSH |
| 25 | SMTP (email) |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 3306 | MySQL |
| 5432 | PostgreSQL |

---

## 15.2 HTTP/HTTPS Fundamentals

HTTP (HyperText Transfer Protocol) is the application-layer protocol that powers the web — the format client and server use to request and exchange resources.

### Request-Response Cycle

1. Client sends an **HTTP request** (method, URL, headers, optional body).
2. Server processes it and sends back an **HTTP response** (status code, headers, optional body).
3. Traditionally, each HTTP/1.1 connection could be reused (`keep-alive`), and HTTP/2+ allows multiplexing many requests over one connection.

### HTTP Methods (Verbs)

| Method | Purpose | Idempotent? | Has Body? |
|---|---|---|---|
| `GET` | Retrieve a resource | Yes | No |
| `POST` | Create a resource / submit data | No | Yes |
| `PUT` | Replace a resource entirely | Yes | Yes |
| `PATCH` | Partially update a resource | No | Yes |
| `DELETE` | Remove a resource | Yes | Usually no |
| `HEAD` | Like GET, but headers only (no body) | Yes | No |
| `OPTIONS` | Discover allowed methods/CORS preflight | Yes | No |

*Idempotent means calling it multiple times has the same effect as calling it once.*

### Status Codes

| Range | Category | Examples |
|---|---|---|
| 1xx | Informational | `100 Continue` |
| 2xx | Success | `200 OK`, `201 Created`, `204 No Content` |
| 3xx | Redirection | `301 Moved Permanently`, `302 Found`, `304 Not Modified` |
| 4xx | Client Error | `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `429 Too Many Requests` |
| 5xx | Server Error | `500 Internal Server Error`, `502 Bad Gateway`, `503 Service Unavailable` |

### Anatomy of a Request/Response

```http
GET /api/users/42 HTTP/1.1
Host: example.com
Accept: application/json
Authorization: Bearer eyJhbGciOi...
User-Agent: Mozilla/5.0
```

```http
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 58
Cache-Control: max-age=3600

{"id": 42, "name": "Alice", "email": "alice@example.com"}
```

### Common Headers

| Header | Purpose |
|---|---|
| `Content-Type` | Format of the request/response body (`application/json`, `text/html`, etc.) |
| `Content-Length` | Size of the body in bytes |
| `Authorization` | Credentials for authentication (`Bearer <token>`, `Basic <base64>`) |
| `Cookie` / `Set-Cookie` | Stores small pieces of state between requests |
| `Cache-Control` | Caching directives (`no-cache`, `max-age=3600`) |
| `User-Agent` | Identifies the client software making the request |
| `Accept` | Formats the client can understand in the response |
| `CORS` headers | `Access-Control-Allow-Origin` and related headers controlling cross-origin requests |

### HTTPS

HTTPS is HTTP layered over **TLS (Transport Layer Security)**, providing:
- **Encryption** — data is unreadable to anyone intercepting it in transit.
- **Integrity** — data can't be tampered with undetected in transit.
- **Authentication** — certificates (issued by trusted Certificate Authorities) verify the server is who it claims to be.

**TLS handshake (simplified):** client and server agree on a TLS version and cipher suite, the server presents its certificate, and both sides derive a shared encryption key used for the rest of the session.

### Statelessness and Cookies/Sessions

HTTP itself is **stateless** — each request is independent, with no memory of prior requests. State (like "is this user logged in?") is layered on top using:
- **Cookies** — small key-value pairs stored by the browser and sent with subsequent requests.
- **Sessions** — server-side storage referenced by a session ID cookie.
- **Tokens** (e.g., JWTs) — self-contained, signed tokens sent in headers, often used for stateless authentication in APIs.

---

## 15.3 Client-Server Model

The client-server model is the dominant architecture for how applications communicate over a network.

### Core Concept

- **Client** — initiates requests (a web browser, mobile app, or another program). Typically handles presentation/user interaction.
- **Server** — listens for and responds to requests, typically holding the shared data and business logic. Can serve many clients simultaneously.

```
   Client                         Server
┌───────────┐   HTTP Request    ┌───────────┐
│  Browser  │ ────────────────► │  Web App  │
│           │ ◄──────────────── │           │
└───────────┘   HTTP Response   └───────────┘
```

### Characteristics

- **Many-to-one relationship** — a single server (or cluster of servers) can serve many clients concurrently.
- **Separation of concerns** — the client handles the UI/UX; the server handles data storage, business rules, and security-critical logic.
- **Servers are typically always-on**, waiting for and responding to incoming requests, while clients connect intermittently.

### Common Variations

- **Single server, many clients** — the classic model (e.g., a website).
- **Load-balanced servers** — multiple server instances behind a **load balancer** that distributes incoming traffic, improving scalability and fault tolerance.
- **Multi-tier architecture** — splitting responsibilities across layers, commonly:
  - **Presentation tier** (client/UI)
  - **Application/logic tier** (business logic, API server)
  - **Data tier** (database)
- **Microservices** — instead of one monolithic server, the backend is split into many small, independently deployable services that communicate over the network (often via HTTP/REST or messaging queues).
- **Peer-to-peer (P2P)** — a contrasting model where nodes act as both clients and servers to each other, without a central authority (e.g., BitTorrent) — mentioned here as a contrast to client-server.

### Client-Side vs. Server-Side

| | Client-Side | Server-Side |
|---|---|---|
| Runs on | User's device (browser, app) | Remote server/cloud |
| Examples | HTML/CSS/JavaScript rendering, form validation for UX | Database access, authentication, business logic, sensitive computation |
| Trust level | Untrusted — users can inspect/modify client code | Trusted — the source of truth for security-critical logic |
| Rendering (web) | Client-Side Rendering (CSR) — JS builds the page in the browser | Server-Side Rendering (SSR) — HTML is generated on the server and sent ready-to-display |

**Important security principle:** never trust client-side validation alone — a malicious user can bypass JavaScript checks entirely (e.g., via browser dev tools or direct API calls), so all critical validation and authorization must also be enforced on the server.

---

## 15.4 REST & API Design Basics

An **API (Application Programming Interface)** defines how software components communicate. A **web API** typically exposes functionality over HTTP so other programs (or frontends) can interact with a server's data and logic.

### What Makes an API "RESTful"

REST (Representational State Transfer) is an architectural style — not a strict protocol — built around a set of guiding constraints:

1. **Client-server separation** — client and server evolve independently, communicating only via defined requests.
2. **Statelessness** — each request contains all the information needed to process it; the server holds no client session state between requests (any needed state is passed by the client, e.g., via a token).
3. **Cacheability** — responses should explicitly indicate whether they can be cached, to improve performance.
4. **Uniform interface** — resources are identified by URLs and manipulated through a standard set of methods (see below).
5. **Layered system** — the client shouldn't need to know whether it's talking directly to the server or through intermediaries (load balancers, proxies, gateways).
6. **Resource-based** — everything is modeled as a **resource** (a noun — `users`, `orders`, `products`), not an action.

### Resource-Oriented URL Design

```
GET    /users              → list all users
GET    /users/42           → get user 42
POST   /users              → create a new user
PUT    /users/42           → replace user 42 entirely
PATCH  /users/42           → partially update user 42
DELETE /users/42           → delete user 42

GET    /users/42/orders    → list orders belonging to user 42
```

**Design conventions:**
- Use **plural nouns** for collections (`/users`, not `/user`).
- Use **nested paths** to express relationships (`/users/42/orders`).
- Avoid verbs in URLs — the HTTP method already expresses the action (`/users/42/delete` is not RESTful; `DELETE /users/42` is).
- Use **query parameters** for filtering, sorting, and pagination:
  ```
  GET /users?role=admin&sort=-created_at&page=2&limit=25
  ```

### Request/Response Design

```http
POST /users HTTP/1.1
Content-Type: application/json

{"name": "Alice", "email": "alice@example.com"}
```

```http
HTTP/1.1 201 Created
Location: /users/42
Content-Type: application/json

{"id": 42, "name": "Alice", "email": "alice@example.com"}
```

**Good practices:**
- Return appropriate status codes (`201 Created` on successful creation, `404 Not Found` for a missing resource, `400 Bad Request` for invalid input, `401`/`403` for auth failures).
- Return a `Location` header pointing to the newly created resource on `201`.
- Use consistent, predictable JSON structures for both success and error responses.
- Version your API (`/v1/users` or via a header) so breaking changes don't disrupt existing clients.

### Error Response Design

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email address is invalid.",
    "field": "email"
  }
}
```

Consistent, machine-readable error bodies make it far easier for API consumers to handle failures programmatically rather than parsing free-text messages.

### Pagination

For large collections, don't return everything at once:

```
GET /users?page=2&limit=25          (offset/page-based)
GET /users?cursor=abc123&limit=25    (cursor-based — better for large, changing datasets)
```

Responses often include metadata:
```json
{
  "data": [ /* ... */ ],
  "pagination": { "page": 2, "limit": 25, "total": 340 }
}
```

### Authentication & Authorization in APIs

- **API keys** — a static secret token identifying the calling application.
- **Bearer tokens / JWTs** — sent in the `Authorization` header (`Authorization: Bearer <token>`), often containing signed, self-verifying claims about the user.
- **OAuth 2.0** — a standard framework for delegated authorization (e.g., "Log in with Google"), issuing access tokens after a user grants permission.
- Always require authentication over HTTPS — sending credentials over plain HTTP exposes them to interception.

### REST vs. Other API Styles (Brief Comparison)

| Style | Characteristics |
|---|---|
| **REST** | Resource-based, uses standard HTTP methods and status codes; simple, widely adopted |
| **GraphQL** | Single endpoint; clients specify exactly which fields they need in a query, reducing over/under-fetching |
| **gRPC** | Binary protocol (Protocol Buffers) over HTTP/2, built for fast, strongly-typed service-to-service communication |
| **SOAP** | XML-based, highly standardized with strict contracts (WSDL); more rigid, common in older enterprise systems |

### API Design Best Practices

- Design around **resources**, not database tables or internal implementation details — the API should reflect how consumers think about the data, not how it happens to be stored.
- Keep responses **consistent** — the same field naming conventions, date formats, and error structures throughout the API.
- **Don't break existing clients** — version the API or use additive-only changes (new optional fields) rather than silently changing response shapes.
- **Rate limit** public APIs to prevent abuse, and clearly communicate limits via headers (`X-RateLimit-Remaining`) and `429` status codes.
- **Document the API** (e.g., via OpenAPI/Swagger) so consumers know what to expect without reading the server's source code.
- Validate all input server-side, regardless of any client-side validation.

[Previous](./[14]-Working-with-Files-Data.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[16]-Algorithms-Problem-Solving.md)
