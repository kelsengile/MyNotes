[Previous](./[15]-Testing-Spring-Boot-Applications.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[17]-Microservices-With-Spring-Cloud.md)

*Production Readiness*

# Lesson 16 - Async, Scheduling, And Messaging

Not all work should happen inside the request thread. This lesson covers running work in the background, on a schedule, in response to events, and through message brokers.

---

## 16.1 Async methods

An **async method** runs on a separate thread, so the caller does not wait for it. This is ideal for slow tasks that the caller does not need immediately, such as sending an email.

### Enabling it

```java
@SpringBootApplication
@EnableAsync
public class DemoApplication { ... }
```

### Fire-and-forget

```java
@Service
public class EmailService {

    @Async
    public void sendWelcome(String address) {
        // slow SMTP call runs on a background thread
    }
}
```

### Returning a result

```java
@Async
public CompletableFuture<Report> buildReport(Long id) {
    Report report = generate(id);                 // slow
    return CompletableFuture.completedFuture(report);
}
```

```java
CompletableFuture<Report> a = service.buildReport(1L);
CompletableFuture<Report> b = service.buildReport(2L);

CompletableFuture.allOf(a, b).join();             // run both in parallel and wait for both
```

### Configuring the thread pool

By default Boot provides a task executor. Define your own to control the limits:

```java
@Configuration
public class AsyncConfig {

    @Bean(name = "taskExecutor")
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor ex = new ThreadPoolTaskExecutor();
        ex.setCorePoolSize(4);
        ex.setMaxPoolSize(8);
        ex.setQueueCapacity(100);
        ex.setThreadNamePrefix("async-");
        ex.initialize();
        return ex;
    }
}
```

Or configure through properties:

```properties
spring.task.execution.pool.core-size=4
spring.task.execution.pool.max-size=8
spring.task.execution.pool.queue-capacity=100
```

### Rules and pitfalls

- `@Async` works through a proxy, so the method must be **public** and called from **another bean**. Calling it from within the same class runs it synchronously.
- A `void` async method's exceptions are lost unless you register an `AsyncUncaughtExceptionHandler`. Returning a `CompletableFuture` lets the caller handle errors.
- The security context, MDC, and transaction do **not** automatically follow into the new thread.
- Do not leave unbounded queues. Set limits so a flood of tasks cannot exhaust memory.

### Virtual threads

On Java 21 and newer, virtual threads make many blocking tasks cheap:

```properties
spring.threads.virtual.enabled=true
```

---

## 16.2 Scheduled tasks

A **scheduled task** runs automatically at fixed times or intervals, for example cleanup jobs, report generation, or polling.

### Enabling it

```java
@SpringBootApplication
@EnableScheduling
public class DemoApplication { ... }
```

### Trigger types

```java
@Component
public class MaintenanceJobs {

    private static final Logger log = LoggerFactory.getLogger(MaintenanceJobs.class);

    // every 30 seconds, measured from the START of the previous run
    @Scheduled(fixedRate = 30_000)
    public void heartbeat() { log.info("alive"); }

    // 10 seconds AFTER the previous run FINISHES
    @Scheduled(fixedDelay = 10_000, initialDelay = 5_000)
    public void poll() { ... }

    // every day at 02:30 server time
    @Scheduled(cron = "0 30 2 * * *")
    public void nightlyCleanup() { ... }

    // cron with a time zone, value read from configuration
    @Scheduled(cron = "${jobs.report.cron}", zone = "Asia/Manila")
    public void report() { ... }
}
```

### Cron format (Spring uses six fields)

```
┌───────────── second (0-59)
│ ┌─────────── minute (0-59)
│ │ ┌───────── hour (0-23)
│ │ │ ┌─────── day of month (1-31)
│ │ │ │ ┌───── month (1-12)
│ │ │ │ │ ┌─── day of week (0-7, MON-SUN)
│ │ │ │ │ │
0 0 9 * * MON-FRI      -> 09:00 on weekdays
0 */15 * * * *         -> every 15 minutes
```

### Important behavior

- By default scheduled tasks run on a **single thread**, so a long job delays the others. Increase it with `spring.task.scheduling.pool.size=4`.
- Methods must take no arguments and return `void`.
- **Multiple app instances each run the job.** If it must run once across a cluster, use a distributed lock such as **ShedLock**, or a dedicated scheduler.
- Catch and log exceptions inside the job so failures are visible.
- For complex needs (persistence, retries, dashboards), look at **Quartz** or a job scheduler.

---

## 16.3 Application events

Spring has a built-in **publish/subscribe** mechanism inside one application. It lets components react to something happening without knowing about each other, which gives **loose coupling**.

### Defining and publishing an event

```java
public record UserRegisteredEvent(Long userId, String email) { }

@Service
public class RegistrationService {

    private final UserRepository users;
    private final ApplicationEventPublisher publisher;

    public RegistrationService(UserRepository users, ApplicationEventPublisher publisher) {
        this.users = users;
        this.publisher = publisher;
    }

    @Transactional
    public User register(String email) {
        User user = users.save(new User(email));
        publisher.publishEvent(new UserRegisteredEvent(user.getId(), user.getEmail()));
        return user;
    }
}
```

### Listening

```java
@Component
public class WelcomeEmailListener {

    @EventListener
    public void onUserRegistered(UserRegisteredEvent event) {
        // send a welcome email
    }
}

@Component
public class AnalyticsListener {

    @EventListener
    public void track(UserRegisteredEvent event) { ... }
}
```

The publisher knows nothing about the listeners. You can add more without touching `RegistrationService`.

### Behavior to know

- By default listeners run **synchronously** on the publisher's thread, in the same transaction. A listener exception can break the publisher.
- Add `@Async` to a listener to run it in the background.

### Waiting for the commit

```java
@TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
public void afterCommit(UserRegisteredEvent event) {
    // runs only if the transaction successfully committed
}
```

This prevents sending an email for a registration that was later rolled back. Use it for side effects that must only happen after the data is safely saved.

### Built-in lifecycle events

```java
@EventListener(ApplicationReadyEvent.class)
public void onReady() { log.info("Application is ready"); }
```

Others include `ContextRefreshedEvent` and `ContextClosedEvent`.

### Limits

Application events stay **inside one JVM**. For communication between services, use a message broker (next section).

---

## 16.4 Kafka and RabbitMQ basics

A **message broker** carries messages between services. The sender (producer) does not wait for, or even know about, the receiver (consumer).

Benefits: **decoupling**, **buffering** during traffic spikes, **resilience** (messages wait if a consumer is down), and **scalable** parallel consumers.

### Comparison

| | Apache Kafka | RabbitMQ |
|---|---|---|
| Model | Distributed, durable **log** of events | Message **queue** with flexible routing |
| Messages after consumption | Retained for a configured time; consumers can replay | Removed once acknowledged |
| Strengths | Very high throughput, event streaming, replay | Complex routing, task queues, per-message delivery |
| Typical use | Event-driven systems, analytics, data pipelines | Background jobs, request distribution |

### Kafka

```xml
<dependency>
  <groupId>org.springframework.kafka</groupId>
  <artifactId>spring-kafka</artifactId>
</dependency>
```

```yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092
    consumer:
      group-id: order-service
      auto-offset-reset: earliest
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
    # use String values and convert JSON yourself, or configure JSON (de)serializers
```

```java
@Service
public class OrderProducer {

    private final KafkaTemplate<String, String> kafka;

    public OrderProducer(KafkaTemplate<String, String> kafka) { this.kafka = kafka; }

    public void publish(String orderId, String json) {
        kafka.send("orders", orderId, json);          // topic, key, value
    }
}
```

```java
@Component
public class OrderConsumer {

    @KafkaListener(topics = "orders", groupId = "order-service")
    public void handle(String message) {
        // process the message
    }
}
```

Key Kafka ideas:

- **Topic** — a named stream, split into **partitions**.
- **Key** — messages with the same key always go to the same partition, which preserves their order.
- **Consumer group** — instances in the same group share a topic's partitions, and each message goes to one instance in the group.
- **Offset** — a consumer's position in a partition.

### RabbitMQ

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

```yaml
spring:
  rabbitmq:
    host: localhost
    port: 5672
    username: guest
    password: guest
```

```java
@Configuration
public class RabbitConfig {
    public static final String QUEUE = "orders.queue";

    @Bean
    public Queue ordersQueue() { return new Queue(QUEUE, true); }   // durable
}

@Service
public class RabbitProducer {
    private final RabbitTemplate rabbit;
    public RabbitProducer(RabbitTemplate rabbit) { this.rabbit = rabbit; }

    public void send(String message) {
        rabbit.convertAndSend(RabbitConfig.QUEUE, message);          // default exchange, routed by queue name
    }
}

@Component
public class RabbitConsumer {
    @RabbitListener(queues = RabbitConfig.QUEUE)
    public void receive(String message) { ... }
}
```

Key RabbitMQ ideas: producers publish to an **exchange**, which routes to **queues** by **bindings** and routing keys. Consumers **acknowledge** messages after processing.

### Reliability checklist

- Make consumers **idempotent**, since delivery is typically "at least once" and duplicates can occur.
- Configure **retries** and a **dead-letter** destination (DLQ/DLT) for messages that keep failing.
- Use JSON with a clear schema (and a version) for message payloads.
- Do not publish to a broker inside a database transaction and assume both succeed together. The **transactional outbox pattern** solves this by writing the message to the database first.
- Monitor consumer lag (Kafka) or queue depth (RabbitMQ).

[Previous](./[15]-Testing-Spring-Boot-Applications.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[17]-Microservices-With-Spring-Cloud.md)