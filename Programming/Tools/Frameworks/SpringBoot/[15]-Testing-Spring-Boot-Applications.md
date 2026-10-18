[Previous](./[14]-Actuator-And-Monitoring.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[16]-Async-Scheduling-And-Messaging.md)

*Production Readiness*

# Lesson 15 - Testing Spring Boot Applications

Automated tests let you change code with confidence. The `spring-boot-starter-test` dependency (included by Initializr) provides everything: JUnit 5, Mockito, AssertJ, Spring Test, JSONassert, and more.

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-test</artifactId>
  <scope>test</scope>
</dependency>
```

### The test pyramid

```
        /\         few   - end-to-end / full integration (slow)
       /--\
      /    \       some  - slice tests, web and data layers
     /------\
    /        \     many  - unit tests (fast, no Spring)
   /----------\
```

Prefer many fast unit tests, and fewer slow integration tests.

---

## 15.1 JUnit and Mockito

### A plain unit test (no Spring)

```java
public class PriceCalculator {
    public double withTax(double net, double rate) {
        if (net < 0) throw new IllegalArgumentException("net must be >= 0");
        return net * (1 + rate);
    }
}
```

```java
import static org.assertj.core.api.Assertions.*;

class PriceCalculatorTest {

    private final PriceCalculator calc = new PriceCalculator();

    @Test
    void addsTax() {
        assertThat(calc.withTax(100, 0.2)).isEqualTo(120.0);
    }

    @Test
    void rejectsNegativePrices() {
        assertThatThrownBy(() -> calc.withTax(-1, 0.2))
                .isInstanceOf(IllegalArgumentException.class)
                .hasMessageContaining("net");
    }

    @ParameterizedTest
    @CsvSource({"100,0.1,110", "50,0.2,60", "0,0.5,0"})
    void calculatesVariousCases(double net, double rate, double expected) {
        assertThat(calc.withTax(net, rate)).isEqualTo(expected);
    }
}
```

### JUnit 5 essentials

| Annotation | Purpose |
|---|---|
| `@Test` | Marks a test method |
| `@BeforeEach` / `@AfterEach` | Run before/after every test |
| `@BeforeAll` / `@AfterAll` | Run once per class (static) |
| `@ParameterizedTest` | Run one test with many inputs |
| `@DisplayName` | Readable test name |
| `@Disabled` | Skip a test |

Structure tests as **Arrange, Act, Assert**: set up data, run the action, check the result. AssertJ's fluent `assertThat(...)` reads well and gives clear failure messages.

### Mocking with Mockito

A **mock** replaces a collaborator so you test one class in isolation.

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

    @Mock
    private OrderRepository repo;

    @Mock
    private EmailService email;

    @InjectMocks
    private OrderService service;

    @Test
    void placesOrderAndSendsEmail() {
        Order order = new Order("book", 2);
        when(repo.save(any(Order.class))).thenReturn(new Order(1L, "book", 2));

        Order saved = service.place(order);

        assertThat(saved.getId()).isEqualTo(1L);
        verify(email).sendConfirmation(saved);          // interaction check
        verify(repo, times(1)).save(order);
    }

    @Test
    void failsWhenRepositoryFails() {
        when(repo.save(any())).thenThrow(new RuntimeException("db down"));

        assertThatThrownBy(() -> service.place(new Order("x", 1)))
                .isInstanceOf(RuntimeException.class);
        verifyNoInteractions(email);
    }
}
```

| Mockito | Meaning |
|---|---|
| `when(x.m()).thenReturn(v)` | Stub a return value |
| `thenThrow(ex)` | Stub an exception |
| `verify(mock).m()` | Assert the method was called |
| `any()`, `eq(v)` | Argument matchers |
| `ArgumentCaptor` | Capture what was passed in |

Constructor injection (Lesson 3) makes these tests trivial, since you can also write `new OrderService(repo, email)` by hand.

---

## 15.2 MockMvc and test slices

### Test slices

A **slice** starts only part of the application, which makes the test fast.

| Annotation | Loads | Typical target |
|---|---|---|
| `@WebMvcTest` | Controllers, advice, JSON, security filters | Controllers |
| `@DataJpaTest` | Entities, repositories, embedded DB | Repositories |
| `@JsonTest` | Jackson setup | JSON serialization |
| `@RestClientTest` | REST client beans | Outgoing HTTP clients |

### Testing a controller with MockMvc

`MockMvc` simulates HTTP requests without starting a real server.

```java
@WebMvcTest(TaskController.class)
class TaskControllerTest {

    @Autowired
    private MockMvc mvc;

    @MockitoBean                                 // replaces the real service in the context
    private TaskService service;

    @Test
    void returnsTask() throws Exception {
        when(service.findById(1L)).thenReturn(Optional.of(new Task(1L, "Learn Spring", false)));

        mvc.perform(get("/api/tasks/1"))
           .andExpect(status().isOk())
           .andExpect(jsonPath("$.title").value("Learn Spring"))
           .andExpect(jsonPath("$.done").value(false));
    }

    @Test
    void returns404WhenMissing() throws Exception {
        when(service.findById(99L)).thenReturn(Optional.empty());

        mvc.perform(get("/api/tasks/99"))
           .andExpect(status().isNotFound());
    }

    @Test
    void createsTask() throws Exception {
        when(service.create("Write tests")).thenReturn(new Task(2L, "Write tests", false));

        mvc.perform(post("/api/tasks")
                .contentType(MediaType.APPLICATION_JSON)
                .content("""
                        {"title":"Write tests","done":false}
                        """))
           .andExpect(status().isCreated())
           .andExpect(header().string("Location", "/api/tasks/2"));
    }
}
```

Imports come from `MockMvcRequestBuilders.*` and `MockMvcResultMatchers.*`.

### Notes

- `@MockitoBean` is the current replacement for the older, deprecated `@MockBean`.
- With Spring Security on the classpath, `@WebMvcTest` applies security. Use `@WithMockUser` or `.with(jwt())` / `.with(csrf())` from `spring-security-test` to simulate authentication and CSRF tokens.
- Test validation: send an invalid body and expect `status().isBadRequest()`.

### `@DataJpaTest` recap

See Lesson 10. It tests repositories against an embedded database and rolls back after each test.

---

## 15.3 Integration tests

An **integration test** runs the real application context, with real beans wired together.

### `@SpringBootTest`

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class TaskApiIntegrationTest {

    @Autowired
    private TestRestTemplate rest;

    @Test
    void createAndFetchTask() {
        ResponseEntity<Task> created = rest.postForEntity(
                "/api/tasks", new TaskRequest("Integration", false), Task.class);

        assertThat(created.getStatusCode()).isEqualTo(HttpStatus.CREATED);
        Long id = created.getBody().id();

        ResponseEntity<Task> fetched = rest.getForEntity("/api/tasks/" + id, Task.class);
        assertThat(fetched.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(fetched.getBody().title()).isEqualTo("Integration");
    }
}
```

| `webEnvironment` | Behavior |
|---|---|
| `MOCK` (default) | Mock web environment, no real server; combine with `@AutoConfigureMockMvc` |
| `RANDOM_PORT` | Real server on a random port (best for HTTP-level tests) |
| `DEFINED_PORT` | Uses the configured port |
| `NONE` | No web environment |

### MockMvc with the full context

```java
@SpringBootTest
@AutoConfigureMockMvc
class FullStackMockMvcTest {
    @Autowired MockMvc mvc;
    // tests run against the real controllers, services and repositories
}
```

### Test profiles and properties

```java
@SpringBootTest
@ActiveProfiles("test")                       // loads application-test.yml
@TestPropertySource(properties = "app.feature.enabled=true")
class ConfiguredTest { }
```

### Speed and isolation

- Every distinct context configuration is cached across tests. Variations such as extra `@MockitoBean`s create new contexts, so keep them consistent where possible.
- Reset state between tests (`@Transactional` rollback, `@Sql` scripts, or explicit cleanup).
- Keep these tests focused on **wiring and critical flows**. Put detailed logic checks in fast unit tests.

---

## 15.4 Testcontainers

**Testcontainers** starts real services (PostgreSQL, Kafka, Redis, ...) in disposable Docker containers during your tests. This avoids "it passed on H2 but failed on PostgreSQL" surprises. Docker must be running.

### Dependencies

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-testcontainers</artifactId>
  <scope>test</scope>
</dependency>
<dependency>
  <groupId>org.testcontainers</groupId>
  <artifactId>junit-jupiter</artifactId>
  <scope>test</scope>
</dependency>
<dependency>
  <groupId>org.testcontainers</groupId>
  <artifactId>postgresql</artifactId>
  <scope>test</scope>
</dependency>
```

### A test with a real PostgreSQL

```java
@SpringBootTest
@Testcontainers
class BookRepositoryIT {

    @Container
    @ServiceConnection                                 // wires the datasource automatically
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16-alpine");

    @Autowired
    private BookRepository repo;

    @Test
    void savesAndLoads() {
        Book saved = repo.save(new Book("Dune", "Frank Herbert", 1965, Genre.FICTION));
        assertThat(repo.findById(saved.getId())).isPresent();
    }
}
```

`@ServiceConnection` tells Boot to point `spring.datasource.*` at the container. You do not set URL, username, or password yourself.

### Other containers

```java
@Container @ServiceConnection
static KafkaContainer kafka = new KafkaContainer(DockerImageName.parse("confluentinc/cp-kafka:7.6.0"));

@Container @ServiceConnection(name = "redis")
static GenericContainer<?> redis = new GenericContainer<>("redis:7").withExposedPorts(6379);
```

### Sharing one container across tests

Declare the container once in a shared `@TestConfiguration` class, annotate its `@Bean` with `@ServiceConnection`, and `@Import` it. This avoids starting a new database per test class.

### Using containers during development

Boot can also start containers when you run the app locally:

```java
public class TestDemoApplication {
    public static void main(String[] args) {
        SpringApplication.from(DemoApplication::main)
                .with(ContainersConfig.class)
                .run(args);
    }
}
```

### Practical tips

- Name integration tests `*IT` and run them with the Maven Failsafe plugin if you want them separate from fast unit tests.
- CI servers need Docker available.
- Pin image versions (`postgres:16-alpine`), not `latest`, so tests stay reproducible.

[Previous](./[14]-Actuator-And-Monitoring.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[16]-Async-Scheduling-And-Messaging.md)