[Previous](./[6]-Building-REST-APIs.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[8]-Server-Side-Views-With-Thymeleaf.md)

*Building The Web Layer*

# Lesson 7 - Validation And Exception Handling

Never trust client input, and never let raw stack traces reach clients. This lesson covers validating requests and returning clean, consistent error responses.

---

## 7.1 Bean Validation

**Bean Validation** (Jakarta Validation) declares rules as annotations. Hibernate Validator is the implementation.

### Dependency

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

### Annotating a request object

```java
public record RegisterRequest(

        @NotBlank(message = "Name is required")
        @Size(max = 50)
        String name,

        @NotBlank @Email
        String email,

        @NotNull @Min(18) @Max(120)
        Integer age,

        @Pattern(regexp = "^(?=.*[A-Z])(?=.*\\d).{8,}$",
                 message = "Password needs 8+ chars, an uppercase letter and a digit")
        String password
) { }
```

### Common constraints

| Annotation | Checks |
|---|---|
| `@NotNull` | Value is not null |
| `@NotEmpty` | Not null and not empty (strings, collections) |
| `@NotBlank` | Text has at least one non-whitespace character |
| `@Size(min, max)` | Length or size range |
| `@Min` / `@Max` | Numeric bounds |
| `@Positive` / `@PositiveOrZero` | Sign checks |
| `@Email` | Looks like an email address |
| `@Pattern` | Matches a regular expression |
| `@Past` / `@Future` | Date checks |

### Triggering validation

```java
@PostMapping("/register")
public ResponseEntity<Void> register(@Valid @RequestBody RegisterRequest request) {
    service.register(request);
    return ResponseEntity.status(HttpStatus.CREATED).build();
}
```

If validation fails, Spring throws `MethodArgumentNotValidException`, and the response is **400**.

### Validating parameters and nested objects

```java
@Validated                                   // needed on the class for parameter checks
@RestController
public class ItemController {

    @GetMapping("/items/{id}")
    public Item get(@PathVariable @Positive Long id,
                    @RequestParam @Size(min = 2) String q) { ... }
}
```

```java
public record Order(@NotEmpty List<@Valid OrderLine> lines) { }   // @Valid cascades into elements
```

Failures on parameters throw `ConstraintViolationException` (or `HandlerMethodValidationException` in newer Spring versions).

### A custom constraint

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = StrongUsernameValidator.class)
public @interface StrongUsername {
    String message() default "Username must start with a letter";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}

public class StrongUsernameValidator implements ConstraintValidator<StrongUsername, String> {
    @Override
    public boolean isValid(String value, ConstraintValidatorContext ctx) {
        return value != null && Character.isLetter(value.charAt(0));
    }
}
```

---

## 7.2 ControllerAdvice

Without handling, errors produce Boot's generic error response. **`@RestControllerAdvice`** centralizes error handling for all controllers.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, Object>> handleValidation(MethodArgumentNotValidException ex) {
        Map<String, String> fieldErrors = new LinkedHashMap<>();
        ex.getBindingResult().getFieldErrors()
          .forEach(e -> fieldErrors.put(e.getField(), e.getDefaultMessage()));

        Map<String, Object> body = new LinkedHashMap<>();
        body.put("status", 400);
        body.put("error", "Validation failed");
        body.put("fields", fieldErrors);
        return ResponseEntity.badRequest().body(body);
    }

    @ExceptionHandler(Exception.class)               // last-resort fallback
    public ResponseEntity<Map<String, String>> handleOther(Exception ex) {
        // log the real error here; do NOT leak details to the client
        return ResponseEntity.status(500).body(Map.of("error", "Unexpected error"));
    }
}
```

### How it works

- `@ExceptionHandler` marks a method that handles a given exception type.
- The most specific matching handler wins.
- `@RestControllerAdvice` = `@ControllerAdvice` + `@ResponseBody`.
- To limit scope: `@RestControllerAdvice(basePackages = "com.example.api")`.

Sample error output:

```json
{
  "status": 400,
  "error": "Validation failed",
  "fields": { "email": "must be a well-formed email address" }
}
```

---

## 7.3 ProblemDetail responses

**RFC 9457** (which replaces RFC 7807) defines a standard JSON shape for HTTP errors. Spring supports it through the `ProblemDetail` class.

### Standard fields

| Field | Meaning |
|---|---|
| `type` | A URI identifying the problem type |
| `title` | Short, human-readable summary |
| `status` | HTTP status code |
| `detail` | Explanation of this occurrence |
| `instance` | URI of the specific occurrence |

### Returning a ProblemDetail

```java
@ExceptionHandler(ProductNotFoundException.class)
public ProblemDetail handleNotFound(ProductNotFoundException ex) {
    ProblemDetail pd = ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, ex.getMessage());
    pd.setTitle("Product not found");
    pd.setType(URI.create("https://api.example.com/errors/product-not-found"));
    pd.setProperty("productId", ex.getProductId());      // custom extension field
    return pd;
}
```

Response (`Content-Type: application/problem+json`):

```json
{
  "type": "https://api.example.com/errors/product-not-found",
  "title": "Product not found",
  "status": 404,
  "detail": "No product with id 42",
  "instance": "/api/products/42",
  "productId": 42
}
```

### Enabling it for Spring's built-in exceptions

```properties
spring.mvc.problemdetails.enabled=true
```

Alternatively, extend `ResponseEntityExceptionHandler` in your advice class to inherit handling for standard MVC exceptions and override only what you need:

```java
@RestControllerAdvice
public class ApiExceptionHandler extends ResponseEntityExceptionHandler {
    // add your own @ExceptionHandler methods here
}
```

---

## 7.4 Custom exceptions

Define exceptions that describe **business problems**, then map each to an HTTP status in one place.

### Defining them

```java
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String resource, Object id) {
        super(resource + " with id " + id + " not found");
    }
}

public class DuplicateEmailException extends RuntimeException {
    public DuplicateEmailException(String email) {
        super("Email already registered: " + email);
    }
}
```

Extend `RuntimeException` (unchecked). Spring's transaction rollback and the controller layer work most smoothly with unchecked exceptions.

### Throwing from the service

```java
@Service
public class UserService {

    public User get(Long id) {
        return repo.findById(id)
                .orElseThrow(() -> new ResourceNotFoundException("User", id));
    }

    public User register(String email) {
        if (repo.existsByEmail(email)) throw new DuplicateEmailException(email);
        ...
    }
}
```

### Mapping to responses

```java
@RestControllerAdvice
public class ApiExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ProblemDetail notFound(ResourceNotFoundException ex) {
        return ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, ex.getMessage());
    }

    @ExceptionHandler(DuplicateEmailException.class)
    public ProblemDetail conflict(DuplicateEmailException ex) {
        return ProblemDetail.forStatusAndDetail(HttpStatus.CONFLICT, ex.getMessage());
    }
}
```

### Quick alternative

For simple cases, annotate the exception itself:

```java
@ResponseStatus(HttpStatus.NOT_FOUND)
public class ResourceNotFoundException extends RuntimeException { ... }
```

Or throw `new ResponseStatusException(HttpStatus.NOT_FOUND, "message")` directly. A central handler is usually cleaner as the app grows.

### Good practices

- Log unexpected exceptions on the server with full detail, but return generic messages to clients.
- Never expose stack traces, SQL, or internal class names in responses.
- Keep error responses in one consistent format across the whole API.

[Previous](./[6]-Building-REST-APIs.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[8]-Server-Side-Views-With-Thymeleaf.md)