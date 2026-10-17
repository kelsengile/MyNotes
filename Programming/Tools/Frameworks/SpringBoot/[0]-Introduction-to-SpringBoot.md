[⬅ Go to Frameworks](../[0]-Introduction-to-Frameworks.md) or [⬅ Go to Java](/Programming/Syntax/Java/[0]-Introduction-to-Java.md)


<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/spring/spring-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Spring Boot</h1>
</div>


Spring Boot is a Java framework that makes it fast to build production-ready, stand-alone applications and microservices. Built on top of the Spring Framework, it removes most of the manual configuration through auto-configuration, sensible defaults, and starter dependencies.

It is one of the most widely used technologies in enterprise software. This Topic covers Spring's core ideas such as inversion of control, building REST APIs, data access with JPA, security, monitoring, testing, messaging, and deployment.

### Why Study Spring Boot?

- **Enterprise standard** — banks, retailers, and large organizations run enormous systems on Spring Boot.
- **Less configuration, more building** — starters and auto-configuration get an app running in minutes.
- **Production features included** — metrics, health checks, and externalized configuration are built in.
- **Deepens your Java knowledge** — it teaches dependency injection, layered design, and testing patterns used across the industry.

### Spring Boot At A Glance

| Attribute | Details |
|---|---|
| Language | Java (also Kotlin) |
| Type | Backend framework |
| Maintained by | VMware / Broadcom (Spring team) |
| Best for | Enterprise back ends and microservices |
| Pairs well with | Maven or Gradle, PostgreSQL, Docker, Kafka |

---

### Table of Contents

**Basics**

   1. **[What Is Spring Boot](./[1]-What-Is-Spring-Boot.md)**  
       1.1 Spring Framework vs Spring Boot  
       1.2 Auto-configuration and starters  
       1.3 Creating a project with Spring Initializr  
       1.4 Tour of a Spring Boot project  
   2. **[Java Foundations For Spring](./[2]-Java-Foundations-For-Spring.md)**  
       2.1 Annotations  
       2.2 Generics and lambdas  
       2.3 Streams  
       2.4 Maven and Gradle basics  
   3. **[Inversion Of Control And Beans](./[3]-Inversion-Of-Control-And-Beans.md)**  
       3.1 The IoC container  
       3.2 Defining beans with annotations  
       3.3 Bean scopes  
       3.4 Constructor injection  
   4. **[Configuration And Profiles](./[4]-Configuration-And-Profiles.md)**  
       4.1 application.properties and YAML  
       4.2 Profiles  
       4.3 ConfigurationProperties  
       4.4 Externalized configuration  

**Building The Web Layer**

   5. **[Spring MVC And Controllers](./[5]-Spring-MVC-And-Controllers.md)**  
       5.1 Request mapping  
       5.2 Path and request parameters  
       5.3 Request bodies  
       5.4 Response handling  
   6. **[Building REST APIs](./[6]-Building-REST-APIs.md)**  
       6.1 RestController  
       6.2 ResponseEntity and status codes  
       6.3 DTOs and mapping  
       6.4 API versioning  
   7. **[Validation And Exception Handling](./[7]-Validation-And-Exception-Handling.md)**  
       7.1 Bean Validation  
       7.2 ControllerAdvice  
       7.3 ProblemDetail responses  
       7.4 Custom exceptions  
   8. **[Server-Side Views With Thymeleaf](./[8]-Server-Side-Views-With-Thymeleaf.md)**  
       8.1 Template basics  
       8.2 Forms and binding  
       8.3 Fragments and layouts  
       8.4 When to choose views over APIs  

**Data**

   9. **[Spring Data JPA](./[9]-Spring-Data-JPA.md)**  
       9.1 Entities  
       9.2 Repositories  
       9.3 Query methods and JPQL  
       9.4 Relationships  
   10. **[Transactions And Migrations](./[10]-Transactions-And-Migrations.md)**  
       10.1 Transactional boundaries  
       10.2 Isolation and propagation  
       10.3 Flyway or Liquibase  
       10.4 Testing with databases  
   11. **[Other Data Sources](./[11]-Other-Data-Sources.md)**  
       11.1 MongoDB and Redis  
       11.2 Caching abstraction  
       11.3 Connection pools  
       11.4 Choosing a data store  

**Security**

   12. **[Spring Security](./[12]-Spring-Security.md)**  
       12.1 Authentication and authorization  
       12.2 The security filter chain  
       12.3 Password encoding  
       12.4 Method-level security  
   13. **[JWT And OAuth2](./[13]-JWT-And-OAuth2.md)**  
       13.1 Token-based authentication  
       13.2 JWT resource server  
       13.3 OAuth2 login  
       13.4 CORS and CSRF  

**Production Readiness**

   14. **[Actuator And Monitoring](./[14]-Actuator-And-Monitoring.md)**  
       14.1 Health and info endpoints  
       14.2 Metrics with Micrometer  
       14.3 Securing actuator  
       14.4 Logging  
   15. **[Testing Spring Boot Applications](./[15]-Testing-Spring-Boot-Applications.md)**  
       15.1 JUnit and Mockito  
       15.2 MockMvc and test slices  
       15.3 Integration tests  
       15.4 Testcontainers  
   16. **[Async, Scheduling, And Messaging](./[16]-Async-Scheduling-And-Messaging.md)**  
       16.1 Async methods  
       16.2 Scheduled tasks  
       16.3 Application events  
       16.4 Kafka and RabbitMQ basics  
   17. **[Microservices With Spring Cloud](./[17]-Microservices-With-Spring-Cloud.md)**  
       17.1 Service communication  
       17.2 Configuration and discovery  
       17.3 API gateways  
       17.4 Resilience patterns  
   18. **[Deployment](./[18]-Deployment.md)**  
       18.1 Building executable jars  
       18.2 Docker and buildpacks  
       18.3 Cloud platforms  
       18.4 GraalVM native images  
   19. **[Next Steps With Spring Boot](./[19]-Next-Steps-With-Spring-Boot.md)**  
       19.1 Reactive programming with WebFlux  
       19.2 Kotlin with Spring  
       19.3 Architecture patterns  
       19.4 Project ideas
