[Previous](./[3]-Inversion-Of-Control-And-Beans.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[5]-Spring-MVC-And-Controllers.md)

*Basics*

# Lesson 4 - Configuration And Profiles

The same code must run on your laptop, in testing, and in production, with different settings each time. Spring Boot handles this through externalized configuration.

---

## 4.1 application.properties and YAML

Boot reads `src/main/resources/application.properties` (or `application.yml`) at startup.

### Properties format

```properties
server.port=9090
spring.application.name=demo
logging.level.root=INFO
logging.level.com.example=DEBUG
```

### YAML format (same settings)

```yaml
server:
  port: 9090
spring:
  application:
    name: demo
logging:
  level:
    root: INFO
    com.example: DEBUG
```

Use one format per project. YAML is more readable for nested settings. Indentation uses **spaces**, never tabs.

### Common built-in properties

| Property | Meaning |
|---|---|
| `server.port` | HTTP port (default 8080) |
| `spring.application.name` | Application name |
| `spring.datasource.url` | Database URL |
| `logging.level.<package>` | Log level for a package |
| `spring.jpa.hibernate.ddl-auto` | Schema handling for JPA |

Boot documents every property in its official *Common Application Properties* reference.

### Reading a single value

```java
@Component
public class AppInfo {

    @Value("${spring.application.name}")
    private String name;

    @Value("${app.greeting:Hello}")      // after the colon: default if missing
    private String greeting;
}
```

`@Value` is fine for one or two values. For groups of related settings, use `@ConfigurationProperties` (section 4.3).

---

## 4.2 Profiles

A **profile** is a named set of configuration or beans that you switch on for an environment.

### Profile-specific files

```
application.yml          # always loaded (shared defaults)
application-dev.yml      # loaded when "dev" is active
application-prod.yml     # loaded when "prod" is active
```

```yaml
# application-dev.yml
spring:
  datasource:
    url: jdbc:h2:mem:devdb
logging:
  level:
    com.example: DEBUG
```

```yaml
# application-prod.yml
spring:
  datasource:
    url: jdbc:postgresql://db.internal:5432/app
logging:
  level:
    com.example: WARN
```

Profile-specific values override the shared defaults.

### Activating a profile

```properties
# in application.properties
spring.profiles.active=dev
```

```bash
# on the command line
java -jar app.jar --spring.profiles.active=prod

# as an environment variable
SPRING_PROFILES_ACTIVE=prod java -jar app.jar
```

You can activate several at once: `--spring.profiles.active=prod,metrics`.

### Profile-specific beans

```java
@Configuration
public class MailConfig {

    @Bean
    @Profile("dev")
    public MailSender fakeMailSender() { return new ConsoleMailSender(); }

    @Bean
    @Profile("prod")
    public MailSender smtpMailSender() { return new SmtpMailSender(); }
}
```

Use `@Profile("!prod")` for "any profile except prod".

> Do not store production secrets in files committed to Git. See section 4.4.

---

## 4.3 ConfigurationProperties

`@ConfigurationProperties` binds a whole group of properties to a typed Java object.

### Properties

```yaml
app:
  mail:
    host: smtp.example.com
    port: 587
    sender: noreply@example.com
    retries: 3
```

### Binding class (a record)

```java
@ConfigurationProperties(prefix = "app.mail")
public record MailProperties(String host, int port, String sender, int retries) { }
```

### Registering it

```java
@SpringBootApplication
@ConfigurationPropertiesScan          // finds all @ConfigurationProperties classes
public class DemoApplication { ... }
```

Alternatively, register one explicitly with `@EnableConfigurationProperties(MailProperties.class)`.

### Using it

```java
@Service
public class MailService {

    private final MailProperties props;

    public MailService(MailProperties props) {
        this.props = props;
    }

    public void send() {
        System.out.println("Connecting to " + props.host() + ":" + props.port());
    }
}
```

### Validation

Add `spring-boot-starter-validation` and the app fails at startup if configuration is wrong:

```java
@Validated
@ConfigurationProperties(prefix = "app.mail")
public record MailProperties(
        @NotBlank String host,
        @Min(1) @Max(65535) int port,
        @Email String sender,
        @DefaultValue("3") int retries) { }
```

### Benefits over `@Value`

- Type safety and IDE auto-completion (with `spring-boot-configuration-processor`).
- Relaxed binding: `app.mail.sender-name`, `app.mail.senderName`, and `APP_MAIL_SENDERNAME` all map to `senderName`.
- One place to validate.

---

## 4.4 Externalized configuration

**Externalized configuration** means settings live outside your compiled code, so the same JAR runs everywhere with different values.

### Where values can come from (highest priority first, simplified)

1. Command-line arguments (`--server.port=9000`)
2. Java system properties (`-Dserver.port=9000`)
3. OS environment variables (`SERVER_PORT=9000`)
4. Profile-specific files (`application-prod.yml`)
5. `application.properties` / `application.yml`
6. Defaults in code (`@Value("${x:default}")`)

A higher source overrides a lower one. The full order is in the Spring Boot reference documentation under *Externalized Configuration*.

### Environment variable naming

Replace dots with underscores and use upper case:

| Property | Environment variable |
|---|---|
| `spring.datasource.url` | `SPRING_DATASOURCE_URL` |
| `app.mail.host` | `APP_MAIL_HOST` |

### Keeping secrets out of the repository

```yaml
spring:
  datasource:
    url: ${DB_URL}
    username: ${DB_USER}
    password: ${DB_PASSWORD}
```

Placeholders `${...}` are resolved from environment variables or other properties. In production, inject the real values from your platform (a secrets manager, Kubernetes Secrets, CI/CD variables).

### Importing extra files

```yaml
spring:
  config:
    import: optional:file:./local.properties
```

The `optional:` prefix avoids failure when the file is absent. Add `local.properties` to `.gitignore` for personal overrides.

### Inspecting the active configuration

With the Actuator `env` and `configprops` endpoints (Lesson 14) you can see every property and where its value came from. Secure these endpoints well, because they can expose sensitive data.

[Previous](./[3]-Inversion-Of-Control-And-Beans.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[5]-Spring-MVC-And-Controllers.md)