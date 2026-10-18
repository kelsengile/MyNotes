[Previous](./[13]-JWT-And-OAuth2.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[15]-Testing-Spring-Boot-Applications.md)

*Production Readiness*

# Lesson 14 - Actuator And Monitoring

Once an application is deployed, you need to know whether it is healthy, how it is performing, and what it is doing. **Spring Boot Actuator** adds production-ready endpoints for exactly that.

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

---

## 14.1 Health and info endpoints

Actuator exposes operational endpoints under `/actuator`.

### Health

`GET /actuator/health`:

```json
{ "status": "UP" }
```

Boot automatically checks the components it finds: database, disk space, Redis, message brokers, and so on. If any critical component is `DOWN`, the overall status becomes `DOWN` (HTTP 503).

Show details:

```properties
management.endpoint.health.show-details=when-authorized
# values: never (default), when-authorized, always
```

Detailed output:

```json
{
  "status": "UP",
  "components": {
    "db": { "status": "UP", "details": { "database": "PostgreSQL" } },
    "diskSpace": { "status": "UP" }
  }
}
```

### Liveness and readiness probes

Orchestrators like Kubernetes use two separate checks:

```properties
management.endpoint.health.probes.enabled=true
```

| Endpoint | Question | Action on failure |
|---|---|---|
| `/actuator/health/liveness` | Is the process alive? | Restart the container |
| `/actuator/health/readiness` | Can it accept traffic? | Stop routing requests to it |

These are enabled automatically when Boot detects it is running on Kubernetes.

### Choosing which endpoints are exposed

By default over HTTP, only `health` is exposed. Expose more deliberately:

```properties
management.endpoints.web.exposure.include=health,info,metrics,prometheus
# management.endpoints.web.exposure.include=*     # everything: use with great care
management.endpoints.web.exposure.exclude=env
```

### Useful endpoints

| Endpoint | Shows |
|---|---|
| `/actuator/health` | Application health |
| `/actuator/info` | Build and custom info |
| `/actuator/metrics` | Metric names and values |
| `/actuator/prometheus` | Metrics in Prometheus format |
| `/actuator/loggers` | View and change log levels at runtime |
| `/actuator/env` | Environment and configuration properties |
| `/actuator/beans` | All beans in the context |
| `/actuator/mappings` | All request mappings |
| `/actuator/threaddump` | Thread dump |

### Info endpoint

```properties
management.info.env.enabled=true
info.app.name=My Service
info.app.version=1.0.0
```

For build details, add to the Maven plugin configuration:

```xml
<plugin>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-maven-plugin</artifactId>
  <executions>
    <execution><goals><goal>build-info</goal></goals></execution>
  </executions>
</plugin>
```

### A custom health indicator

```java
@Component
public class PaymentGatewayHealthIndicator implements HealthIndicator {

    private final PaymentClient client;

    public PaymentGatewayHealthIndicator(PaymentClient client) { this.client = client; }

    @Override
    public Health health() {
        try {
            client.ping();
            return Health.up().withDetail("gateway", "reachable").build();
        } catch (Exception e) {
            return Health.down(e).build();
        }
    }
}
```

The component appears in the health output as `paymentGateway`.

---

## 14.2 Metrics with Micrometer

**Micrometer** is the metrics facade Boot uses (comparable to SLF4J for logging). You record metrics once, and they can be exported to Prometheus, Datadog, CloudWatch, and others.

### Built-in metrics

Boot records many metrics without any code: JVM memory, garbage collection, thread counts, CPU, HTTP server request timings, database pool usage, and more.

```bash
curl localhost:8080/actuator/metrics
curl localhost:8080/actuator/metrics/jvm.memory.used
curl "localhost:8080/actuator/metrics/http.server.requests?tag=status:200"
```

### Metric types

| Type | Use | Example |
|---|---|---|
| **Counter** | A value that only increases | Orders placed |
| **Gauge** | A value that goes up and down | Items in a queue |
| **Timer** | Duration and count of events | Time to process a payment |
| **Distribution summary** | Distribution of sizes | Request payload size |

### Custom metrics

```java
@Service
public class OrderService {

    private final Counter ordersPlaced;
    private final Timer paymentTimer;

    public OrderService(MeterRegistry registry) {
        this.ordersPlaced = Counter.builder("orders.placed")
                .description("Number of orders placed")
                .tag("channel", "web")
                .register(registry);
        this.paymentTimer = Timer.builder("orders.payment.time")
                .register(registry);
    }

    public void placeOrder(Order order) {
        paymentTimer.record(() -> processPayment(order));
        ordersPlaced.increment();
    }
}
```

Annotation style (requires AOP and registering the `TimedAspect` bean):

```java
@Timed(value = "reports.generate", description = "Time to generate a report")
public Report generate() { ... }
```

### Exporting to Prometheus

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
```

```properties
management.endpoints.web.exposure.include=health,prometheus
```

Prometheus scrapes `/actuator/prometheus` on a schedule. **Grafana** then draws dashboards and alerts from that data.

### Tag guidance

Tags (dimensions) are powerful but each unique combination creates a new time series. Never use unbounded values such as user IDs, emails, or raw URLs as tag values, because this causes a **cardinality explosion**.

### What to watch

| Signal | Why |
|---|---|
| Request rate, error rate, latency (p95/p99) | User-facing health |
| JVM heap and GC pauses | Memory problems |
| DB pool active/pending connections | Pool exhaustion |
| Business counters | Real-world behavior |

---

## 14.3 Securing actuator

Actuator endpoints can expose sensitive information (configuration, heap dumps, bean lists) and even change behavior (log levels, shutdown). Treat them as protected infrastructure.

### Principles

1. **Expose only what you need.**
2. **Require authentication** for everything except basic health probes.
3. **Prefer a separate management port** that is not publicly reachable.

### Separate management port

```properties
management.server.port=9091
management.server.address=127.0.0.1       # or an internal-only network interface
management.endpoints.web.base-path=/actuator
```

Your public traffic uses port 8080, while monitoring tools reach 9091 over the internal network.

### Spring Security rules

```java
@Bean
public SecurityFilterChain actuatorChain(HttpSecurity http) throws Exception {
    http
        .securityMatcher(EndpointRequest.toAnyEndpoint())
        .authorizeHttpRequests(auth -> auth
            .requestMatchers(EndpointRequest.to("health", "info")).permitAll()
            .anyRequest().hasRole("ACTUATOR_ADMIN"))
        .httpBasic(Customizer.withDefaults());
    return http.build();
}
```

If you define several `SecurityFilterChain` beans, give them `@Order` annotations so the more specific chain comes first.

### Sensitive data

- `/actuator/env` and `/actuator/configprops` mask values that look like secrets (password, secret, key, token), but do not rely on masking alone.
- Keep `management.endpoint.health.show-details` set to `when-authorized` or `never` on public endpoints.
- `heapdump`, `threaddump`, and `shutdown` are especially dangerous. Leave them disabled unless truly needed:

```properties
management.endpoint.shutdown.enabled=false
```

### Checklist

- [ ] Only needed endpoints are exposed
- [ ] Authentication on all but liveness/readiness
- [ ] Management port not internet-facing
- [ ] No secrets visible in `env` output

---

## 14.4 Logging

### SLF4J and Logback

Boot uses **SLF4J** as the logging API and **Logback** as the default implementation.

```java
@Service
public class PaymentService {

    private static final Logger log = LoggerFactory.getLogger(PaymentService.class);

    public void pay(String orderId, BigDecimal amount) {
        log.info("Processing payment for order {} amount {}", orderId, amount);   // placeholders, not concatenation
        try {
            // ...
        } catch (Exception e) {
            log.error("Payment failed for order {}", orderId, e);                 // exception goes last
        }
    }
}
```

### Levels (from most to least severe)

| Level | Use for |
|---|---|
| `ERROR` | Something failed and needs attention |
| `WARN` | Unexpected but handled |
| `INFO` | Important business or lifecycle events (default level) |
| `DEBUG` | Developer diagnostics |
| `TRACE` | Very fine detail |

Setting a level shows that level and everything more severe.

### Configuration

```properties
logging.level.root=INFO
logging.level.com.example=DEBUG
logging.level.org.hibernate.SQL=DEBUG
logging.file.name=logs/app.log
logging.logback.rollingpolicy.max-file-size=10MB
logging.logback.rollingpolicy.max-history=14
```

Change a level at runtime without a restart:

```bash
curl -X POST localhost:8080/actuator/loggers/com.example \
  -H "Content-Type: application/json" -d '{"configuredLevel":"DEBUG"}'
```

### Structured (JSON) logging

Log aggregators work best with JSON. Recent Boot versions support it directly:

```properties
logging.structured.format.console=ecs
# other formats include logstash and gelf
```

### Correlating requests with MDC

The **Mapped Diagnostic Context** attaches values to every log line from the current thread:

```java
@Component
public class RequestIdFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest req, HttpServletResponse res, FilterChain chain)
            throws ServletException, IOException {
        MDC.put("requestId", UUID.randomUUID().toString());
        try {
            chain.doFilter(req, res);
        } finally {
            MDC.clear();                      // always clean up
        }
    }
}
```

```properties
logging.pattern.level=%5p [%X{requestId}]
```

### Best practices

- Use parameterized messages: `log.debug("User {}", id)`, not string concatenation.
- **Never log passwords, tokens, or full personal data.**
- Log once per error, at the layer that handles it, and avoid logging and rethrowing the same exception.
- Prefer `INFO` for business events and `DEBUG` for diagnostics. Avoid noisy `INFO` in loops.
- In containers, log to **standard output** and let the platform collect it.

[Previous](./[13]-JWT-And-OAuth2.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[15]-Testing-Spring-Boot-Applications.md)