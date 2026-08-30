[Previous](./[16]-Algorithms-Problem-Solving.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[18]-Testing-Quality.md)

# Lesson 17 - Software Design & Architecture

## 17.1 Design Patterns (Singleton, Factory, Observer, etc.)

Design patterns are reusable, general solutions to commonly recurring problems in software design. They aren't finished code, but templates and vocabulary that help developers communicate and structure solutions consistently. Most classic patterns come from the 1994 "Gang of Four" (GoF) book and are grouped into three categories: **creational**, **structural**, and **behavioral**.

### Creational Patterns

Concerned with *how objects are created*, abstracting away instantiation details.

**Singleton** — ensures a class has only one instance, with a global point of access to it. Useful for shared resources like configuration objects, logging, or connection pools.
```python
class Singleton:
    _instance = None

    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
```
*Caution:* overused Singletons introduce global state, which can make testing harder and hide dependencies — use sparingly and consider dependency injection as an alternative.

**Factory Method** — defines an interface for creating an object, but lets subclasses decide which class to instantiate. Decouples client code from concrete classes.
```python
class Dog:
    def speak(self): return "Woof"

class Cat:
    def speak(self): return "Meow"

def animal_factory(kind):
    return {"dog": Dog, "cat": Cat}[kind]()

pet = animal_factory("dog")
print(pet.speak())  # Woof
```

**Abstract Factory** — provides an interface for creating *families* of related objects without specifying their concrete classes (e.g., a UI toolkit factory that produces matching buttons, checkboxes, and menus for either "Light" or "Dark" theme).

**Builder** — separates the construction of a complex object from its representation, allowing step-by-step construction. Useful when an object has many optional parameters.
```python
class PizzaBuilder:
    def __init__(self):
        self.toppings = []

    def add_topping(self, topping):
        self.toppings.append(topping)
        return self  # enables chaining

    def build(self):
        return f"Pizza with {', '.join(self.toppings)}"

pizza = PizzaBuilder().add_topping("cheese").add_topping("olives").build()
```

**Prototype** — creates new objects by copying ("cloning") an existing instance, rather than instantiating from a class directly — useful when object creation is expensive.

### Structural Patterns

Concerned with *how objects and classes are composed* into larger structures.

**Adapter** — converts the interface of one class into another interface clients expect, letting incompatible interfaces work together (like a physical power plug adapter).
```python
class EuropeanSocket:
    def voltage(self): return 230

class USPlugAdapter:
    def __init__(self, socket):
        self.socket = socket
    def voltage(self):
        return self.socket.voltage() / 2  # adapts to ~110V expectation
```

**Decorator** — attaches additional responsibilities to an object dynamically, without altering its structure — an alternative to subclassing for extending behavior.
```python
def with_logging(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@with_logging
def greet(name):
    return f"Hello, {name}"
```

**Facade** — provides a simplified, unified interface to a complex subsystem, hiding internal complexity from the client (e.g., a single `startServer()` method that internally initializes a database, cache, and web server).

**Composite** — composes objects into tree structures to represent part-whole hierarchies, letting clients treat individual objects and groups of objects uniformly (e.g., files and folders, UI component trees).

**Proxy** — provides a placeholder/stand-in for another object to control access to it (e.g., lazy loading, access control, caching, or remote object proxies).

### Behavioral Patterns

Concerned with *how objects communicate and assign responsibility*.

**Observer** — defines a one-to-many dependency so that when one object (the "subject") changes state, all its dependents ("observers") are notified automatically. Foundational to event systems, GUI frameworks, and pub/sub messaging.
```python
class Subject:
    def __init__(self):
        self._observers = []

    def subscribe(self, observer):
        self._observers.append(observer)

    def notify(self, event):
        for obs in self._observers:
            obs.update(event)

class Logger:
    def update(self, event):
        print(f"Logged: {event}")

subject = Subject()
subject.subscribe(Logger())
subject.notify("user_signed_up")
```

**Strategy** — defines a family of interchangeable algorithms, encapsulating each one and letting them be swapped at runtime (e.g., different sorting or payment-processing strategies passed into a shared context).
```python
class ContextualSorter:
    def __init__(self, strategy):
        self.strategy = strategy
    def sort(self, data):
        return self.strategy(data)

ContextualSorter(sorted).sort([3, 1, 2])
ContextualSorter(lambda d: sorted(d, reverse=True)).sort([3, 1, 2])
```

**Command** — encapsulates a request (and its parameters) as an object, allowing actions to be queued, logged, undone, or parameterized (common in undo/redo systems, task queues, and menu actions).

**Iterator** — provides a way to access elements of a collection sequentially without exposing its underlying representation (built into most languages via `for` loops, generators/iterators).

**State** — allows an object to alter its behavior when its internal state changes, appearing as if it changed class (e.g., a traffic light or order object behaving differently depending on whether it's "Pending," "Shipped," or "Delivered").

**Template Method** — defines the skeleton of an algorithm in a base class, deferring specific steps to subclasses without changing the algorithm's overall structure.

**Chain of Responsibility** — passes a request along a chain of handlers, each deciding whether to process it or pass it to the next handler (common in middleware pipelines, event bubbling, and support-ticket escalation).

### When to Use Patterns

| Pattern | Use When |
|---|---|
| Singleton | You need exactly one shared instance (config, logger) |
| Factory Method | Object creation logic should be decoupled from usage |
| Builder | An object has many optional/complex construction steps |
| Adapter | You need to integrate incompatible interfaces |
| Decorator | You need to add behavior without subclassing every combination |
| Facade | You want to simplify a complex subsystem's interface |
| Observer | Multiple parts of a system need to react to one event |
| Strategy | You need interchangeable algorithms selected at runtime |
| Command | You need undoable, queueable, or loggable actions |

**Word of caution:** patterns are tools for specific recurring problems, not goals in themselves. Forcing a pattern where a simple function or class would do is a common form of over-engineering — sometimes called "pattern fever."

---

## 17.2 Modular Design & Separation of Concerns

### Separation of Concerns (SoC)

The principle of dividing a program into distinct sections, each addressing a separate "concern" (a specific piece of functionality or knowledge), so that changes to one part have minimal impact on others.

**Example — a web app separated by concern:**
```
UI Layer         → renders what the user sees
Business Logic   → application rules, calculations, workflows
Data Access      → talks to the database
```

Mixing these together (e.g., SQL queries directly inside UI rendering code) makes the codebase brittle — a change to the database schema shouldn't require touching UI code.

### Modularity

Breaking a system into independent, interchangeable **modules**, each with a well-defined responsibility and a clear interface to the rest of the system.

**Characteristics of good modules:**
- **High cohesion** — everything inside the module is closely related and works toward a single, well-defined purpose.
- **Low coupling** — modules depend on each other as little as possible, and only through well-defined interfaces, not internal details.
- **Encapsulation** — internal implementation details are hidden; only the intended interface is exposed (public functions/classes vs. private/internal helpers).

```python
# Low cohesion / high coupling (bad): one module doing unrelated things,
# and directly reaching into another module's internals
class UserManager:
    def create_user(self, data): ...
    def send_marketing_email(self, user): ...   # unrelated concern
    def generate_invoice_pdf(self, user): ...    # unrelated concern

# Better: separate modules, each with one responsibility
class UserService:
    def create_user(self, data): ...

class EmailService:
    def send_marketing_email(self, user): ...

class InvoiceService:
    def generate_invoice_pdf(self, user): ...
```

### Key Related Principles

- **DRY (Don't Repeat Yourself)** — avoid duplicating logic; extract shared behavior into a single, reusable place.
- **Single Responsibility Principle (SRP)** — a module/class should have exactly one reason to change (part of the broader **SOLID** principles).
- **Information hiding** — expose the minimum interface necessary; hide internal representations so they can change without breaking callers.
- **Loose coupling via interfaces/abstractions** — depend on abstractions (interfaces, contracts) rather than concrete implementations, so components can be swapped or mocked independently (this is also the basis of **dependency injection**).

### Benefits

- **Easier testing** — small, focused modules with clear inputs/outputs can be unit-tested in isolation.
- **Parallel development** — different teams/developers can work on separate modules simultaneously with minimal conflict.
- **Easier maintenance** — a bug or required change is easier to locate and fix when concerns are cleanly separated.
- **Reusability** — well-isolated modules can be reused in other projects or contexts.
- **Replaceability** — a module can be rewritten or swapped (e.g., switching databases) without rewriting unrelated parts of the system, as long as the interface is preserved.

### A Note on the SOLID Principles

Modular design is closely tied to SOLID, a set of five object-oriented design principles:

| Letter | Principle | Idea |
|---|---|---|
| S | Single Responsibility | A class should have one reason to change |
| O | Open/Closed | Open for extension, closed for modification |
| L | Liskov Substitution | Subtypes must be substitutable for their base types |
| I | Interface Segregation | Prefer many small, specific interfaces over one large one |
| D | Dependency Inversion | Depend on abstractions, not concrete implementations |

---

## 17.3 Basic System Design Concepts

System design is about structuring larger applications — often distributed across multiple machines — to meet requirements around scale, reliability, and performance.

### Scalability

The ability of a system to handle increased load.

- **Vertical scaling (scale up)** — adding more power (CPU, RAM) to an existing machine. Simple, but has a hard ceiling and creates a single point of failure.
- **Horizontal scaling (scale out)** — adding more machines and distributing load across them. More complex but can scale much further; the dominant approach for large-scale systems.

### Load Balancing

A **load balancer** distributes incoming requests across multiple servers, improving both scalability and fault tolerance (if one server goes down, traffic is routed to the others).

```
                ┌────────────┐
Clients  ─────► │Load Balancer│
                └─────┬──────┘
          ┌───────────┼───────────┐
     ┌────▼───┐  ┌─────▼───┐  ┌────▼───┐
     │Server 1│  │Server 2 │  │Server 3│
     └────────┘  └─────────┘  └────────┘
```

Common strategies: round-robin, least-connections, and IP-hash based routing.

### Caching

Storing frequently accessed data in a fast-access layer to avoid recomputing or re-fetching it repeatedly.

- **Where caches live:** browser, CDN (content delivery network), application layer (e.g., Redis/Memcached), or database query cache.
- **Cache invalidation** — deciding when cached data becomes stale and must be refreshed; famously one of the hardest problems in computer science.
- **Common strategies:** TTL (time-to-live expiration), write-through (update cache and database together), write-back (update cache first, database later), and cache-aside (application checks cache first, falls back to database on a miss).

### Databases at Scale

- **Replication** — copying data across multiple database servers for redundancy and to spread read load (a common pattern: one primary/writer, multiple read replicas).
- **Sharding (partitioning)** — splitting a large dataset across multiple database instances (e.g., by user ID range), so no single machine holds all the data.
- **Read vs. write scaling** — read-heavy systems benefit from caching and replicas; write-heavy systems often need sharding or specialized write-optimized stores.

### CAP Theorem

For distributed systems, the CAP theorem states you can only guarantee **two of three** properties simultaneously during a network partition:
- **C**onsistency — every read receives the most recent write.
- **A**vailability — every request receives a (non-error) response.
- **P**artition tolerance — the system continues operating despite network failures between nodes.

Since network partitions are unavoidable in real distributed systems, the practical trade-off is usually between consistency and availability (**CP** vs. **AP** systems).

### Asynchronous Processing & Messaging

- **Message queues** (RabbitMQ, Amazon SQS) — decouple producers and consumers; a producer places a task on a queue and continues without waiting for it to be processed.
- **Pub/Sub systems** (Kafka, Google Pub/Sub) — publishers broadcast events to topics, and any number of subscribers can independently consume them.
- **Background jobs/workers** — offloading slow or non-time-critical work (sending emails, generating reports, resizing images) from the main request-response cycle to be processed asynchronously.

This decoupling improves responsiveness (the client doesn't wait for slow work to finish) and resilience (a queue can buffer spikes in load rather than overwhelming downstream services).

### High-Level Architecture Styles

| Style | Description |
|---|---|
| **Monolith** | Entire application is one deployable unit; simple to develop and deploy initially, harder to scale/maintain as it grows |
| **Microservices** | Application split into small, independently deployable services communicating over the network; more scalable and flexible, but adds operational complexity |
| **Event-driven architecture** | Components communicate by producing/consuming events asynchronously rather than direct calls |
| **Serverless** | Code runs in stateless, ephemeral functions managed by a cloud provider (e.g., AWS Lambda), scaling automatically and billed per invocation |

### A Simple System Design Thought Process

1. Clarify requirements (functional and non-functional — expected scale, latency, consistency needs).
2. Estimate scale (requests/second, data volume, storage growth).
3. Sketch a high-level architecture (clients → load balancer → app servers → cache → database).
4. Identify bottlenecks and single points of failure.
5. Decide where to apply caching, replication, sharding, or async processing.
6. Iterate — refine based on the specific constraints of the problem (read-heavy vs. write-heavy, strict consistency vs. eventual consistency, etc.).

---

## 17.4 APIs, Libraries, and Frameworks

These three terms are often used loosely, but they represent distinct ways of reusing and structuring code.

### APIs (Application Programming Interfaces)

An API is a defined contract that specifies how one piece of software can interact with another — what functions/endpoints are available, what inputs they expect, and what outputs they return — without exposing the internal implementation.

- **Library APIs** — the public functions/classes exposed by a code library.
- **Web/network APIs** — endpoints exposed over HTTP (see Section 15.4 for REST specifics).
- **Operating system APIs** — system calls that let programs interact with hardware/OS resources (file I/O, networking, process management).

The key idea: an API is an **interface/contract**, not an implementation — the same API can have different underlying implementations (e.g., multiple libraries implementing the same standard interface).

### Libraries

A library is a collection of pre-written code (functions, classes, modules) that a program can call **on its own terms** — the calling application is in control of the flow, and the library is used as needed.

```python
import requests  # a library

response = requests.get("https://api.example.com/data")
# The program decides when and how to call the library
```

**Characteristics:**
- You call the library's functions.
- Integrating a library into an existing codebase is usually low-friction and incremental — you can adopt one function at a time.
- Examples: NumPy (numerical computing), Requests (HTTP calls), Lodash (utility functions), OpenSSL (cryptography).

### Frameworks

A framework is a more comprehensive foundation that dictates the overall structure of an application. Rather than you calling it, **it calls your code** at defined points — this is often summarized as **"inversion of control"** or the **Hollywood Principle** ("don't call us, we'll call you").

```python
# Flask (a framework) — you write code that fits into its structure,
# and the framework calls your function when a matching request arrives
from flask import Flask
app = Flask(__name__)

@app.route("/hello")
def hello():
    return "Hello, World!"

app.run()  # the framework's event loop drives execution, not your code directly
```

**Characteristics:**
- The framework calls your code, not the other way around.
- Dictates project structure, conventions, and often a "correct" way of doing things.
- Adopting a framework is a bigger commitment — it shapes the whole application's architecture.
- Examples: Django/Flask/FastAPI (Python web), React/Angular/Vue (frontend), Spring (Java), Ruby on Rails.

### Comparing the Three

| | API | Library | Framework |
|---|---|---|---|
| What it is | A contract/interface | Callable code you use | Structural foundation that uses your code |
| Control flow | N/A (just a contract) | Your code is in control | The framework is in control (inversion of control) |
| Commitment | Varies | Low — adopt incrementally | High — shapes the whole app |
| Example | REST endpoint spec, OS syscalls | NumPy, Requests, Lodash | Django, React, Spring, Rails |

### Choosing Between a Library and a Framework

- Reach for a **library** when you need a specific, self-contained capability (parsing dates, making HTTP calls, manipulating images) without altering your application's overall structure.
- Reach for a **framework** when building a larger application from scratch and you want established conventions, built-in tooling, and less architectural decision-making — at the cost of flexibility and a steeper initial learning curve.
- Be cautious of **framework lock-in** — migrating away from a deeply integrated framework later is significantly more costly than swapping out a library.

### Working with Third-Party Code — Best Practices

- **Read the documentation** before integrating — understand the API's contract, versioning policy, and any breaking-change history.
- **Pin dependency versions** in production to avoid unexpected breakage from upstream updates; use lockfiles (`package-lock.json`, `poetry.lock`, `Pipfile.lock`).
- **Isolate third-party dependencies** behind your own interface/wrapper where feasible, so swapping a library later doesn't ripple through the whole codebase (this echoes the Dependency Inversion principle from SOLID).
- **Evaluate maintenance health** before adopting a dependency — check for active maintenance, security advisories, and community size, especially for anything handling sensitive data.
- **Understand licensing** — libraries and frameworks carry licenses (MIT, Apache 2.0, GPL, etc.) that affect how you're permitted to use, modify, and distribute code built with them.

[Previous](./[16]-Algorithms-Problem-Solving.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[18]-Testing-Quality.md)
