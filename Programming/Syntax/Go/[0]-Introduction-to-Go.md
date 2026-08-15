# Go

Welcome! This is a self-paced course for learning Go (Golang), the statically-typed, compiled language from Google known for its simplicity, fast compilation, and first-class support for concurrency — widely used for backend services, cloud infrastructure, and CLI tools.

---

## What is Go?

Go lets you:
- Write simple, statically-typed, compiled code that runs blazingly fast
- Build highly concurrent programs with goroutines and channels
- Build backend services, REST/gRPC APIs, and microservices
- Build cloud-native infrastructure tools (Docker, Kubernetes, Terraform are written in Go)
- Work with files, databases, and external APIs
- Organize code using packages and modules
- Compile to a single, dependency-free binary for easy deployment
- Build command-line tools and system utilities
- Take advantage of a growing ecosystem of frameworks and libraries (Gin, Echo, GORM, Cobra)

## Table of Contents

**Getting Started**  
    1. **[Installing Go & First-Time Setup](./[1]-Installation-and-Setup.md)**  
    2. **[Running Code: go run, go build & the Go Toolchain](./[2]-Running-Go-Code.md)**  
    3. **[Go Modules & Dependency Management (go.mod, go.sum)](./[3]-Go-Modules-and-Dependency-Management.md)**  
    4. **[Workspace Layout & Project Structure](./[4]-Workspace-Layout-and-Project-Structure.md)**  

**Core Syntax**  
    5. **[Variables & Basic Data Types](./[5]-Variables-and-Data-Types.md)**  
    6. **[Constants & iota](./[6]-Constants-and-iota.md)**  
    7. **[Operators & Expressions](./[7]-Operators-and-Expressions.md)**  
    8. **[Conditionals: if, else, switch](./[8]-Conditionals.md)**  
    9. **[Loops: for (Go's only loop construct), range](./[9]-Loops.md)**  
    10. **[Functions, Multiple Return Values & Named Returns](./[10]-Functions.md)**  
    11. **[Variadic Functions & Closures](./[11]-Variadic-Functions-and-Closures.md)**  
    12. **[String Formatting & Manipulation (fmt, strings, strconv)](./[12]-String-Formatting.md)**  
    13. **[Error Handling (the `error` type, wrapping, `errors.Is`/`As`)](./[13]-Error-Handling.md)**  
    14. **[Panic, Recover & Defer](./[14]-Panic-Recover-and-Defer.md)**  

**Data Structures**  
    15. **[Arrays & Slices](./[15]-Arrays-and-Slices.md)**  
    16. **[Maps](./[16]-Maps.md)**  
    17. **[Structs](./[17]-Structs.md)**  
    18. **[Pointers](./[18]-Pointers.md)**  

**Interfaces & Composition**  
    19. **[Interfaces & Structural Typing](./[19]-Interfaces.md)**  
    20. **[Embedding & Composition (Go's alternative to inheritance)](./[20]-Embedding-and-Composition.md)**  
    21. **[Methods & Method Sets (value vs. pointer receivers)](./[21]-Methods-and-Method-Sets.md)**  
    22. **[The Empty Interface & Type Assertions/Switches](./[22]-Empty-Interface-and-Type-Assertions.md)**  

**Generics**  
    23. **[Generics: Type Parameters & Constraints](./[23]-Generics.md)**  
    24. **[The `constraints` & `slices`/`maps` Packages](./[24]-Constraints-Slices-and-Maps-Packages.md)**  

**Concurrency**  
    25. **[Goroutines](./[25]-Goroutines.md)**  
    26. **[Channels (unbuffered, buffered, directional)](./[26]-Channels.md)**  
    27. **[Select Statement](./[27]-Select-Statement.md)**  
    28. **[sync Package (Mutex, WaitGroup, Once)](./[28]-Sync-Package.md)**  
    29. **[Context Package (cancellation, timeouts, values)](./[29]-Context-Package.md)**  
    30. **[Concurrency Patterns (worker pools, fan-in/fan-out, pipelines)](./[30]-Concurrency-Patterns.md)**  
    31. **[The Race Detector & Common Pitfalls](./[31]-Race-Detector-and-Common-Pitfalls.md)**  

**Standard Library**  
    32. **[Working with Files (os, io, bufio)](./[32]-Working-with-Files.md)**  
    33. **[Packages & Visibility (exported vs. unexported)](./[33]-Packages-and-Visibility.md)**  
    34. **[Working with Dates & Times (time package)](./[34]-Dates-and-Times.md)**  
    35. **[Regular Expressions (regexp)](./[35]-Regular-Expressions.md)**  
    36. **[Data Serialization (encoding/json, encoding/xml, encoding/csv)](./[36]-Data-Serialization.md)**  
    37. **[Logging (log, log/slog)](./[37]-Logging.md)**  
    38. **[Command-Line Tools (flag package, Cobra, Viper)](./[38]-CLI-Tools.md)**  
    39. **[System & OS Operations (os, os/exec)](./[39]-System-and-OS-Operations.md)**  
    40. **[Math & Random](./[40]-Math-and-Random.md)**  
    41. **[Reflection (reflect package)](./[41]-Reflection.md)**  

**Networking**  
    42. **[net/http: Building HTTP Servers & Clients](./[42]-net-http.md)**  
    43. **[Sockets & Low-Level Networking (net package)](./[43]-Sockets-and-Low-Level-Networking.md)**  
    44. **[WebSockets in Go (gorilla/websocket)](./[44]-WebSockets.md)**  
    45. **[gRPC & Protocol Buffers with Go](./[45]-gRPC-and-Protocol-Buffers.md)**  

**Security**  
    46. **[Hashing & Encryption (crypto package)](./[46]-Hashing-and-Encryption.md)**  
    47. **[Authentication & Authorization (JWT, OAuth2)](./[47]-Authentication-and-Authorization.md)**  
    48. **[Secure Coding Practices](./[48]-Secure-Coding-Practices.md)**  

**Databases**  
    49. **[database/sql Fundamentals](./[49]-database-sql-Fundamentals.md)**  
    50. **[Working with PostgreSQL & MySQL (pgx, go-sql-driver)](./[50]-PostgreSQL-and-MySQL.md)**  
    51. **[ORMs & Query Builders (GORM, sqlx, sqlc)](./[51]-ORMs-and-Query-Builders.md)**  
    52. **[MongoDB with the Go Driver](./[52]-MongoDB-Go-Driver.md)**  
    53. **[Caching with Redis (go-redis)](./[53]-Caching-with-Redis.md)**  

**Messaging & Event-Driven Systems**  
    54. **[Apache Kafka with Go (Sarama, kafka-go)](./[54]-Apache-Kafka.md)**  
    55. **[RabbitMQ with Go (amqp091-go)](./[55]-RabbitMQ.md)**  
    56. **[NATS Messaging](./[56]-NATS-Messaging.md)**  

**Web Development**  
    57. **[Building REST APIs with net/http & Routers (chi, gorilla/mux)](./[57]-REST-APIs.md)**  
    58. **[Gin Framework](./[58]-Gin-Framework.md)**  
    59. **[Echo Framework](./[59]-Echo-Framework.md)**  
    60. **[Fiber Framework](./[60]-Fiber-Framework.md)**  
    61. **[API Documentation (OpenAPI/Swagger with Swaggo)](./[61]-API-Documentation-OpenAPI.md)**  
    62. **[GraphQL with Go (gqlgen)](./[62]-GraphQL-with-Go.md)**  
    63. **[Templating (html/template, text/template)](./[63]-Templating.md)**  
    64. **[Middleware Patterns](./[64]-Middleware-Patterns.md)**  

**Testing & Code Quality**  
    65. **[Testing: the testing Package & go test](./[65]-Testing.md)**  
    66. **[Table-Driven Tests](./[66]-Table-Driven-Tests.md)**  
    67. **[Mocking (testify, gomock)](./[67]-Mocking.md)**  
    68. **[Benchmarking](./[68]-Benchmarking.md)**  
    69. **[Fuzzing](./[69]-Fuzzing.md)**  
    70. **[Linters & Formatters (gofmt, golangci-lint, go vet)](./[70]-Linters-and-Formatters.md)**  
    71. **[Code Coverage](./[71]-Code-Coverage.md)**  

**Design Patterns & Architecture**  
    72. **[Idiomatic Go & Effective Go Principles](./[72]-Idiomatic-Go.md)**  
    73. **[Common Design Patterns in Go (functional options, dependency injection)](./[73]-Common-Design-Patterns.md)**  
    74. **[Clean/Hexagonal Architecture in Go](./[74]-Clean-Hexagonal-Architecture.md)**  

**Packaging & Deployment**  
    75. **[Building & Cross-Compiling Binaries](./[75]-Building-and-Cross-Compiling.md)**  
    76. **[Docker Basics for Go Apps](./[76]-Docker-Basics.md)**  
    77. **[Kubernetes Fundamentals for Go Services](./[77]-Kubernetes-Fundamentals.md)**  
    78. **[CI/CD Basics (GitHub Actions)](./[78]-CICD-Basics.md)**  
    79. **[Observability: Metrics, Tracing & Health Checks (Prometheus, OpenTelemetry)](./[79]-Observability.md)**  

**Performance & Optimization**  
    80. **[Profiling (pprof)](./[80]-Profiling.md)**  
    81. **[Memory Management & Garbage Collection](./[81]-Memory-Management-and-Garbage-Collection.md)**  
    82. **[Performance Optimization Techniques](./[82]-Performance-Optimization-Techniques.md)**  

**Best Practices**  
    83. **[Effective Go & Idiomatic Style](./[83]-Best-Practices-and-Style.md)**
