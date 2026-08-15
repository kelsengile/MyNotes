# Rust

Welcome! This is a self-paced course for learning Rust, the systems programming language built for performance, reliability, and memory safety — without a garbage collector — widely used for systems software, WebAssembly, embedded programming, and high-performance backend services.

---

## What is Rust?

Rust lets you:
- Write memory-safe, statically-typed code with no garbage collector
- Guarantee thread safety and eliminate data races at compile time
- Build systems software, CLI tools, and performance-critical applications
- Build backend services and REST/gRPC APIs
- Compile to WebAssembly for the browser
- Work with files, databases, and external APIs
- Organize code using modules, crates, and Cargo packages
- Interoperate with C/C++ through FFI
- Write embedded and low-level code with fine-grained control over memory

## Table of Contents

**Getting Started**  
    1. **[Installing Rust & First-Time Setup (rustup, rustc)](./[1]-Installation-and-Setup.md)**  
    2. **[Running Code: cargo run, cargo build & the Rust Toolchain](./[2]-Running-Rust-Code.md)**  
    3. **[Cargo & Dependency Management (Cargo.toml, crates.io)](./[3]-Cargo-and-Dependency-Management.md)**  
    4. **[Project Structure & Workspaces](./[4]-Project-Structure-and-Workspaces.md)**  

**Core Syntax**  
    5. **[Variables, Mutability & Basic Data Types](./[5]-Variables-Mutability-and-Data-Types.md)**  
    6. **[Numbers, Strings & Booleans](./[6]-Numbers-Strings-and-Booleans.md)**  
    7. **[Operators & Expressions](./[7]-Operators-and-Expressions.md)**  
    8. **[Conditionals: if, else if, else, if let](./[8]-Conditionals.md)**  
    9. **[Loops: loop, while, for, break with values](./[9]-Loops.md)**  
    10. **[Functions & Scope](./[10]-Functions-and-Scope.md)**  
    11. **[String Formatting & Manipulation (String vs. &str, format!, slicing)](./[11]-String-Formatting.md)**  
    12. **[Comments & Documentation (doc comments, rustdoc)](./[12]-Comments-and-Documentation.md)**  

**Ownership & Memory Safety**  
    13. **[Ownership Rules](./[13]-Ownership-Rules.md)**  
    14. **[Borrowing & References](./[14]-Borrowing-and-References.md)**  
    15. **[The Slice Type](./[15]-The-Slice-Type.md)**  
    16. **[Lifetimes](./[16]-Lifetimes.md)**  
    17. **[The Stack, the Heap & Copy vs. Move Semantics](./[17]-Stack-Heap-and-Copy-vs-Move.md)**  

**Data Structures**  
    18. **[Structs](./[18]-Structs.md)**  
    19. **[Enums & Pattern Matching](./[19]-Enums-and-Pattern-Matching.md)**  
    20. **[The `match` Expression & `if let`/`while let`](./[20]-Match-Expression.md)**  
    21. **[Collections: Vec, HashMap, HashSet, VecDeque](./[21]-Collections.md)**  
    22. **[Option & Result: Handling Absence & Errors](./[22]-Option-and-Result.md)**  

**Error Handling**  
    23. **[The Result Type & the `?` Operator](./[23]-Result-Type-and-the-Question-Mark-Operator.md)**  
    24. **[Panic & Unwinding](./[24]-Panic-and-Unwinding.md)**  
    25. **[Custom Error Types & Error Handling Crates (thiserror, anyhow)](./[25]-Custom-Error-Types.md)**  

**Traits & Generics**  
    26. **[Traits & Trait Bounds](./[26]-Traits-and-Trait-Bounds.md)**  
    27. **[Generics](./[27]-Generics.md)**  
    28. **[Default Trait Implementations & Trait Objects (`dyn`)](./[28]-Trait-Objects.md)**  
    29. **[Common Standard Traits (Clone, Copy, Debug, PartialEq, Iterator)](./[29]-Common-Standard-Traits.md)**  
    30. **[Operator Overloading (std::ops)](./[30]-Operator-Overloading.md)**  

**Modules & Project Organization**  
    31. **[Modules, Paths & Visibility (`mod`, `pub`, `use`)](./[31]-Modules-Paths-and-Visibility.md)**  
    32. **[Crates & Packages](./[32]-Crates-and-Packages.md)**  
    33. **[Publishing Crates to crates.io](./[33]-Publishing-Crates.md)**  

**Advanced Language Features**  
    34. **[Closures](./[34]-Closures.md)**  
    35. **[Iterators & the Iterator Trait](./[35]-Iterators.md)**  
    36. **[Smart Pointers (Box, Rc, Arc, RefCell)](./[36]-Smart-Pointers.md)**  
    37. **[Interior Mutability](./[37]-Interior-Mutability.md)**  
    38. **[Macros: Declarative (`macro_rules!`) & Procedural](./[38]-Macros.md)**  
    39. **[Unsafe Rust](./[39]-Unsafe-Rust.md)**  
    40. **[Foreign Function Interface (FFI) & Interop with C](./[40]-FFI-and-Interop-with-C.md)**  

**Concurrency**  
    41. **[Threads (std::thread)](./[41]-Threads.md)**  
    42. **[Message Passing with Channels (mpsc)](./[42]-Channels.md)**  
    43. **[Shared State Concurrency (Mutex, Arc, RwLock)](./[43]-Shared-State-Concurrency.md)**  
    44. **[Send & Sync Traits](./[44]-Send-and-Sync-Traits.md)**  
    45. **[Async/Await Fundamentals](./[45]-Async-Await-Fundamentals.md)**  
    46. **[The Tokio Runtime](./[46]-Tokio-Runtime.md)**  

**Standard Library**  
    47. **[Working with Files (std::fs, std::io)](./[47]-Working-with-Files.md)**  
    48. **[Working with Dates & Times (chrono crate)](./[48]-Dates-and-Times.md)**  
    49. **[Regular Expressions (regex crate)](./[49]-Regular-Expressions.md)**  
    50. **[Data Serialization (serde, serde_json)](./[50]-Data-Serialization.md)**  
    51. **[Logging (log, tracing crates)](./[51]-Logging.md)**  
    52. **[Command-Line Tools (clap, std::env::args)](./[52]-CLI-Tools.md)**  
    53. **[Environment & System Operations (std::env, std::process)](./[53]-Environment-and-System-Operations.md)**  

**Networking**  
    54. **[TCP/UDP Networking (std::net)](./[54]-TCP-UDP-Networking.md)**  
    55. **[HTTP Clients (reqwest)](./[55]-HTTP-Clients.md)**  
    56. **[WebSockets in Rust (tokio-tungstenite)](./[56]-WebSockets.md)**  
    57. **[gRPC & Protocol Buffers with Rust (tonic)](./[57]-gRPC-and-Protocol-Buffers.md)**  

**Security**  
    58. **[Hashing & Encryption (ring, RustCrypto)](./[58]-Hashing-and-Encryption.md)**  
    59. **[Authentication & Authorization (JWT, OAuth2)](./[59]-Authentication-and-Authorization.md)**  
    60. **[Secure & Safe Rust Practices](./[60]-Secure-Rust-Practices.md)**  

**Databases**  
    61. **[Working with SQL Databases (sqlx, diesel)](./[61]-SQL-Databases.md)**  
    62. **[PostgreSQL & MySQL with Rust](./[62]-PostgreSQL-and-MySQL.md)**  
    63. **[MongoDB with the Rust Driver](./[63]-MongoDB-Rust-Driver.md)**  
    64. **[Caching with Redis](./[64]-Caching-with-Redis.md)**  

**Web Development**  
    65. **[Actix Web Framework](./[65]-Actix-Web.md)**  
    66. **[Axum Framework](./[66]-Axum.md)**  
    67. **[Rocket Framework](./[67]-Rocket.md)**  
    68. **[Building REST APIs](./[68]-Building-REST-APIs.md)**  
    69. **[API Documentation (OpenAPI/Swagger with utoipa)](./[69]-API-Documentation-OpenAPI.md)**  
    70. **[Templating (Askama, Tera)](./[70]-Templating.md)**  

**Messaging & Event-Driven Systems**  
    71. **[Apache Kafka with Rust (rdkafka)](./[71]-Apache-Kafka.md)**  
    72. **[RabbitMQ with Rust (lapin)](./[72]-RabbitMQ.md)**  

**WebAssembly**  
    73. **[Compiling Rust to WebAssembly (wasm-pack, wasm-bindgen)](./[73]-Rust-to-WebAssembly.md)**  
    74. **[Interfacing Rust/WASM with JavaScript](./[74]-Interfacing-Rust-WASM-with-JavaScript.md)**  

**Testing & Code Quality**  
    75. **[Testing: Unit & Integration Tests](./[75]-Testing.md)**  
    76. **[Mocking (mockall)](./[76]-Mocking.md)**  
    77. **[Benchmarking (criterion)](./[77]-Benchmarking.md)**  
    78. **[Fuzzing (cargo-fuzz)](./[78]-Fuzzing.md)**  
    79. **[Linters & Formatters (clippy, rustfmt)](./[79]-Linters-and-Formatters.md)**  
    80. **[Code Coverage (cargo-tarpaulin, llvm-cov)](./[80]-Code-Coverage.md)**  

**Design Patterns & Architecture**  
    81. **[Idiomatic Rust Patterns (builder, newtype, typestate)](./[81]-Idiomatic-Rust-Patterns.md)**  
    82. **[Error Handling Architecture at Scale](./[82]-Error-Handling-Architecture.md)**  

**Packaging & Deployment**  
    83. **[Building Release Binaries & Cross-Compilation](./[83]-Building-Release-Binaries-and-Cross-Compilation.md)**  
    84. **[Docker Basics for Rust Apps](./[84]-Docker-Basics.md)**  
    85. **[CI/CD Basics (GitHub Actions)](./[85]-CICD-Basics.md)**  
    86. **[Observability: Metrics & Tracing (tracing, OpenTelemetry)](./[86]-Observability.md)**  

**Performance & Optimization**  
    87. **[Profiling (perf, flamegraph, cargo-instruments)](./[87]-Profiling.md)**  
    88. **[Zero-Cost Abstractions & Performance Tuning](./[88]-Zero-Cost-Abstractions-and-Performance-Tuning.md)**  

**Best Practices**  
    89. **[Idiomatic Rust & the Rust API Guidelines](./[89]-Best-Practices-and-Style.md)**
