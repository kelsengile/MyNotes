[Previous](./[1]-What-Is-Spring-Boot.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[3]-Inversion-Of-Control-And-Beans.md)

*Basics*

# Lesson 2 - Java Foundations For Spring

Spring Boot code leans heavily on a few Java features. If these are comfortable, the rest of the Topic reads naturally.

---

## 2.1 Annotations

An **annotation** attaches metadata to code. Spring reads that metadata at runtime (using reflection) to decide what to do.

```java
@Service                       // marks a class as a service bean
public class OrderService {

    @Autowired                 // asks Spring to inject a dependency
    private OrderRepository repo;

    @Override                  // compiler-checked override
    public String toString() { return "OrderService"; }
}
```

### Where annotations go

Classes, methods, fields, parameters, and constructors can all be annotated, depending on the annotation's declared `@Target`.

### Annotations with attributes

```java
@GetMapping(path = "/orders", produces = "application/json")
@GetMapping("/orders")   // "value" is the default attribute, so its name can be omitted
```

### Writing your own

```java
import java.lang.annotation.*;

@Retention(RetentionPolicy.RUNTIME)   // keep it available at runtime
@Target(ElementType.METHOD)           // only allowed on methods
public @interface LogExecutionTime {
}
```

- `@Retention(RUNTIME)` is essential: without it, Spring cannot see the annotation while the app runs.
- Spring itself processes annotations in many ways. Some are read during component scanning, others by AOP proxies that wrap your beans.
- **Meta-annotations**: annotations placed on other annotations. `@SpringBootApplication` and `@RestController` are both built this way (`@RestController` = `@Controller` + `@ResponseBody`).

---

## 2.2 Generics and lambdas

### Generics

Generics let a class or method work with a type chosen by the caller, while keeping compile-time type safety.

```java
List<String> names = new ArrayList<>();
names.add("Ana");
// names.add(42);   // compile error: good

public interface JpaRepository<T, ID> { ... }   // T = entity type, ID = id type
```

Spring Data uses this constantly:

```java
public interface UserRepository extends JpaRepository<User, Long> { }
```

This says "a repository of `User` entities whose id is a `Long`".

### Lambdas

A **lambda** is a short anonymous function. It can be used wherever a **functional interface** (an interface with exactly one abstract method) is expected.

```java
// Without a lambda
Runnable r1 = new Runnable() {
    public void run() { System.out.println("hi"); }
};

// With a lambda
Runnable r2 = () -> System.out.println("hi");
```

Common functional interfaces:

| Interface | Shape | Example |
|---|---|---|
| `Function<T,R>` | T → R | `s -> s.length()` |
| `Predicate<T>` | T → boolean | `n -> n > 10` |
| `Consumer<T>` | T → void | `x -> System.out.println(x)` |
| `Supplier<T>` | () → T | `() -> new ArrayList<>()` |

### Method references

When a lambda just calls an existing method, use `::`:

```java
names.forEach(System.out::println);          // x -> System.out.println(x)
names.stream().map(String::toUpperCase);     // s -> s.toUpperCase()
```

### Where Spring uses lambdas

Spring Security configuration uses lambdas heavily (Lesson 12):

```java
http.authorizeHttpRequests(auth -> auth
        .requestMatchers("/public/**").permitAll()
        .anyRequest().authenticated());
```

---

## 2.3 Streams

A **stream** processes a collection through a pipeline: a source, zero or more **intermediate** operations, and one **terminal** operation. Streams do not modify the source.

```java
record Product(String name, double price, String category) {}

List<Product> products = List.of(
    new Product("Pen", 1.5, "office"),
    new Product("Laptop", 900.0, "tech"),
    new Product("Mouse", 25.0, "tech")
);
```

### Common operations

```java
// filter + map + collect
List<String> techNames = products.stream()
        .filter(p -> p.category().equals("tech"))
        .map(Product::name)
        .toList();                       // [Laptop, Mouse]

// sum
double total = products.stream()
        .mapToDouble(Product::price)
        .sum();

// group by
Map<String, List<Product>> byCategory = products.stream()
        .collect(Collectors.groupingBy(Product::category));

// find the first match
Optional<Product> cheap = products.stream()
        .filter(p -> p.price() < 2)
        .findFirst();
```

### Optional

`Optional<T>` represents a value that may be absent, and it avoids returning `null`. Spring Data returns it from `findById`.

```java
String name = repo.findById(1L)
        .map(User::getName)
        .orElse("unknown");
```

### Records

A **record** is a compact immutable data class. Spring Boot code uses records for DTOs and configuration:

```java
public record UserDto(Long id, String name) {}
// auto-generates constructor, accessors (id(), name()), equals, hashCode, toString
```

---

## 2.4 Maven and Gradle basics

Both tools compile your code, manage dependencies, run tests, and package the app. Use either one. Initializr generates both.

### Maven (`pom.xml`)

```xml
<project>
  <modelVersion>4.0.0</modelVersion>

  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.4.0</version>   <!-- use the current stable version -->
  </parent>

  <groupId>com.example</groupId>
  <artifactId>demo</artifactId>
  <version>0.0.1-SNAPSHOT</version>

  <properties>
    <java.version>17</java.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
      <!-- no <version>: the parent manages it -->
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

Key ideas:

- The **parent** supplies tested versions for hundreds of libraries, so you omit `<version>` on Boot-managed dependencies.
- The **Spring Boot Maven plugin** builds the runnable "fat" JAR.

### Gradle (`build.gradle`)

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.4.0'
    id 'io.spring.dependency-management' version '1.1.6'
}

java { sourceCompatibility = '17' }

repositories { mavenCentral() }

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

### Everyday commands

| Task | Maven | Gradle |
|---|---|---|
| Run the app | `./mvnw spring-boot:run` | `./gradlew bootRun` |
| Run tests | `./mvnw test` | `./gradlew test` |
| Build the JAR | `./mvnw clean package` | `./gradlew build` |
| Clean outputs | `./mvnw clean` | `./gradlew clean` |

The built JAR lands in `target/` (Maven) or `build/libs/` (Gradle).

### Dependency scopes

| Maven scope | Gradle config | Meaning |
|---|---|---|
| `compile` (default) | `implementation` | Needed to compile and run |
| `runtime` | `runtimeOnly` | Needed only at runtime (e.g. a DB driver) |
| `test` | `testImplementation` | Only for tests |

[Previous](./[1]-What-Is-Spring-Boot.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[3]-Inversion-Of-Control-And-Beans.md)