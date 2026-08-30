[⬅ Back to README](../../../README.md)

# Java


Welcome! This is a self-paced course for learning Java, a general-purpose, class-based, object-oriented programming language known for its "write once, run anywhere" portability, strong typing, and widespread use across enterprise systems, Android apps, and large-scale backend services.

---

## What is Java?

Java lets you:
- Write robust, statically-typed, object-oriented code that runs on any platform via the JVM
- Build enterprise-grade backend systems, REST APIs, and microservices
- Develop Android mobile applications
- Build desktop GUI applications
- Work with files, databases, and external APIs
- Organize code using classes, interfaces, packages, and modules
- Handle multithreaded and concurrent workloads at scale
- Communicate over networks with sockets, RPC, and messaging systems
- Secure applications with encryption, authentication, and authorization
- Build, test, package, and deploy production-ready, cloud-native software
- Take advantage of a massive ecosystem of libraries, frameworks, and tooling (Maven/Gradle, Spring, Hibernate, JUnit, and more)

## Table of Contents

**Getting Started**  

1. **[Installing Java & First-Time Setup (JDK, JRE, JVM)](./[1]-Installation-and-Setup.md)**  
    1.1 JDK vs JRE vs JVM  
    1.2 Choosing a Java Version (LTS Releases)  
    1.3 Installing the JDK  
    1.4 Verifying Your Installation  
    1.5 Setting JAVA_HOME and PATH  
2. **[Running Code: javac, java, jshell REPL & IDEs](./[2]-Running-Java-Code.md)**  
    2.1 Compiling with javac  
    2.2 Running with java  
    2.3 Single-File Source-Code Programs  
    2.4 The jshell REPL  
    2.5 Using an IDE (IntelliJ, Eclipse, VS Code)  
3. **[How Java Works: Bytecode, the JVM & Classloading](./[3]-How-Java-Works.md)**  
    3.1 From Source Code to Bytecode  
    3.2 The Java Virtual Machine  
    3.3 Write Once, Run Anywhere  
    3.4 Classloading  
    3.5 JIT Compilation  
4. **[Build Tools & Dependency Management (Maven, Gradle)](./[4]-Build-Tools-Maven-and-Gradle.md)**  
    4.1 Why Build Tools?  
    4.2 Project Structure Conventions  
    4.3 Maven Basics (pom.xml)  
    4.4 Gradle Basics (build.gradle)  
    4.5 Choosing Between Maven and Gradle  

**Core Syntax**  

5. **[Variables & Basic Data Types (Primitives vs. Reference Types)](./[5]-Variables-and-Data-Types.md)**  
    5.1 Declaring Variables  
    5.2 Primitive Types  
    5.3 Reference Types  
    5.4 Type Inference with var  
    5.5 Naming Conventions  
6. **[Numbers, Strings, Characters & Booleans](./[6]-Numbers-Strings-and-Booleans.md)**  
    6.1 Integer Types in Depth  
    6.2 Floating-Point Types in Depth  
    6.3 The char Type  
    6.4 The String Type  
    6.5 The boolean Type  
7. **[Operators & Expressions (arithmetic, comparison, logical, bitwise, ternary)](./[7]-Operators-and-Expressions.md)**  
    7.1 Arithmetic Operators  
    7.2 Assignment Operators  
    7.3 Comparison Operators  
    7.4 Logical Operators  
    7.5 Bitwise Operators  
    7.6 The Ternary Operator  
    7.7 Operator Precedence  
8. **[Conditionals: if, else if, else, switch (incl. switch expressions)](./[8]-Conditionals.md)**  
    8.1 if, else if, else  
    8.2 switch Statements  
    8.3 Switch Expressions (Java 14+)  
    8.4 Pattern Matching in switch  
9. **[Loops: for, while, do-while, enhanced for, break, continue](./[9]-Loops.md)**  
    9.1 for Loops  
    9.2 while Loops  
    9.3 do-while Loops  
    9.4 Enhanced for Loop  
    9.5 break and continue  
    9.6 Nested Loops and Labels  
10. **[Methods & Scope (overloading, varargs, pass-by-value)](./[10]-Methods-and-Scope.md)**  
    10.1 Defining Methods  
    10.2 Parameters and Return Values  
    10.3 Method Overloading  
    10.4 Varargs  
    10.5 Pass-by-Value Semantics  
    10.6 Variable Scope  
11. **[String Formatting & Manipulation (String, StringBuilder, text blocks)](./[11]-String-Formatting.md)**  
    11.1 String Immutability  
    11.2 Common String Methods  
    11.3 StringBuilder  
    11.4 String Formatting (String.format, printf)  
    11.5 Text Blocks  
12. **[Arrays & Multidimensional Arrays](./[12]-Arrays.md)**  
    12.1 Declaring and Creating Arrays  
    12.2 Array Initialization  
    12.3 Iterating Arrays  
    12.4 Multidimensional Arrays  
    12.5 Arrays Utility Class  
13. **[Exception Handling: try, catch, finally, throw, custom exceptions](./[13]-Exception-Handling.md)**  
    13.1 What Are Exceptions?  
    13.2 try, catch, finally  
    13.3 Checked vs Unchecked Exceptions  
    13.4 throw and throws  
    13.5 Custom Exceptions  
    13.6 try-with-resources  

**Object-Oriented Programming**  

14. **[Classes & Objects](./[14]-OOP-Classes-and-Objects.md)**  
    14.1 What Is a Class?  
    14.2 Fields and Methods  
    14.3 Creating Objects  
    14.4 Instance vs Class Members  
    14.5 Object References  
15. **[Constructors & the `this` Keyword](./[15]-Constructors.md)**  
    15.1 Default Constructors  
    15.2 Parameterized Constructors  
    15.3 Constructor Overloading  
    15.4 The this Keyword  
    15.5 Constructor Chaining (this(...))  
16. **[Inheritance & Polymorphism (`extends`, method overriding)](./[16]-Inheritance-and-Polymorphism.md)**  
    16.1 The extends Keyword  
    16.2 Method Overriding  
    16.3 super Keyword  
    16.4 Polymorphism  
    16.5 The Object Superclass  
17. **[Encapsulation & Access Modifiers (public, private, protected, package-private)](./[17]-Encapsulation-and-Access-Modifiers.md)**  
    17.1 What Is Encapsulation?  
    17.2 public, private, protected, package-private  
    17.3 Getters and Setters  
    17.4 Access Modifiers on Classes  
    17.5 Best Practices  
18. **[Abstraction: Abstract Classes & Interfaces](./[18]-Abstraction-Interfaces-and-Abstract-Classes.md)**  
    18.1 What Is Abstraction?  
    18.2 Abstract Classes  
    18.3 Interfaces  
    18.4 Default and Static Interface Methods  
    18.5 Abstract Class vs Interface  
19. **[The Object Class & Overriding equals(), hashCode(), toString()](./[19]-The-Object-Class.md)**  
    19.1 Every Class Extends Object  
    19.2 toString()  
    19.3 equals()  
    19.4 hashCode()  
    19.5 The equals/hashCode Contract  
20. **[Static Members, Static Blocks & Final Keyword](./[20]-Static-and-Final.md)**  
    20.1 Static Fields  
    20.2 Static Methods  
    20.3 Static Initializer Blocks  
    20.4 The final Keyword  
    20.5 Static vs Instance Context  
21. **[Nested, Inner, Local & Anonymous Classes](./[21]-Nested-and-Inner-Classes.md)**  
    21.1 Static Nested Classes  
    21.2 Inner (Non-Static) Classes  
    21.3 Local Classes  
    21.4 Anonymous Classes  
    21.5 When to Use Each  
22. **[Enums](./[22]-Enums.md)**  
    22.1 What Is an Enum?  
    22.2 Enum Constants and Basic Usage  
    22.3 Enums with Fields, Constructors, and Methods  
    22.4 Enums with Constant-Specific Bodies  
    22.5 Enum Methods: values(), valueOf(), ordinal(), name()  
    22.6 Enums in Switch Statements  
    22.7 Implementing Interfaces with Enums  
23. **[Records](./[23]-Records.md)**  
    23.1 What Is a Record?  
    23.2 Declaring and Using Records  
    23.3 Compact Constructors  
    23.4 Custom Methods and Static Members  
    23.5 Records vs Traditional Classes  
    23.6 A Preview: Records and Pattern Matching  
24. **[Sealed Classes & Pattern Matching (instanceof, switch patterns)](./[24]-Sealed-Classes-and-Pattern-Matching.md)**  
    24.1 What Are Sealed Classes?  
    24.2 Declaring Sealed Classes and Interfaces  
    24.3 Permitted Subclasses: final, sealed, non-sealed  
    24.4 Pattern Matching for instanceof  
    24.5 Switch Expressions with Pattern Matching  
    24.6 Record Patterns in Switch  
    24.7 Sealed Classes and Pattern Matching Together  

**Generics & Type System**  
    25. **[Generics (generic classes, methods, bounded types, wildcards)](./[25]-Generics.md)**  
    26. **[Autoboxing, Unboxing & Wrapper Classes](./[26]-Autoboxing-and-Wrapper-Classes.md)**  

**Data Structures & Collections**  
    27. **[The Collections Framework Overview](./[27]-Collections-Framework-Overview.md)**  
    28. **[Lists (ArrayList, LinkedList)](./[28]-Lists.md)**  
    29. **[Sets (HashSet, LinkedHashSet, TreeSet)](./[29]-Sets.md)**  
    30. **[Maps (HashMap, LinkedHashMap, TreeMap)](./[30]-Maps.md)**  
    31. **[Queues, Deques & Stacks](./[31]-Queues-Deques-and-Stacks.md)**  
    32. **[Iterators & the Iterable Interface](./[32]-Iterators-and-Iterable.md)**  
    33. **[Comparable & Comparator](./[33]-Comparable-and-Comparator.md)**  

**Functional Programming & Streams**  
    34. **[Lambda Expressions & Functional Interfaces](./[34]-Lambda-Expressions.md)**  
    35. **[The Streams API (map, filter, reduce, collect)](./[35]-Streams-API.md)**  
    36. **[Method References & Optional](./[36]-Method-References-and-Optional.md)**  

**Advanced Language Features**  
    37. **[Annotations (built-in & custom)](./[37]-Annotations.md)**  
    38. **[Reflection API](./[38]-Reflection.md)**  
    39. **[The Java Module System (JPMS)](./[39]-Module-System.md)**  
    40. **[Garbage Collection & Memory Management](./[40]-Garbage-Collection-and-Memory-Management.md)**  
    41. **[Java Native Interface (JNI) & Foreign Function & Memory API (Project Panama)](./[41]-JNI-and-Foreign-Function-API.md)**  
    42. **[Java Version Highlights (Java 8 through Latest LTS)](./[42]-Java-Version-Highlights.md)**  

**Standard Library**  
    43. **[Working with Files & Paths (java.io, java.nio, Files, Path)](./[43]-Working-with-Files.md)**  
    44. **[Packages & Imports](./[44]-Packages-and-Imports.md)**  
    45. **[Working with Dates & Times (java.time: LocalDate, LocalDateTime, etc.)](./[45]-Dates-and-Times.md)**  
    46. **[Regular Expressions (java.util.regex)](./[46]-Regular-Expressions.md)**  
    47. **[Data Serialization (JSON with Jackson/Gson, Java Serialization, CSV, XML)](./[47]-Data-Serialization.md)**  
    48. **[Logging (java.util.logging, SLF4J, Log4j, Logback)](./[48]-Logging.md)**  
    49. **[Command-Line Tools & Arguments](./[49]-CLI-Tools.md)**  
    50. **[System & OS Operations (System, Runtime, ProcessBuilder)](./[50]-System-and-OS-Operations.md)**  
    51. **[Math & Random](./[51]-Math-and-Random.md)**  
    52. **[Internationalization & Localization (Locale, ResourceBundle, i18n)](./[52]-Internationalization-and-Localization.md)**  

**Concurrency**  
    53. **[Threads & the Runnable Interface](./[53]-Threads-and-Runnable.md)**  
    54. **[Synchronization, Locks & Thread Safety](./[54]-Synchronization-and-Locks.md)**  
    55. **[The Executor Framework & Thread Pools](./[55]-Executor-Framework.md)**  
    56. **[Concurrent Collections (java.util.concurrent)](./[56]-Concurrent-Collections.md)**  
    57. **[CompletableFuture & Asynchronous Programming](./[57]-CompletableFuture.md)**  
    58. **[Virtual Threads & Structured Concurrency (Project Loom)](./[58]-Virtual-Threads.md)**  

**Networking**  
    59. **[Sockets & Networking Basics (TCP/UDP)](./[59]-Sockets-and-Networking.md)**  
    60. **[HTTP Clients (java.net.http.HttpClient)](./[60]-HTTP-Clients.md)**  
    61. **[WebSockets in Java](./[61]-WebSockets.md)**  
    62. **[Remote Method Invocation (RMI) & gRPC Basics](./[62]-RMI-and-gRPC.md)**  

**Security**  
    63. **[Java Security Fundamentals (Security Manager legacy, sandboxing concepts)](./[63]-Security-Fundamentals.md)**  
    64. **[Cryptography (JCA/JCE): Hashing, Encryption & Digital Signatures](./[64]-Cryptography.md)**  
    65. **[Authentication & Authorization (JWT, OAuth2, Spring Security)](./[65]-Authentication-and-Authorization.md)**  
    66. **[Secure Coding Practices](./[66]-Secure-Coding-Practices.md)**  

**Databases**  
    67. **[JDBC Fundamentals (Connections, Statements, ResultSets)](./[67]-JDBC-Fundamentals.md)**  
    68. **[Connection Pooling & Working with SQLite/PostgreSQL/MySQL](./[68]-Connection-Pooling-and-Databases.md)**  
    69. **[JPA & Hibernate (ORM)](./[69]-JPA-and-Hibernate.md)**  
    70. **[MongoDB with the Java Driver](./[70]-MongoDB-Java-Driver.md)**  
    71. **[Caching with Redis (Jedis/Lettuce)](./[71]-Caching-with-Redis.md)**  

**Messaging & Event-Driven Systems**  
    72. **[Java Message Service (JMS)](./[72]-JMS.md)**  
    73. **[Apache Kafka with Java](./[73]-Apache-Kafka.md)**  
    74. **[RabbitMQ with Java (AMQP)](./[74]-RabbitMQ.md)**  

**Web Development**  
    75. **[Servlets & JSP Basics](./[75]-Servlets-and-JSP.md)**  
    76. **[Spring Framework & Spring Boot](./[76]-Spring-and-Spring-Boot.md)**  
    77. **[Building REST APIs with Spring Boot](./[77]-REST-APIs-with-Spring-Boot.md)**  
    78. **[API Documentation (OpenAPI/Swagger)](./[78]-API-Documentation-OpenAPI.md)**  
    79. **[GraphQL with Java (Spring GraphQL)](./[79]-GraphQL-with-Java.md)**  
    80. **[Working with APIs & JSON (HttpClient, RestTemplate/WebClient)](./[80]-APIs-and-JSON.md)**  
    81. **[Web Scraping with Jsoup](./[81]-Web-Scraping-Jsoup.md)**  
    82. **[Templating (Thymeleaf, JSP)](./[82]-Templating.md)**  

**Microservices & Cloud-Native Java**  
    83. **[Microservices Architecture Basics](./[83]-Microservices-Architecture.md)**  
    84. **[Docker Basics for Java Apps](./[84]-Docker-Basics.md)**  
    85. **[Kubernetes Fundamentals for Java Services](./[85]-Kubernetes-Fundamentals.md)**  
    86. **[GraalVM & Native Image Compilation](./[86]-GraalVM-and-Native-Image.md)**  
    87. **[Observability: Metrics, Tracing & Health Checks (Micrometer, Actuator)](./[87]-Observability.md)**  

**GUI Development**  
    88. **[Swing](./[88]-Swing.md)**  
    89. **[JavaFX](./[89]-JavaFX.md)**  

**Mobile Development**  
    90. **[Introduction to Android Development with Java](./[90]-Android-Development.md)**  

**Automation & Scripting**  
    91. **[File & Task Automation](./[91]-File-and-Task-Automation.md)**  
    92. **[Working with Excel (Apache POI)](./[92]-Excel-Automation-Apache-POI.md)**  
    93. **[Task Scheduling (Quartz Scheduler, ScheduledExecutorService)](./[93]-Task-Scheduling.md)**  

**Design Patterns & Architecture**  
    94. **[SOLID Principles](./[94]-SOLID-Principles.md)**  
    95. **[Creational Patterns (Singleton, Factory, Builder)](./[95]-Creational-Design-Patterns.md)**  
    96. **[Structural Patterns (Adapter, Decorator, Proxy)](./[96]-Structural-Design-Patterns.md)**  
    97. **[Behavioral Patterns (Observer, Strategy, Command)](./[97]-Behavioral-Design-Patterns.md)**  
    98. **[Architectural Patterns (MVC, Layered, Hexagonal/Clean Architecture)](./[98]-Architectural-Patterns.md)**  

**Testing & Code Quality**  
    99. **[Testing: JUnit & TestNG](./[99]-Testing-JUnit-and-TestNG.md)**  
    100. **[Mocking (Mockito)](./[100]-Mocking-Mockito.md)**  
    101. **[Integration Testing with Testcontainers](./[101]-Integration-Testing-Testcontainers.md)**  
    102. **[Linters & Formatters (Checkstyle, PMD, SpotBugs)](./[102]-Linters-and-Formatters.md)**  
    103. **[Code Coverage (JaCoCo)](./[103]-Code-Coverage.md)**  

**Packaging & Deployment**  
    104. **[Packaging & Distributing Your Code (JAR, WAR, fat JARs)](./[104]-Packaging-and-Distribution.md)**  
    105. **[Application Servers (Tomcat, Jetty, WildFly)](./[105]-Application-Servers.md)**  
    106. **[CI/CD Basics (GitHub Actions, Jenkins)](./[106]-CICD-Basics.md)**  

**Performance & Optimization**  
    107. **[Profiling (JVisualVM, JFR, async-profiler)](./[107]-Profiling.md)**  
    108. **[JVM Tuning & JIT Compilation](./[108]-JVM-Tuning-and-JIT.md)**  

**Best Practices**  
    109. **[Idiomatic Java & Code Conventions](./[109]-Best-Practices-and-Style.md)**