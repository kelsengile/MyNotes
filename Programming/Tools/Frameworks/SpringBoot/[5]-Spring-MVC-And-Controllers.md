[Previous](./[4]-Configuration-And-Profiles.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[6]-Building-REST-APIs.md)

*Building The Web Layer*

# Lesson 5 - Spring MVC And Controllers

Spring MVC is the web framework inside Spring Boot. A **controller** is a class that receives HTTP requests and produces responses.

### How a request flows

1. The request arrives at the **DispatcherServlet** (the front controller).
2. It finds the controller method whose mapping matches the URL and HTTP method.
3. Arguments are resolved from the request (path, query string, body, headers).
4. Your method runs and returns a value.
5. The return value is converted into the response (JSON, text, or a view name).

---

## 5.1 Request mapping

### Basic mappings

```java
@RestController
@RequestMapping("/api/books")           // prefix for every method in this class
public class BookController {

    @GetMapping                          // GET    /api/books
    public List<String> list() { return List.of("Dune", "Emma"); }

    @GetMapping("/{id}")                 // GET    /api/books/5
    public String one(@PathVariable Long id) { return "Book " + id; }

    @PostMapping                         // POST   /api/books
    public String create() { return "created"; }

    @PutMapping("/{id}")                 // PUT    /api/books/5
    public String replace(@PathVariable Long id) { return "replaced"; }

    @DeleteMapping("/{id}")              // DELETE /api/books/5
    public void delete(@PathVariable Long id) { }
}
```

### HTTP method annotations

| Annotation | HTTP method | Typical meaning |
|---|---|---|
| `@GetMapping` | GET | Read |
| `@PostMapping` | POST | Create |
| `@PutMapping` | PUT | Replace |
| `@PatchMapping` | PATCH | Partial update |
| `@DeleteMapping` | DELETE | Remove |

All are shortcuts for `@RequestMapping(method = ...)`.

### Narrowing a mapping

```java
@GetMapping(
    path = "/report",
    produces = "application/json",       // only if the client accepts JSON
    params = "format=full",              // only if ?format=full is present
    headers = "X-Client=mobile")         // only if this header is present
public Report report() { ... }

@PostMapping(path = "/import", consumes = "text/csv")   // only for CSV request bodies
public void importCsv(@RequestBody String csv) { }
```

### URL patterns

```java
@GetMapping("/files/{name:.+}")                 // regex: keep file extensions
@GetMapping("/users/{userId}/orders/{orderId}") // several variables
```

If no method matches the URL, the client gets **404**. If the URL matches but the HTTP method does not, it gets **405**.

---

## 5.2 Path and request parameters

### Path variables: identify a resource

```java
@GetMapping("/users/{id}")
public User find(@PathVariable Long id) { ... }          // name matches the placeholder

@GetMapping("/users/{userId}/posts/{postId}")
public Post post(@PathVariable("userId") Long user,
                 @PathVariable("postId") Long post) { ... }
```

### Request parameters: filter, sort, paginate

For `GET /search?q=spring&page=2`:

```java
@GetMapping("/search")
public List<Result> search(
        @RequestParam String q,                              // required by default
        @RequestParam(defaultValue = "0") int page,          // optional with default
        @RequestParam(required = false) String sort) {       // may be null
    ...
}
```

Repeated or list parameters:

```java
// GET /items?tag=a&tag=b   or   /items?tag=a,b
@GetMapping("/items")
public List<Item> byTags(@RequestParam List<String> tag) { ... }
```

If a required parameter is missing, Spring returns **400 Bad Request**. If it cannot be converted (for example, `page=abc`), it also returns 400.

### Headers and cookies

```java
@GetMapping("/info")
public String info(@RequestHeader("User-Agent") String agent,
                   @RequestHeader(value = "X-Trace-Id", required = false) String trace,
                   @CookieValue(value = "theme", defaultValue = "light") String theme) {
    return agent;
}
```

### Binding many parameters into an object

```java
public record BookFilter(String author, Integer year, int page, int size) { }

@GetMapping("/books")
public List<Book> filter(BookFilter filter) { ... }   // ?author=X&year=2020&page=0&size=10
```

### Which to use

| Use | For |
|---|---|
| `@PathVariable` | Identifying a specific resource (`/users/5`) |
| `@RequestParam` | Optional filters, sorting, paging |
| `@RequestHeader` | Metadata such as tracing or content type |

---

## 5.3 Request bodies

### JSON bodies

```java
public record CreateBookRequest(String title, String author, int year) { }

@PostMapping("/books")
public Book create(@RequestBody CreateBookRequest request) {
    return service.create(request);
}
```

Request:

```bash
curl -X POST http://localhost:8080/books \
  -H "Content-Type: application/json" \
  -d '{"title":"Dune","author":"Frank Herbert","year":1965}'
```

Spring uses **Jackson** to convert JSON into your object. Common failures:

- Malformed JSON → **400**.
- Missing or wrong `Content-Type` → **415 Unsupported Media Type**.

### HTML form data

For `application/x-www-form-urlencoded` posts, skip `@RequestBody`:

```java
@PostMapping("/login")
public String login(@RequestParam String username, @RequestParam String password) { ... }

// or bind to an object
@PostMapping("/register")
public String register(@ModelAttribute RegistrationForm form) { ... }
```

### File uploads

```java
@PostMapping(path = "/upload", consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
public String upload(@RequestParam("file") MultipartFile file) throws IOException {
    String name = file.getOriginalFilename();
    long size = file.getSize();
    file.transferTo(Path.of("uploads", name));   // validate the name in real code!
    return "Saved " + name + " (" + size + " bytes)";
}
```

Limit sizes in configuration:

```properties
spring.servlet.multipart.max-file-size=5MB
spring.servlet.multipart.max-request-size=10MB
```

> Never trust an uploaded file name. It could contain `../`. Generate your own safe names.

### Reading the raw body

```java
@PostMapping("/echo")
public String echo(@RequestBody String raw) { return raw; }
```

---

## 5.4 Response handling

### Returning values

```java
@RestController
public class DemoController {

    @GetMapping("/text")
    public String text() { return "plain text"; }          // text/plain

    @GetMapping("/object")
    public Book object() { return new Book("Dune"); }      // converted to JSON

    @GetMapping("/list")
    public List<Book> list() { return List.of(...); }      // JSON array
}
```

### Setting the status

```java
@PostMapping("/books")
@ResponseStatus(HttpStatus.CREATED)        // 201 instead of the default 200
public Book create(@RequestBody Book book) { ... }
```

A method returning `void` or `null` produces an empty body.

### Full control with `ResponseEntity`

```java
@GetMapping("/books/{id}")
public ResponseEntity<Book> find(@PathVariable Long id) {
    return service.find(id)
            .map(ResponseEntity::ok)                          // 200 + body
            .orElseGet(() -> ResponseEntity.notFound().build());  // 404
}
```

(`ResponseEntity` is explored further in Lesson 6.)

### Content negotiation

The client states what it wants using the `Accept` header, and Spring picks a matching converter:

```java
@GetMapping(path = "/data", produces = { "application/json", "application/xml" })
public Data data() { ... }
```

XML output requires an XML converter (such as `jackson-dataformat-xml`) on the classpath.

### `@Controller` vs `@RestController`

| Annotation | Return value is... |
|---|---|
| `@RestController` | Written directly to the response body (JSON/text) |
| `@Controller` | Treated as a **view name** (HTML template), see Lesson 8 |

`@RestController` = `@Controller` + `@ResponseBody`.

[Previous](./[4]-Configuration-And-Profiles.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[6]-Building-REST-APIs.md)