[⬅ Back to README](../../../README.md)

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
       1.1 What You're Installing  
       1.2 Installing rustup  
       1.3 Verifying the Install  
       1.4 Keeping Rust Up to Date  
    2. **[Running Code: cargo run, cargo build & the Rust Toolchain](./[2]-Running-Rust-Code.md)**  
       2.1 Creating a New Project  
       2.2 Building With cargo build  
       2.3 Running With cargo run  
       2.4 Checking Code Without Building  
    3. **[Cargo & Dependency Management (Cargo.toml, crates.io)](./[3]-Cargo-and-Dependency-Management.md)**  
       3.1 Anatomy of Cargo.toml  
       3.2 Adding Dependencies  
       3.3 Semantic Versioning  
       3.4 Cargo.lock  
    4. **[Project Structure & Workspaces](./[4]-Project-Structure-and-Workspaces.md)**  
       4.1 Standard Layout  
       4.2 Binaries vs. Libraries  
       4.3 Multiple Binaries  
       4.4 Workspaces  

**Core Syntax**  
    5. **[Variables, Mutability & Basic Data Types](./[5]-Variables-Mutability-and-Data-Types.md)**  
       5.1 Variables Are Immutable by Default  
       5.2 Shadowing  
       5.3 Constants  
       5.4 Basic Data Types Overview  
    6. **[Numbers, Strings & Booleans](./[6]-Numbers-Strings-and-Booleans.md)**  
       6.1 Integer Types  
       6.2 Floating-Point Types  
       6.3 Booleans and Characters  
       6.4 Strings: A First Look  
    7. **[Operators & Expressions](./[7]-Operators-and-Expressions.md)**  
       7.1 Arithmetic and Comparison Operators  
       7.2 Compound Assignment  
       7.3 Everything Is an Expression  
       7.4 Statements vs. Expressions  
    8. **[Conditionals: if, else if, else, if let](./[8]-Conditionals.md)**  
       8.1 Basic if / else  
       8.2 else if Chains  
       8.3 if as an Expression  
       8.4 if let for Pattern Matching  
    9. **[Loops: loop, while, for, break with values](./[9]-Loops.md)**  
       9.1 The loop Keyword  
       9.2 break With a Value  
       9.3 while Loops  
       9.4 for Loops and Iterators  
       9.5 Loop Labels  
    10. **[Functions & Scope](./[10]-Functions-and-Scope.md)**  
        10.1 Defining Functions  
        10.2 Return Values  
        10.3 Scope and Blocks  
        10.4 Parameters vs. Arguments  
    11. **[String Formatting & Manipulation (String vs. &str, format!, slicing)](./[11]-String-Formatting.md)**  
        11.1 String vs. &str, Revisited  
        11.2 The format! Macro and println!  
        11.3 Common String Methods  
        11.4 Slicing Strings Safely  
    12. **[Comments & Documentation (doc comments, rustdoc)](./[12]-Comments-and-Documentation.md)**  
        12.1 Regular Comments  
        12.2 Doc Comments  
        12.3 Generating Docs With rustdoc  
        12.4 Doc-Tests  

**Ownership & Memory Safety**  
    13. **[Ownership Rules](./[13]-Ownership-Rules.md)**  
        13.1 Why Ownership Exists  
        13.2 Move Semantics  
        13.3 Ownership and Functions  
        13.4 Ownership and Drop  
    14. **[Borrowing & References](./[14]-Borrowing-and-References.md)**  
        14.1 References: Access Without Ownership  
        14.2 Mutable References  
        14.3 The Borrowing Rules  
        14.4 Dangling References  
    15. **[The Slice Type](./[15]-The-Slice-Type.md)**  
        15.1 What Is a Slice?  
        15.2 String Slices (&str)  
        15.3 Array and Vector Slices  
        15.4 Why Slices Matter for Safety  
    16. **[Lifetimes](./[16]-Lifetimes.md)**  
        16.1 What Lifetimes Describe  
        16.2 Lifetime Annotation Syntax  
        16.3 Lifetimes in Structs  
        16.4 Lifetime Elision  
        16.5 The 'static Lifetime  
    17. **[The Stack, the Heap & Copy vs. Move Semantics](./[17]-Stack-Heap-and-Copy-vs-Move.md)**  
        17.1 The Stack  
        17.2 The Heap  
        17.3 Why Some Types Copy and Others Move  
        17.4 Explicit Clones  

**Data Structures**  
    18. **[Structs](./[18]-Structs.md)**  
        18.1 Defining and Instantiating Structs  
        18.2 Field Init Shorthand and Update Syntax  
        18.3 Tuple Structs and Unit Structs  
        18.4 Methods With impl  
    19. **[Enums & Pattern Matching](./[19]-Enums-and-Pattern-Matching.md)**  
        19.1 Defining Enums  
        19.2 Enums With Data  
        19.3 Pattern Matching Basics  
        19.4 Option: Rust's Answer to Null  
    20. **[The `match` Expression & `if let`/`while let`](./[20]-Match-Expression.md)**  
        20.1 match Basics  
        20.2 Matching and Binding Values  
        20.3 Match Guards  
        20.4 if let and while let  
    21. **[Collections: Vec, HashMap, HashSet, VecDeque](./[21]-Collections.md)**  
        21.1 Vec: Growable Lists  
        21.2 HashMap: Key-Value Storage  
        21.3 HashSet: Unique Values  
        21.4 VecDeque: Double-Ended Queue  
    22. **[Option & Result: Handling Absence & Errors](./[22]-Option-and-Result.md)**  
        22.1 Option Recap  
        22.2 Working With Option  
        22.3 The Result Type  
        22.4 Handling Results  

**Error Handling**  
    23. **[The Result Type & the `?` Operator](./[23]-Result-Type-and-the-Question-Mark-Operator.md)**  
        23.1 Propagating Errors Manually  
        23.2 The ? Operator  
        23.3 ? and Error Conversion  
        23.4 ? in main  
    24. **[Panic & Unwinding](./[24]-Panic-and-Unwinding.md)**  
        24.1 What a Panic Is  
        24.2 Unwinding vs. Aborting  
        24.3 Catching Panics  
        24.4 When to Panic vs. Return Result  
    25. **[Custom Error Types & Error Handling Crates (thiserror, anyhow)](./[25]-Custom-Error-Types.md)**  
        25.1 Writing a Custom Error Enum  
        25.2 thiserror: Less Boilerplate  
        25.3 anyhow: Flexible Application Errors  
        25.4 Choosing Between Them  

**Traits & Generics**  
    26. **[Traits & Trait Bounds](./[26]-Traits-and-Trait-Bounds.md)**  
        26.1 Defining and Implementing a Trait  
        26.2 Default Implementations  
        26.3 Traits as Parameters  
        26.4 Multiple Trait Bounds  
        26.5 Returning Types That Implement a Trait  
    27. **[Generics](./[27]-Generics.md)**  
        27.1 Why Generics?  
        27.2 Generic Structs and Enums  
        27.3 Generic Methods  
        27.4 Zero-Cost Abstraction  
    28. **[Default Trait Implementations & Trait Objects (`dyn`)](./[28]-Trait-Objects.md)**  
        28.1 Default Implementations, Revisited  
        28.2 The Problem Trait Objects Solve  
        28.3 dyn Trait  
        28.4 Static vs. Dynamic Dispatch  
    29. **[Common Standard Traits (Clone, Copy, Debug, PartialEq, Iterator)](./[29]-Common-Standard-Traits.md)**  
        29.1 Deriving Traits  
        29.2 Debug and Display  
        29.3 Clone and Copy, Revisited  
        29.4 PartialEq and Eq  
        29.5 The Iterator Trait  
    30. **[Operator Overloading (std::ops)](./[30]-Operator-Overloading.md)**  
        30.1 Operators Are Traits  
        30.2 Implementing Add  
        30.3 Other Common Operator Traits  
        30.4 When to Overload Operators  

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
