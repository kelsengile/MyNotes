[Previous](./[18]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md)

*Production Readiness*

# Lesson 19 - Next Steps With Spring Boot

You now have the full path from a first endpoint to a deployed, secured, monitored service. This final lesson points to where to grow next and offers project ideas to practice on.

---

## 19.1 Reactive programming with WebFlux

**Spring WebFlux** is a non-blocking, reactive web stack, an alternative to Spring MVC. It is built on **Project Reactor**.

### The idea

In the classic model each request occupies a thread, which waits (blocks) during database or network calls. In the reactive model, a small number of threads handle many requests, and work resumes when data becomes available.

### Core types

| Type | Represents |
|---|---|
| `Mono<T>` | Zero or one value, arriving asynchronously |
| `Flux<T>` | Zero to many values, arriving asynchronously |

Nothing happens until something **subscribes**. WebFlux subscribes for you when it writes the response.

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```

```java
@RestController
@RequestMapping("/api/products")
public class ProductController {

    private final ProductRepository repo;      // a reactive repository

    public ProductController(ProductRepository repo) { this.repo = repo; }

    @GetMapping
    public Flux<Product> all() { return repo.findAll(); }

    @GetMapping("/{id}")
    public Mono<ResponseEntity<Product>> one(@PathVariable String id) {
        return repo.findById(id)
                .map(ResponseEntity::ok)
                .defaultIfEmpty(ResponseEntity.notFound().build());
    }

    @PostMapping
    public Mono<Product> create(@RequestBody Product p) { return repo.save(p); }

    @GetMapping(value = "/stream", produces = MediaType.TEXT_EVENT_STREAM_VALUE)
    public Flux<Product> stream() {                         // server-sent events
        return repo.findAll().delayElements(Duration.ofSeconds(1));
    }
}
```

### Operators

```java
Flux.just("a", "b", "c")
    .map(String::toUpperCase)
    .filter(s -> !s.equals("B"))
    .flatMap(s -> callRemote(s))             // async step returning a Mono/Flux
    .subscribe(System.out::println);
```

### Reactive data access

The whole chain must be non-blocking to benefit. That means reactive drivers: **R2DBC** for relational databases, and reactive MongoDB, Redis, and Cassandra. **JPA/JDBC are blocking**, so do not use them in WebFlux handlers without isolating them.

### When to choose it

| Choose WebFlux when | Stay with MVC when |
|---|---|
| Very high concurrency with lots of I/O waiting | A typical CRUD service |
| Streaming data (SSE, WebSockets) | The team knows imperative code well |
| The whole stack can be reactive | You rely on blocking libraries such as JPA |

### Virtual threads: a simpler alternative

On Java 21+, `spring.threads.virtual.enabled=true` lets ordinary blocking MVC code scale to many concurrent requests with simple, readable code. For many apps this removes the main reason to adopt reactive. Reactive remains valuable for streaming and back-pressure scenarios.

### Pitfalls

- Never call blocking code (`Thread.sleep`, JDBC) on reactive threads.
- Debugging stack traces is harder.
- `Mono`/`Flux` pipelines that are never subscribed do nothing.

---

## 19.2 Kotlin with Spring

**Kotlin** is a concise, null-safe JVM language, and Spring has first-class support for it. Spring Initializr can generate Kotlin projects directly.

### Why use it

- **Null safety** built into the type system (`String` vs `String?`).
- **Data classes** replace boilerplate (like Java records, but older and richer).
- **Concise syntax** and expressive standard library.
- **Coroutines** for readable asynchronous and reactive code.
- 100% interoperable with Java libraries.

### Example

```kotlin
@SpringBootApplication
class DemoApplication

fun main(args: Array<String>) {
    runApplication<DemoApplication>(*args)
}

data class Task(val id: Long?, val title: String, val done: Boolean = false)

@RestController
@RequestMapping("/api/tasks")
class TaskController(private val service: TaskService) {   // constructor injection is built in

    @GetMapping
    fun list(): List<Task> = service.findAll()

    @GetMapping("/{id}")
    fun get(@PathVariable id: Long): ResponseEntity<Task> =
        service.find(id)?.let { ResponseEntity.ok(it) }
            ?: ResponseEntity.notFound().build()
}

@Service
class TaskService(private val repo: TaskRepository) {
    fun findAll(): List<Task> = repo.findAll()
    fun find(id: Long): Task? = repo.findById(id).orElse(null)
}
```

### Build setup

Kotlin classes are `final` by default, but Spring needs to proxy some classes. The Initializr-generated setup includes the right compiler plugins:

- `kotlin-spring` (opens `@Component`, `@Transactional`, and similar classes)
- `kotlin-jpa` (adds no-arg constructors for entities)

And the dependency `com.fasterxml.jackson.module:jackson-module-kotlin` for JSON.

### Coroutines with WebFlux

```kotlin
@GetMapping("/{id}")
suspend fun one(@PathVariable id: String): Product? = repo.findById(id)

@GetMapping
fun all(): Flow<Product> = repo.findAll()
```

`suspend` functions and `Flow` give reactive behavior with sequential-looking code.

### Kotlin-specific features

- **Bean definition DSL** and **router DSL** for functional-style configuration.
- Named and default parameters work well with `@ConfigurationProperties`.
- Be careful with JPA: data classes are not ideal as entities (generated `equals`/`hashCode`, no-arg requirements). Use ordinary classes.

---

## 19.3 Architecture patterns

### Layered architecture (where you start)

```
Controller -> Service -> Repository -> Database
```

Simple and effective for most apps. The risk is that business logic becomes tied to the database and web frameworks.

### Package structure: by layer vs by feature

```
By layer:                      By feature (preferred as the app grows):
controller/                    order/
service/                         OrderController
repository/                      OrderService
model/                           OrderRepository
                               customer/
                                 CustomerController
                                 ...
```

Grouping by feature keeps related code together and makes boundaries explicit.

### Hexagonal architecture (ports and adapters)

Put business logic in the center, independent of frameworks. It talks to the outside through **ports** (interfaces), implemented by **adapters** (web, database, messaging).

```
        [REST adapter]      [Kafka adapter]
               \                /
              (  application core  )  <- pure business logic
               /                \
     [JPA adapter]        [Email adapter]
```

Benefits: easy to test the core without Spring, and the ability to swap infrastructure.

### Modular monolith

One deployable application, divided into well-isolated **modules** with clear public APIs. This delivers most of the organizational benefits of microservices without the distributed-systems cost. **Spring Modulith** supports it:

- Verifies module boundaries in tests.
- Provides event publication between modules, with persistence of events.
- Generates module documentation.

A modular monolith can be split into microservices later if truly needed.

### CQRS and event sourcing

- **CQRS** — separate the model used for **writing** (commands) from the one used for **reading** (queries), so each can be optimized.
- **Event sourcing** — store every change as an immutable event, and rebuild current state from the events.

Both add significant complexity. Use them only where the domain really benefits (audit-heavy or high-read systems).

### Other patterns worth knowing

| Pattern | Problem it solves |
|---|---|
| **Transactional outbox** | Reliably publish messages along with a database change |
| **Saga** | Coordinate a business process across several services without a distributed transaction |
| **Strangler fig** | Gradually replace a legacy system piece by piece |
| **Anti-corruption layer** | Protect your model from an external system's model |
| **Domain-driven design (DDD)** | Model software around the business domain and its language |

### Guiding principles

- Prefer the **simplest design** that works, and evolve it when real pain appears.
- Depend on abstractions at boundaries (Dependency Inversion).
- Keep business rules out of controllers and entities' persistence details.
- Write architecture tests (ArchUnit or Spring Modulith) so structure does not erode.

---

## 19.4 Project ideas

Building real projects is the best way to cement everything. Each idea lists what it practices.

### Beginner

| Project | Practices |
|---|---|
| **To-do list API** | REST controllers, validation, error handling (Lessons 5-7) |
| **Personal book tracker** | JPA entities, repositories, derived queries (Lesson 9) |
| **Notes web app with Thymeleaf** | Views, forms, fragments (Lesson 8) |
| **URL shortener** | Unique codes, redirects, Redis caching |

### Intermediate

| Project | Practices |
|---|---|
| **Blog platform with user accounts** | Spring Security, roles, relationships, pagination |
| **E-commerce back end** | Products, carts, orders, transactions, Flyway migrations |
| **Library management** | Complex relationships, search, `@PreAuthorize` rules |
| **Job board with JWT** | Resource server, role-based access, Testcontainers tests |
| **Expense tracker with reports** | Scheduling, aggregation queries, CSV export |

### Advanced

| Project | Practices |
|---|---|
| **Ride-share or food-delivery simulation** | Microservices, Kafka events, gateway, resilience |
| **Real-time chat or notifications** | WebSockets, WebFlux or SSE, Redis pub/sub |
| **Multi-tenant SaaS starter** | Tenant isolation, OAuth2, Actuator metrics, Docker/Kubernetes |
| **Event-driven order system** | Outbox pattern, sagas, idempotent consumers |
| **Observability demo** | Micrometer, Prometheus, Grafana dashboards, tracing |

### How to approach any project

1. **Write requirements first**: a short list of features and the endpoints they imply.
2. **Design the data model**: entities and relationships on paper.
3. **Build vertical slices**: one feature fully (controller to database to tests) before the next.
4. **Test as you go**: unit tests plus a few integration tests with Testcontainers.
5. **Add production concerns**: security, validation, logging, Actuator health checks.
6. **Containerize and deploy** it somewhere public (free tiers exist), and write a clear README.
7. **Review and refactor** with the architecture ideas above.

### Keep learning

- The official **Spring Boot reference documentation** and **spring.io guides** are authoritative and kept current.
- Read other people's code on GitHub, such as the official `spring-petclinic` sample.
- Contribute to open source, including this repository: add lessons, exercises, and corrections following the contribution guidelines.

*Congratulations on completing the Spring Boot Topic!*

[Previous](./[18]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md)