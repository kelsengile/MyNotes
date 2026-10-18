[Previous](./[17]-Microservices-With-Spring-Cloud.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[19]-Next-Steps-With-Spring-Boot.md)

*Production Readiness*

# Lesson 18 - Deployment

Deployment means getting your tested application running reliably where real users can reach it. Boot makes this simple because the application is a self-contained program.

---

## 18.1 Building executable jars

### What a Boot JAR is

A normal JAR contains only your classes. A Boot **executable ("fat") JAR** also bundles all dependencies and an embedded web server, so you can run it anywhere Java is installed. There is no separate Tomcat to install.

### Building

```bash
# Maven
./mvnw clean package

# Gradle
./gradlew clean bootJar
```

The `spring-boot-maven-plugin` (or Gradle's Boot plugin) repackages the JAR. Output:

```
target/demo-0.0.1-SNAPSHOT.jar        (Maven)
build/libs/demo-0.0.1-SNAPSHOT.jar    (Gradle)
```

Skip tests when you only need the artifact quickly (not recommended for your main CI build):

```bash
./mvnw clean package -DskipTests
```

### Running

```bash
java -jar target/demo-0.0.1-SNAPSHOT.jar
java -jar app.jar --spring.profiles.active=prod --server.port=9000
java -Xms256m -Xmx512m -jar app.jar
```

### JVM options and memory

```bash
JAVA_TOOL_OPTIONS="-XX:MaxRAMPercentage=75" java -jar app.jar
```

In containers, `MaxRAMPercentage` sizes the heap relative to the container's memory limit and is usually better than a fixed `-Xmx`.

### Running as a service

On a Linux server, run it with **systemd** so it starts on boot and restarts on failure:

```ini
# /etc/systemd/system/demo.service
[Unit]
Description=Demo Spring Boot app
After=network.target

[Service]
User=demo
ExecStart=/usr/bin/java -jar /opt/demo/app.jar
Environment=SPRING_PROFILES_ACTIVE=prod
Restart=always
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable --now demo
sudo journalctl -u demo -f
```

### WAR files

Boot can also produce a **WAR** (`<packaging>war</packaging>`) for deployment into an existing Tomcat. This is rarely needed for new projects.

---

## 18.2 Docker and buildpacks

A **container image** packages your app, the Java runtime, and its configuration, so it runs identically on a laptop, in CI, and in production.

### Option 1: a Dockerfile

Simple version:

```dockerfile
FROM eclipse-temurin:21-jre
WORKDIR /app
COPY target/demo-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 8080
USER 1000
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Multi-stage version (builds inside Docker, with a small final image):

```dockerfile
# Stage 1: build
FROM eclipse-temurin:21-jdk AS build
WORKDIR /src
COPY . .
RUN ./mvnw -q clean package -DskipTests

# Stage 2: run
FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=build /src/target/*.jar app.jar
EXPOSE 8080
USER 1000
ENTRYPOINT ["java", "-XX:MaxRAMPercentage=75", "-jar", "app.jar"]
```

```bash
docker build -t demo:1.0 .
docker run -p 8080:8080 -e SPRING_PROFILES_ACTIVE=prod demo:1.0
```

Pass configuration and secrets as **environment variables**, never baked into the image.

### Layered jars (faster rebuilds)

Dependencies change rarely, while your code changes often. Splitting them into layers lets Docker cache the dependency layer:

```dockerfile
FROM eclipse-temurin:21-jre AS builder
WORKDIR /app
COPY target/*.jar app.jar
RUN java -Djarmode=tools -jar app.jar extract --layers --destination extracted

FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=builder /app/extracted/dependencies/ ./
COPY --from=builder /app/extracted/spring-boot-loader/ ./
COPY --from=builder /app/extracted/snapshot-dependencies/ ./
COPY --from=builder /app/extracted/application/ ./
ENTRYPOINT ["java", "-jar", "app.jar"]
```

(The extract command's syntax changed across Boot versions. Check the "Efficient container images" page of the Boot reference for yours.)

### Option 2: Cloud Native Buildpacks (no Dockerfile)

```bash
./mvnw spring-boot:build-image -Dspring-boot.build-image.imageName=demo:1.0
docker run -p 8080:8080 demo:1.0
```

Buildpacks create an optimized, layered image with sensible JVM memory settings, and they are patched for security without you maintaining a Dockerfile.

| | Dockerfile | Buildpacks |
|---|---|---|
| Control | Full | Less |
| Maintenance | You | Automatic base image updates |
| Setup effort | More | Almost none |

### Docker Compose for local development

```yaml
services:
  app:
    image: demo:1.0
    ports: ["8080:8080"]
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://db:5432/app
      SPRING_DATASOURCE_USERNAME: app
      SPRING_DATASOURCE_PASSWORD: secret
    depends_on: [db]
  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: app
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secret
```

### Container checklist

- Run as a **non-root** user.
- Use a **JRE** image, not a full JDK, for runtime.
- Pin image versions.
- Add a `.dockerignore` (exclude `target/`, `.git/`, and so on).
- Log to standard output.

---

## 18.3 Cloud platforms

### Twelve-factor principles

Boot suits the **twelve-factor app** approach. The most important points:

- **Config in the environment** — no environment-specific values in the image.
- **Stateless processes** — keep session and state in a database or Redis, so any instance can serve any request.
- **Logs as streams** — write to standard output.
- **Disposability** — fast startup and graceful shutdown.
- **Dev/prod parity** — use the same kinds of services (this is where Testcontainers helps).

### Graceful shutdown

```properties
server.shutdown=graceful
spring.lifecycle.timeout-per-shutdown-phase=30s
```

On a stop signal, the app finishes in-flight requests before exiting. This avoids dropped requests during rolling deployments.

### Deployment targets

| Target | Description | Good for |
|---|---|---|
| **PaaS** (Heroku, Render, Railway, Azure App Service, Google App Engine, Cloud Foundry) | Push code or JAR; platform runs it | Small teams, fast start |
| **Container services** (AWS ECS/Fargate, Google Cloud Run, Azure Container Apps) | Run containers without managing servers | Simple scalable containers |
| **Kubernetes** (EKS, GKE, AKS, self-managed) | Full orchestration | Many services, advanced needs |
| **VM / bare server** | You manage the OS and Java | Full control, more ops work |

Many platforms provide the port in a `PORT` variable. Map it:

```properties
server.port=${PORT:8080}
```

### Kubernetes essentials

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo
spec:
  replicas: 3
  selector:
    matchLabels: { app: demo }
  template:
    metadata:
      labels: { app: demo }
    spec:
      containers:
        - name: demo
          image: registry.example.com/demo:1.0
          ports: [{ containerPort: 8080 }]
          env:
            - name: SPRING_PROFILES_ACTIVE
              value: prod
            - name: SPRING_DATASOURCE_PASSWORD
              valueFrom:
                secretKeyRef: { name: db-secret, key: password }
          readinessProbe:
            httpGet: { path: /actuator/health/readiness, port: 8080 }
          livenessProbe:
            httpGet: { path: /actuator/health/liveness, port: 8080 }
          resources:
            requests: { cpu: "250m", memory: "512Mi" }
            limits: { memory: "768Mi" }
```

The readiness and liveness probes rely on the Actuator endpoints from Lesson 14.

### CI/CD pipeline

```
push -> build -> unit tests -> integration tests -> build image -> scan -> push to registry -> deploy
```

Automate it (GitHub Actions, GitLab CI, Jenkins). Deploy the **same image** to every environment and change only configuration. Plan for rollback. Rolling or blue/green deployments make that easier.

### Database schema in production

Apply schema changes with Flyway or Liquibase (Lesson 10). Make changes **backward compatible** so old and new app versions can run together during a rolling update.

---

## 18.4 GraalVM native images

**GraalVM Native Image** compiles your application **ahead of time (AOT)** into a standalone native executable, with no JVM needed at runtime.

### Benefits and trade-offs

| | JVM | Native image |
|---|---|---|
| Startup time | Seconds | Milliseconds |
| Memory use | Higher | Much lower |
| Peak throughput | Excellent (JIT optimizes over time) | Often somewhat lower |
| Build time | Fast | Slow (minutes) and memory-hungry |
| Dynamic features (reflection, proxies, resources) | Work freely | Must be known at build time |
| Debugging and tooling | Rich | More limited |

Native images shine for **serverless functions, scale-to-zero services, and CLI tools**, where startup time and memory cost matter most. For long-running services, the regular JVM is often just as good and much simpler.

### How Boot supports it

Boot performs AOT processing at build time (generating configuration and hints for reflection and proxies) so many applications compile natively with little effort.

### Building a native executable

Requires a GraalVM JDK and a native toolchain, or Docker for the buildpack route.

```bash
# Native executable on your machine (Maven)
./mvnw -Pnative native:compile
./target/demo

# Native container image using buildpacks (no local GraalVM needed)
./mvnw -Pnative spring-boot:build-image
docker run --rm -p 8080:8080 demo:0.0.1-SNAPSHOT
```

Projects generated by Initializr include the `native` profile and the GraalVM plugin.

### Testing natively

```bash
./mvnw -PnativeTest test
```

Always run your tests in native mode too, since behavior can differ.

### Common issues

- **Reflection, resources, and JNI** must be registered. Spring handles its own parts, but libraries that use reflection heavily may need hints (`@RegisterReflectionForBinding`, `RuntimeHints`).
- Some libraries do not yet support native images.
- Profiles and conditions that depend on the classpath are **fixed at build time**, so you cannot change them at runtime.

### Alternatives for faster startup on the JVM

- **CDS / AppCDS** (class data sharing) and Project **CRaC** (checkpoint/restore) reduce JVM startup.
- **Virtual threads** (`spring.threads.virtual.enabled=true`) improve concurrency for blocking code.

Measure before committing to native. Many teams find the regular JVM in a container is entirely sufficient.

[Previous](./[17]-Microservices-With-Spring-Cloud.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[19]-Next-Steps-With-Spring-Boot.md)