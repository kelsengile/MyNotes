[Previous](./[2]-Java-Foundations-For-Spring.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[4]-Configuration-And-Profiles.md)

*Basics*

# Lesson 3 - Inversion Of Control And Beans

Inversion of Control is the central idea of Spring. Everything else, including web, data, and security, is built on it.

---

## 3.1 The IoC container

### The problem

Without a framework, a class creates its own dependencies:

```java
public class OrderService {
    private final OrderRepository repo = new JdbcOrderRepository();  // hard-wired
}
```

This is hard to test (you cannot swap in a fake) and hard to change (you must edit the class to use a different repository).

### The idea

With **Inversion of Control (IoC)**, a class declares what it *needs*, and something else decides what to supply. That "something else" is the **IoC container**. Handing dependencies to a class from outside is called **dependency injection (DI)**.

```java
public class OrderService {
    private final OrderRepository repo;

    public OrderService(OrderRepository repo) {   // supplied from outside
        this.repo = repo;
    }
}
```

### Beans and the ApplicationContext

- A **bean** is any object created and managed by the Spring container.
- The **`ApplicationContext`** is the container. It creates beans, wires them together, and manages their lifecycle.

```java
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        ApplicationContext ctx = SpringApplication.run(DemoApplication.class, args);
        OrderService service = ctx.getBean(OrderService.class);   // rarely needed in real code
    }
}
```

In normal code you do not call `getBean` yourself. You simply declare dependencies and Spring injects them.

### Startup flow

1. Spring scans for classes marked as components and for `@Bean` methods.
2. It builds a graph of what depends on what.
3. It creates beans in dependency order and injects them.
4. The application is ready.

If Spring cannot satisfy a dependency (for example, no bean of the required type exists), startup fails with a clear error. This is better than failing later at runtime.

---

## 3.2 Defining beans with annotations

### Stereotype annotations

Annotate a class and component scanning registers it as a bean.

| Annotation | Typical use |
|---|---|
| `@Component` | Generic bean |
| `@Service` | Business logic |
| `@Repository` | Data access (also translates persistence exceptions) |
| `@Controller` / `@RestController` | Web layer |
| `@Configuration` | Holds `@Bean` definitions |

```java
@Service
public class GreetingService {
    public String greet(String name) {
        return "Hello, " + name;
    }
}
```

The specialized annotations behave like `@Component`, but they document intent and enable extra behavior.

### `@Bean` methods

Use `@Bean` for objects you cannot annotate (third-party classes) or that need custom construction.

```java
@Configuration
public class AppConfig {

    @Bean
    public Clock clock() {
        return Clock.systemUTC();
    }

    @Bean
    public ObjectMapper objectMapper() {
        return new ObjectMapper().findAndRegisterModules();
    }
}
```

The method name becomes the default bean name, and the return type is the bean's type.

### Rule of thumb

- **Your own classes** → stereotype annotations.
- **Classes you do not own** → `@Bean` methods.

---

## 3.3 Bean scopes

A **scope** controls how many instances Spring creates and how long each lives.

| Scope | Instances | Notes |
|---|---|---|
| `singleton` (default) | One per container | Shared everywhere; must be thread-safe |
| `prototype` | New one each time it is requested | Spring does not manage its destruction |
| `request` | One per HTTP request | Web apps only |
| `session` | One per HTTP session | Web apps only |
| `application` | One per ServletContext | Web apps only |

```java
@Component
@Scope("prototype")
public class ShoppingCart { }

@Component
@Scope(value = WebApplicationContext.SCOPE_REQUEST, proxyMode = ScopedProxyMode.TARGET_CLASS)
public class RequestInfo { }
```

### Singleton means shared state

Singletons serve many threads at once, so **do not store per-user mutable state in fields**:

```java
@Service
public class BadService {
    private String currentUser;       // DANGER: shared across all requests
}
```

Keep services stateless: pass data through method parameters instead.

### Lifecycle callbacks

```java
@Component
public class CacheWarmer {

    @PostConstruct                // runs after the bean is created and injected
    public void init() { /* load data */ }

    @PreDestroy                   // runs before the container shuts down
    public void cleanup() { /* release resources */ }
}
```

`@PostConstruct` and `@PreDestroy` come from `jakarta.annotation`.

---

## 3.4 Constructor injection

### The recommended style

```java
@Service
public class OrderService {

    private final OrderRepository repo;
    private final EmailService email;

    public OrderService(OrderRepository repo, EmailService email) {
        this.repo = repo;
        this.email = email;
    }
}
```

When a class has a **single constructor**, `@Autowired` is not needed.

### Why prefer constructor injection

- Fields can be `final`, so the object is immutable and always fully initialized.
- Dependencies are explicit; too many constructor parameters is a visible hint that the class does too much.
- You can build the object in a plain unit test with `new OrderService(fakeRepo, fakeEmail)`, with no Spring needed.

Field injection (`@Autowired` on a private field) works but hides dependencies and makes testing harder. Avoid it.

### Multiple candidates

If two beans match the required type, Spring does not know which to pick and fails at startup. Resolve it one of two ways:

```java
public interface PaymentGateway { void pay(double amount); }

@Service @Primary                      // default choice when ambiguous
public class StripeGateway implements PaymentGateway { ... }

@Service("paypal")
public class PayPalGateway implements PaymentGateway { ... }

@Service
public class CheckoutService {
    private final PaymentGateway gateway;

    public CheckoutService(@Qualifier("paypal") PaymentGateway gateway) {  // choose by name
        this.gateway = gateway;
    }
}
```

You can also inject all implementations at once:

```java
public CheckoutService(List<PaymentGateway> gateways) { ... }
```

### Optional dependencies

```java
public ReportService(Optional<AuditService> audit) { ... }
// or: @Autowired(required = false)
```

### Circular dependencies

If A needs B and B needs A, Spring fails at startup. The usual fix is to redesign: extract the shared logic into a third bean.

[Previous](./[2]-Java-Foundations-For-Spring.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[4]-Configuration-And-Profiles.md)