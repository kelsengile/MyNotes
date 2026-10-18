[Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[2]-Java-Foundations-For-Spring.md)

*Basics*

# Lesson 1 - What Is Spring Boot

Spring Boot lets you go from an empty folder to a running web application in minutes. This lesson explains what it is, how it relates to the Spring Framework, and how a Boot project is laid out.

---

## 1.1 Spring Framework vs Spring Boot

The **Spring Framework** is the foundation. It provides:

- **Inversion of Control (IoC) and dependency injection** — the container creates and wires your objects.
- **Spring MVC** — the web layer.
- **Data access support** — JDBC, transactions, and ORM integration.
- **AOP (Aspect-Oriented Programming)** — cross-cutting concerns such as transactions and security.

Used alone, Spring needs a lot of setup: choosing compatible library versions, declaring beans, configuring a web server, and wiring a database.

**Spring Boot** is an opinionated layer on top that removes that setup:

| Concern | Plain Spring Framework | Spring Boot |
|---|---|---|
| Library versions | You pick and align them | Managed for you by a parent/BOM |
| Configuration | Manual bean definitions | Auto-configuration from the classpath |
| Web server | Install and deploy a WAR to Tomcat | Embedded server inside your JAR |
| Running the app | Deploy to a server | `java -jar app.jar` |
| Production features | Build yourself | Actuator included |

Boot does not replace Spring. Your app is still a Spring app, and everything you learn about Spring applies.

---

## 1.2 Auto-configuration and starters

### Starters

A **starter** is a single dependency that pulls in a curated, version-compatible set of libraries for one purpose.

| Starter | What it gives you |
|---|---|
| `spring-boot-starter-web` | Spring MVC, Jackson (JSON), embedded Tomcat |
| `spring-boot-starter-data-jpa` | JPA, Hibernate, Spring Data |
| `spring-boot-starter-security` | Spring Security |
| `spring-boot-starter-test` | JUnit 5, Mockito, AssertJ, Spring Test |
| `spring-boot-starter-actuator` | Health checks and metrics |

### Auto-configuration

When the app starts, Boot looks at what is on your **classpath** and what **beans you have defined**, and configures sensible defaults. For example:

- If `spring-webmvc` and Tomcat are on the classpath, Boot sets up a `DispatcherServlet` and an embedded Tomcat.
- If an H2 database driver is present and no `DataSource` is defined, Boot creates an in-memory database.
- If you define your own `DataSource` bean, Boot backs off and uses yours.

This "back off when the user defines their own" behavior is built with conditional annotations such as `@ConditionalOnClass` and `@ConditionalOnMissingBean`.

### The main annotation

```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

`@SpringBootApplication` is a shortcut for three annotations:

- `@Configuration` — this class can define beans.
- `@EnableAutoConfiguration` — turn on auto-configuration.
- `@ComponentScan` — scan this package and its sub-packages for components.

> Because of `@ComponentScan`, keep the main class in the **root package** (e.g. `com.example.demo`) so all your other packages sit beneath it.

### Seeing what auto-configuration did

Run the app with the `--debug` flag to print a **conditions report** listing which auto-configurations were applied and which were skipped, and why:

```bash
java -jar demo.jar --debug
```

To disable a specific auto-configuration:

```java
@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
```

---

## 1.3 Creating a project with Spring Initializr

**Spring Initializr** at [start.spring.io](https://start.spring.io) generates a ready-to-run project skeleton.

1. Choose **Project**: Maven or Gradle.
2. Choose **Language**: Java (Kotlin and Groovy are also available).
3. Choose the **Spring Boot version**. Pick the latest stable release (not a SNAPSHOT or milestone).
4. Fill in the **metadata**:
   - **Group** — your reverse domain, e.g. `com.example`.
   - **Artifact** — the project name, e.g. `demo`.
   - **Packaging** — `Jar` for almost everything.
   - **Java** — 17 or newer.
5. Add **dependencies**. For this course start with **Spring Web**.
6. Click **Generate**, unzip, and open the folder in your IDE.

Initializr also works from the command line:

```bash
curl https://start.spring.io/starter.zip \
  -d dependencies=web \
  -d type=maven-project \
  -d javaVersion=17 \
  -d name=demo \
  -o demo.zip
```

Run the project with the included wrapper (no Maven install needed):

```bash
# macOS / Linux
./mvnw spring-boot:run

# Windows
mvnw.cmd spring-boot:run
```

The console shows the Spring banner, and the app listens on port **8080** by default.

---

## 1.4 Tour of a Spring Boot project

A freshly generated Maven project looks like this:

```
demo/
├── .mvn/wrapper/            # Maven wrapper support files
├── src/
│   ├── main/
│   │   ├── java/com/example/demo/
│   │   │   └── DemoApplication.java
│   │   └── resources/
│   │       ├── application.properties
│   │       ├── static/      # static files (CSS, JS, images)
│   │       └── templates/   # server-side templates
│   └── test/
│       └── java/com/example/demo/
│           └── DemoApplicationTests.java
├── mvnw / mvnw.cmd          # Maven wrapper scripts
└── pom.xml                  # build and dependency definition
```

| File / folder | Purpose |
|---|---|
| `DemoApplication.java` | Entry point; holds `main` and `@SpringBootApplication` |
| `application.properties` | Configuration (covered in Lesson 4) |
| `static/` | Served as-is at the web root |
| `templates/` | Thymeleaf or other view templates (Lesson 8) |
| `DemoApplicationTests.java` | A starter test that checks the context loads |
| `pom.xml` | Build file; inherits from `spring-boot-starter-parent` |

### Your first endpoint

Create `HelloController.java` next to the main class:

```java
package com.example.demo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello() {
        return "Hello, Spring Boot!";
    }
}
```

Start the app and open `http://localhost:8080/hello`, or run:

```bash
curl http://localhost:8080/hello
```

You wrote no server setup, no XML, and no deployment descriptor. Auto-configuration did that work.

[Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[2]-Java-Foundations-For-Spring.md)