[⬅ Back to README](../../../README.md)

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
    1.1 Installing Go  
    1.2 Setting Your PATH  
    1.3 Understanding GOPATH and GOROOT  
    1.4 Choosing an Editor  
2. **[Running Code: go run, go build & the Go Toolchain](./[2]-Running-Go-Code.md)**  
    2.1 Your First Program  
    2.2 go run  
    2.3 go build  
    2.4 go install  
    2.5 Other Useful Toolchain Commands  
3. **[Go Modules & Dependency Management (go.mod, go.sum)](./[3]-Go-Modules-and-Dependency-Management.md)**  
    3.1 What Is a Module?  
    3.2 Adding Dependencies  
    3.3 Common Module Commands  
    3.4 Semantic Versioning  
4. **[Workspace Layout & Project Structure](./[4]-Workspace-Layout-and-Project-Structure.md)**  
    4.1 A Minimal Project  
    4.2 The Standard Layout for Larger Projects  
    4.3 The internal Package Convention  
    4.4 Multiple Binaries with cmd/  

**Core Syntax**  

5. **[Variables & Basic Data Types](./[5]-Variables-and-Data-Types.md)**  
    5.1 Declaring Variables  
    5.2 Zero Values  
    5.3 Basic Types  
    5.4 Type Conversion  
    5.5 Multiple Declarations  
6. **[Constants & iota](./[6]-Constants-and-iota.md)**  
    6.1 Declaring Constants  
    6.2 Untyped Constants  
    6.3 iota  
    6.4 iota with Expressions  
7. **[Operators & Expressions](./[7]-Operators-and-Expressions.md)**  
    7.1 Arithmetic Operators  
    7.2 Comparison and Logical Operators  
    7.3 Assignment Operators  
    7.4 Increment and Decrement  
    7.5 Bitwise Operators  
8. **[Conditionals: if, else, switch](./[8]-Conditionals.md)**  
    8.1 if / else  
    8.2 if with an Init Statement  
    8.3 switch  
    8.4 switch Without a Condition  
    8.5 fallthrough  
9. **[Loops: for (Go's only loop construct), range](./[9]-Loops.md)**  
    9.1 for Is Go's Only Loop  
    9.2 range  
    9.3 break and continue  
    9.4 Labeled Loops  
10. **[Functions, Multiple Return Values & Named Returns](./[10]-Functions.md)**  
    10.1 Declaring Functions  
    10.2 Multiple Return Values  
    10.3 Named Return Values  
    10.4 Functions as Values  
11. **[Variadic Functions & Closures](./[11]-Variadic-Functions-and-Closures.md)**  
    11.1 Variadic Functions  
    11.2 Anonymous Functions  
    11.3 Closures  
    11.4 Common Closure Pitfall: Loop Variables  
12. **[String Formatting & Manipulation (fmt, strings, strconv)](./[12]-String-Formatting.md)**  
    12.1 The fmt Package  
    12.2 The strings Package  
    12.3 The strconv Package  
    12.4 Strings Are UTF-8 Byte Sequences  
13. **[Error Handling (the `error` type, wrapping, `errors.Is`/`As`)](./[13]-Error-Handling.md)**  
    13.1 The error Interface  
    13.2 Creating Errors  
    13.3 Wrapping Errors  
    13.4 errors.Is and errors.As  
    13.5 Custom Error Types  
14. **[Panic, Recover & Defer](./[14]-Panic-Recover-and-Defer.md)**  
    14.1 defer  
    14.2 defer Order and Evaluation  
    14.3 panic  
    14.4 recover  
    14.5 When to Use Each  

**Data Structures**  

15. **[Arrays & Slices](./[15]-Arrays-and-Slices.md)**  
    15.1 Arrays  
    15.2 Slices  
    15.3 Appending and Growing  
    15.4 Slicing Syntax  
    15.5 len, cap, and Shared Backing Arrays  
16. **[Maps](./[16]-Maps.md)**  
    16.1 Declaring and Initializing Maps  
    16.2 Reading, Writing, and Deleting  
    16.3 The "Comma OK" Idiom  
    16.4 Iterating Over Maps  
    16.5 Maps as Sets  
17. **[Structs](./[17]-Structs.md)**  
    17.1 Defining and Creating Structs  
    17.2 Accessing and Modifying Fields  
    17.3 Nested Structs  
    17.4 Struct Comparison  
    17.5 Struct Tags  
18. **[Pointers](./[18]-Pointers.md)**  
    18.1 What Is a Pointer?  
    18.2 & and *  
    18.3 Pointers to Structs  
    18.4 Pointers and Function Arguments  
    18.5 nil Pointers  
    18.6 new vs Composite Literals  

**Interfaces & Composition**  

19. **[Interfaces & Structural Typing](./[19]-Interfaces.md)**  
    19.1 Defining an Interface  
    19.2 Structural (Implicit) Typing  
    19.3 Interfaces as Function Parameters  
    19.4 Small Interfaces Are Idiomatic  
    19.5 nil Interfaces  
20. **[Embedding & Composition (Go's alternative to inheritance)](./[20]-Embedding-and-Composition.md)**  
    20.1 Go Has No Inheritance  
    20.2 Struct Embedding  
    20.3 Overriding Promoted Methods  
    20.4 Interface Embedding  
    20.5 Composition Over Inheritance in Practice  
21. **[Methods & Method Sets (value vs. pointer receivers)](./[21]-Methods-and-Method-Sets.md)**  
    21.1 Declaring Methods  
    21.2 Value Receivers vs Pointer Receivers  
    21.3 Choosing a Receiver Type  
    21.4 Method Sets and Interface Satisfaction  
22. **[The Empty Interface & Type Assertions/Switches](./[22]-Empty-Interface-and-Type-Assertions.md)**  
    22.1 The Empty Interface  
    22.2 Type Assertions  
    22.3 Type Switches  
    22.4 When to Reach for any  

**Generics**  

23. **[Generics: Type Parameters & Constraints](./[23]-Generics.md)**  
    23.1 Why Generics?  
    23.2 Generic Functions  
    23.3 Constraints  
    23.4 Generic Types  
    23.5 Explicit Type Arguments  
24. **[The `constraints` & `slices`/`maps` Packages](./[24]-Constraints-Slices-and-Maps-Packages.md)**  
    24.1 golang.org/x/constraints  
    24.2 The slices Package  
    24.3 The maps Package  
    24.4 Why These Matter  

**Concurrency**  

25. **[Goroutines](./[25]-Goroutines.md)**  
    25.1 What Is a Goroutine?  
    25.2 The main Goroutine  
    25.3 Anonymous Goroutines and Capturing Variables  
    25.4 Goroutines Are Not Free  
26. **[Channels (unbuffered, buffered, directional)](./[26]-Channels.md)**  
    26.1 What Is a Channel?  
    26.2 Unbuffered Channels  
    26.3 Buffered Channels  
    26.4 Closing Channels  
    26.5 Ranging Over a Channel  
    26.6 Directional Channels  
27. **[Select Statement](./[27]-Select-Statement.md)**  
    27.1 What select Does  
    27.2 default: Non-Blocking Operations  
    27.3 Timeouts with select and time.After  
    27.4 Combining select with a Quit Channel  
    27.5 Empty select  
28. **[sync Package (Mutex, WaitGroup, Once)](./[28]-Sync-Package.md)**  
    28.1 sync.WaitGroup  
    28.2 sync.Mutex  
    28.3 sync.RWMutex  
    28.4 sync.Once  
    28.5 sync.Map  
29. **[Context Package (cancellation, timeouts, values)](./[29]-Context-Package.md)**  
    29.1 What context Is For  
    29.2 Creating Contexts  
    29.3 Cancellation  
    29.4 Timeouts and Deadlines  
    29.5 Passing Values  
    29.6 Convention: ctx as the First Parameter  
30. **[Concurrency Patterns (worker pools, fan-in/fan-out, pipelines)](./[30]-Concurrency-Patterns.md)**  
    30.1 Worker Pools  
    30.2 Fan-Out, Fan-In  
    30.3 Pipelines  
    30.4 Rate Limiting  
31. **[The Race Detector & Common Pitfalls](./[31]-Race-Detector-and-Common-Pitfalls.md)**  
    31.1 What Is a Data Race?  
    31.2 The -race Flag  
    31.3 Fixing Races  
    31.4 Common Pitfall: Goroutine Leaks  
    31.5 Common Pitfall: Deadlocks  
    31.6 Common Pitfall: Closing a Channel Twice, or from the Wrong Side  

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
