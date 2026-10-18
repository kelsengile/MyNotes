[Previous](./[16]-Async-Scheduling-And-Messaging.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[18]-Deployment.md)

*Production Readiness*

# Lesson 17 - Microservices With Spring Cloud

A **microservice architecture** splits a system into small, independently deployable services, each owning one business capability and its own data. **Spring Cloud** provides tools for the problems that appear once you have many services talking over a network.

### Should you use microservices?

They add real costs: network failures, distributed data, deployment complexity, and harder debugging. Start with a well-structured monolith and split only when team size, scaling needs, or deployment independence justify it (see Lesson 19).

### Version compatibility

Spring Cloud releases are versioned separately (for example by year-based release trains) and each supports specific Spring Boot versions. Always import the Spring Cloud **BOM** that matches your Boot version, and check the official compatibility table:

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-dependencies</artifactId>
      <version>${spring-cloud.version}</version>   <!-- pick the release train matching your Boot version -->
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

---

## 17.1 Service communication

Services call each other in two ways.

| Style | Examples | Characteristics |
|---|---|---|
| **Synchronous** | HTTP/REST, gRPC | Simple; the caller waits; tightly coupled in time |
| **Asynchronous** | Kafka, RabbitMQ (Lesson 16) | Decoupled; more resilient; eventual consistency |

### `RestClient` (modern synchronous HTTP client)

```java
@Configuration
public class ClientConfig {

    @Bean
    public RestClient inventoryClient(RestClient.Builder builder) {
        return builder
                .baseUrl("http://inventory-service")
                .defaultHeader("Accept", "application/json")
                .build();
    }
}

@Service
public class InventoryGateway {

    private final RestClient client;

    public InventoryGateway(RestClient inventoryClient) { this.client = inventoryClient; }

    public StockLevel stockFor(String sku) {
        return client.get()
                .uri("/stock/{sku}", sku)
                .retrieve()
                .onStatus(HttpStatusCode::is4xxClientError,
                          (req, res) -> { throw new SkuNotFoundException(sku); })
                .body(StockLevel.class);
    }
}
```

`RestClient` replaces the older `RestTemplate` for new code. `WebClient` (from WebFlux) is the reactive alternative.

### Declarative HTTP interfaces

Describe the remote API as an interface:

```java
@HttpExchange("/stock")
public interface InventoryApi {

    @GetExchange("/{sku}")
    StockLevel stockFor(@PathVariable String sku);

    @PostExchange
    void reserve(@RequestBody ReserveRequest request);
}

@Bean
public InventoryApi inventoryApi(RestClient.Builder builder) {
    RestClient client = builder.baseUrl("http://inventory-service").build();
    return HttpServiceProxyFactory
            .builderFor(RestClientAdapter.create(client))
            .build()
            .createClient(InventoryApi.class);
}
```

**OpenFeign** (`spring-cloud-starter-openfeign`) offers a similar declarative style and is still widely used.

### Good practices

- **Always set timeouts** on connections and reads. The default is often "wait forever".
- Do not share a database between services; communicate through APIs or events.
- Do not call chains of five services synchronously for one user request, because latency and failure risk multiply.
- Pass **correlation IDs** (and use distributed tracing with Micrometer Tracing and OpenTelemetry) to follow a request across services.
- Version your APIs and prefer backward-compatible changes (Lesson 6).

---

## 17.2 Configuration and discovery

### Centralized configuration

With many services, managing configuration files in each repository becomes painful. **Spring Cloud Config Server** serves configuration from a Git repository.

Server:

```java
@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication { ... }
```

```yaml
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/example/config-repo
```

Client (each service):

```yaml
spring:
  application:
    name: orders-service
  config:
    import: "configserver:http://config-server:8888"
```

The client loads `orders-service.yml` (plus profile variants) from the Git repository.

### Service discovery

Service addresses change constantly (autoscaling, restarts). **Discovery** lets services find each other by **name** instead of fixed IPs.

```
1. Each instance registers itself with a registry on startup.
2. Callers ask the registry for "inventory-service" and get its live instances.
3. A client-side load balancer picks one.
```

Using **Eureka**:

```java
@SpringBootApplication
@EnableEurekaServer
public class RegistryApplication { ... }       // the registry
```

```yaml
# each service
eureka:
  client:
    service-url:
      defaultZone: http://registry:8761/eureka
```

With Spring Cloud LoadBalancer, a load-balanced client resolves service names:

```java
@Bean
@LoadBalanced
public RestClient.Builder loadBalancedBuilder() {
    return RestClient.builder();
}
// then: baseUrl("http://inventory-service") is resolved through the registry
```

### Use the platform when you can

If you deploy to **Kubernetes**, it already provides service discovery (DNS names like `http://inventory-service`) and configuration (ConfigMaps and Secrets). Often you do not need Eureka or Config Server in that case. Spring Cloud Kubernetes bridges the two where needed.

| Need | Spring Cloud option | Kubernetes option |
|---|---|---|
| Discovery | Eureka / Consul | Services + DNS |
| Central config | Config Server | ConfigMaps / Secrets |
| Load balancing | Spring Cloud LoadBalancer | Service / Ingress |

---

## 17.3 API gateways

An **API gateway** is the single entry point for clients. It routes requests to the right service and handles shared concerns in one place.

```
Client -> [ Gateway ] -> orders-service
                      -> users-service
                      -> inventory-service
```

### What a gateway typically does

- Routing by path or header
- Authentication and token validation
- Rate limiting
- CORS
- Request and response modification
- Logging and tracing entry
- Hiding internal service layout from clients

### Spring Cloud Gateway

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-gateway-server-webflux</artifactId>
</dependency>
```

(Artifact names changed in recent releases. Check the Spring Cloud Gateway docs for your version.)

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: orders
          uri: http://orders-service:8080
          predicates:
            - Path=/api/orders/**
          filters:
            - StripPrefix=1
            - AddRequestHeader=X-Gateway, spring
        - id: users
          uri: lb://users-service            # lb:// = load-balanced via discovery
          predicates:
            - Path=/api/users/**
            - Method=GET,POST
```

| Term | Meaning |
|---|---|
| **Route** | A destination plus the conditions to reach it |
| **Predicate** | A condition (path, method, header, time) |
| **Filter** | A transformation before or after the call |

### Gateway filters in Java

```java
@Bean
public RouteLocator routes(RouteLocatorBuilder builder) {
    return builder.routes()
            .route("orders", r -> r.path("/api/orders/**")
                    .filters(f -> f.stripPrefix(1))
                    .uri("http://orders-service:8080"))
            .build();
}
```

### Cautions

- The gateway is a single point of failure. Run multiple instances.
- Keep business logic **out** of it.
- Still validate authorization inside each service. Do not trust that traffic always came through the gateway.

---

## 17.4 Resilience patterns

In a distributed system, **failures are normal**: services are slow, restart, or are overloaded. Resilience patterns stop one failing service from bringing everything down (a **cascading failure**).

### Resilience4j

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
</dependency>
```

(Resilience4j can also be used directly through its own Spring Boot starter and annotations.)

### The core patterns

| Pattern | Purpose |
|---|---|
| **Timeout** | Stop waiting after a limit |
| **Retry** | Try again after transient errors, with backoff |
| **Circuit breaker** | Stop calling a failing service for a while |
| **Fallback** | Return a degraded answer when the call fails |
| **Bulkhead** | Limit concurrent calls so one dependency cannot use every thread |
| **Rate limiter** | Cap calls per time period |

### Circuit breaker states

```
CLOSED  --(failure rate too high)-->  OPEN  --(wait time passes)-->  HALF_OPEN
   ^                                    |                                  |
   |                    calls fail fast without calling the service        |
   +---------------(trial calls succeed)-----------------------------------+
```

- **CLOSED** — normal; calls go through and failures are counted.
- **OPEN** — calls fail immediately. This protects the service and gives it time to recover.
- **HALF_OPEN** — a few trial calls test whether it has recovered.

### Using annotations

```java
@Service
public class RecommendationClient {

    @CircuitBreaker(name = "recommendations", fallbackMethod = "defaultRecommendations")
    @Retry(name = "recommendations")
    @TimeLimiter(name = "recommendations")       // note: requires an async return type such as CompletableFuture
    public CompletableFuture<List<Product>> fetch(Long userId) {
        return CompletableFuture.supplyAsync(() -> remoteCall(userId));
    }

    private CompletableFuture<List<Product>> defaultRecommendations(Long userId, Throwable t) {
        return CompletableFuture.completedFuture(List.of());       // popular items or an empty list
    }
}
```

A simple synchronous version needs only `@CircuitBreaker` and `@Retry`. The fallback method must have the same return type and accept the original arguments plus the exception.

### Configuration

```yaml
resilience4j:
  circuitbreaker:
    instances:
      recommendations:
        sliding-window-size: 20
        failure-rate-threshold: 50           # open when 50% of calls fail
        wait-duration-in-open-state: 30s
        permitted-number-of-calls-in-half-open-state: 3
  retry:
    instances:
      recommendations:
        max-attempts: 3
        wait-duration: 500ms
        enable-exponential-backoff: true
        exponential-backoff-multiplier: 2
```

### Rules for safe retries

- Retry only **transient** errors (timeouts, 503), never validation failures (400).
- Retry only **idempotent** operations, or use idempotency keys. Retrying "charge the card" can charge twice.
- Always use **backoff** (and ideally jitter) so retries do not stampede a struggling service.
- Cap the total attempts and total time.

### Design for degradation

Decide in advance what the user sees when a dependency is down. Hide the recommendations panel, show cached data, or queue the action for later. Test failure behavior, not just success.

[Previous](./[16]-Async-Scheduling-And-Messaging.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[18]-Deployment.md)