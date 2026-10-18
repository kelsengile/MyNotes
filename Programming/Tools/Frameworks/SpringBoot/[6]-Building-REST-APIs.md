[Previous](./[5]-Spring-MVC-And-Controllers.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[7]-Validation-And-Exception-Handling.md)

*Building The Web Layer*

# Lesson 6 - Building REST APIs

REST is a style for designing web APIs around **resources** (nouns) and standard HTTP methods (verbs). This lesson builds a complete CRUD API.

---

## 6.1 RestController

### REST conventions

| Operation | Method | URL | Success status |
|---|---|---|---|
| List | GET | `/api/tasks` | 200 |
| Read one | GET | `/api/tasks/{id}` | 200 |
| Create | POST | `/api/tasks` | 201 |
| Replace/update | PUT | `/api/tasks/{id}` | 200 |
| Delete | DELETE | `/api/tasks/{id}` | 204 |

Use plural nouns for URLs (`/tasks`, not `/getTasks`). The HTTP method states the action.

### A layered design

```
Controller  ->  Service  ->  Repository
 (HTTP)        (logic)        (data)
```

The controller handles HTTP only. Business rules live in the service.

### A working example (in-memory storage)

```java
public record Task(Long id, String title, boolean done) { }
```

```java
@Service
public class TaskService {

    private final Map<Long, Task> store = new ConcurrentHashMap<>();
    private final AtomicLong sequence = new AtomicLong();

    public List<Task> findAll() { return List.copyOf(store.values()); }

    public Optional<Task> findById(Long id) { return Optional.ofNullable(store.get(id)); }

    public Task create(String title) {
        Task task = new Task(sequence.incrementAndGet(), title, false);
        store.put(task.id(), task);
        return task;
    }

    public Optional<Task> update(Long id, String title, boolean done) {
        if (!store.containsKey(id)) return Optional.empty();
        Task updated = new Task(id, title, done);
        store.put(id, updated);
        return Optional.of(updated);
    }

    public boolean delete(Long id) { return store.remove(id) != null; }
}
```

```java
@RestController
@RequestMapping("/api/tasks")
public class TaskController {

    private final TaskService service;

    public TaskController(TaskService service) {
        this.service = service;
    }

    @GetMapping
    public List<Task> list() { return service.findAll(); }

    @GetMapping("/{id}")
    public ResponseEntity<Task> get(@PathVariable Long id) {
        return service.findById(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    // ... create/update/delete shown in the next section
}
```

---

## 6.2 ResponseEntity and status codes

`ResponseEntity<T>` lets you set the **status**, **headers**, and **body** together.

### Completing the controller

```java
public record TaskRequest(String title, boolean done) { }

@PostMapping
public ResponseEntity<Task> create(@RequestBody TaskRequest req) {
    Task created = service.create(req.title());
    URI location = URI.create("/api/tasks/" + created.id());
    return ResponseEntity.created(location).body(created);       // 201 + Location header
}

@PutMapping("/{id}")
public ResponseEntity<Task> update(@PathVariable Long id, @RequestBody TaskRequest req) {
    return service.update(id, req.title(), req.done())
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
}

@DeleteMapping("/{id}")
public ResponseEntity<Void> delete(@PathVariable Long id) {
    return service.delete(id)
            ? ResponseEntity.noContent().build()                 // 204
            : ResponseEntity.notFound().build();                 // 404
}
```

### Status codes to know

| Code | Name | When to use |
|---|---|---|
| 200 | OK | Successful read or update |
| 201 | Created | Resource created (include `Location`) |
| 204 | No Content | Success with nothing to return (delete) |
| 400 | Bad Request | Invalid input |
| 401 | Unauthorized | Not authenticated |
| 403 | Forbidden | Authenticated but not allowed |
| 404 | Not Found | Resource does not exist |
| 409 | Conflict | State conflict, such as a duplicate |
| 422 | Unprocessable Content | Well-formed but semantically invalid |
| 500 | Internal Server Error | Unexpected server failure |

### Custom headers

```java
return ResponseEntity.ok()
        .header("X-Total-Count", String.valueOf(total))
        .body(items);
```

### Testing with curl

```bash
curl -i -X POST localhost:8080/api/tasks \
  -H "Content-Type: application/json" -d '{"title":"Learn Spring","done":false}'

curl -i localhost:8080/api/tasks/1
curl -i -X DELETE localhost:8080/api/tasks/1
```

---

## 6.3 DTOs and mapping

A **DTO (Data Transfer Object)** is a class that defines exactly what travels over the API. It is separate from your internal model (such as a JPA entity).

### Why not expose entities directly

- **Security** — an entity may hold `passwordHash` or internal flags that must never leak.
- **Stability** — renaming a database column should not break API clients.
- **Shape** — the API often needs fewer, combined, or reshaped fields.
- **Over-posting** — a client could set fields like `id` or `role` if you bind straight into the entity.

### Separate request and response types

```java
public record CreateUserRequest(String name, String email, String password) { }
public record UserResponse(Long id, String name, String email) { }   // no password
```

### Manual mapping

```java
public class UserMapper {

    public static User toEntity(CreateUserRequest r, String passwordHash) {
        User u = new User();
        u.setName(r.name());
        u.setEmail(r.email());
        u.setPasswordHash(passwordHash);
        return u;
    }

    public static UserResponse toResponse(User u) {
        return new UserResponse(u.getId(), u.getName(), u.getEmail());
    }
}
```

```java
@GetMapping("/{id}")
public UserResponse get(@PathVariable Long id) {
    return UserMapper.toResponse(service.getUser(id));
}
```

### Mapping libraries

For large models, a library can generate mapping code at compile time. **MapStruct** is the most common:

```java
@Mapper(componentModel = "spring")
public interface UserMapper {
    UserResponse toResponse(User user);
    User toEntity(CreateUserRequest request);
}
```

Manual mapping is perfectly fine for small projects, and it is explicit and easy to debug.

### Naming guidance

Use consistent suffixes such as `...Request` and `...Response`, and keep DTOs in a `dto` package or next to their controller.

---

## 6.4 API versioning

APIs change. **Versioning** lets old clients keep working while new ones use the improved contract.

### What counts as a breaking change

Removing or renaming a field, changing a type, changing a status code, or making an optional field required. Adding an optional field is usually safe.

### Common strategies

| Strategy | Example | Notes |
|---|---|---|
| URI path | `/api/v1/users` | Most visible and simplest; easy to route and cache |
| Request header | `X-API-Version: 2` | Clean URLs; harder to try in a browser |
| Media type | `Accept: application/vnd.example.v2+json` | Purist approach; more complex |
| Query parameter | `/api/users?version=2` | Easy but easy to forget |

### URI versioning in practice

```java
@RestController
@RequestMapping("/api/v1/users")
public class UserControllerV1 {
    @GetMapping("/{id}")
    public UserResponseV1 get(@PathVariable Long id) { ... }
}

@RestController
@RequestMapping("/api/v2/users")
public class UserControllerV2 {
    @GetMapping("/{id}")
    public UserResponseV2 get(@PathVariable Long id) { ... }   // adds new fields
}
```

Both controllers can call the same service and differ only in their DTOs.

### Header versioning

```java
@GetMapping(path = "/users/{id}", headers = "X-API-Version=2")
public UserResponseV2 getV2(@PathVariable Long id) { ... }
```

### Framework support

Recent Spring releases (Spring Framework 7 and Spring Boot 4) add built-in API versioning support. Check the official reference documentation for the exact configuration in your Boot version.

### Practical advice

- Prefer **additive** changes so you rarely need a new version.
- Announce deprecations and keep old versions for a defined period.
- Do not create a new version for every small change. Version only on breaking ones.

[Previous](./[5]-Spring-MVC-And-Controllers.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[7]-Validation-And-Exception-Handling.md)