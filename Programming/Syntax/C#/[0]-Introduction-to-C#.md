[⬅ Back to README](../../../README.md)

# C#

Welcome! This is a self-paced course for learning C#, a modern, object-oriented language from Microsoft used for web apps, desktop software, games, cloud services, and more via the .NET platform.

---

## What is C#?

C# lets you:
- Write strongly-typed, object-oriented code with modern syntax
- Build web APIs and full-stack apps with ASP.NET Core
- Create desktop applications with WPF, WinForms, or MAUI
- Develop games using the Unity engine
- Query data expressively with LINQ
- Work with async/await for scalable, non-blocking code
- Access databases through Entity Framework Core
- Build cross-platform apps that run on Windows, macOS, and Linux
- Package, test, and deploy production-ready cloud-native services
- Interoperate with other languages and native code

## Table of Contents

**Getting Started**  
    1. **[Installing .NET SDK & First-Time Setup](./[1]-Installation-and-Setup.md)**  
       1.1 What is the .NET SDK?  
       1.2 Installing the .NET SDK  
       1.3 Verifying Your Installation  
       1.4 Choosing an Editor  
    2. **[Running Code: `dotnet run`, the REPL (csharp), and Visual Studio](./[2]-Running-CSharp-Code.md)**  
       2.1 Creating and Running Your First Program  
       2.2 The C# REPL  
       2.3 Running Code in an Editor/IDE  
    3. **[Projects & Solutions (.csproj, .sln, NuGet)](./[3]-Projects-and-Solutions.md)**  
       3.1 What is a .csproj File?  
       3.2 Solutions (.sln)  
       3.3 NuGet Packages  
       3.4 Common `dotnet` CLI Commands  
    4. **[Configuration & Environment (appsettings.json, environment variables)](./[4]-Configuration-and-Environment.md)**  
       4.1 appsettings.json  
       4.2 Environment Variables  
       4.3 Environments (Development, Staging, Production)  

**Core Syntax**  
    5. **[Variables & Basic Data Types](./[5]-Variables-and-Data-Types.md)**  
       5.1 Declaring Variables  
       5.2 Type Inference with `var`  
       5.3 Value Types vs Reference Types  
       5.4 Constants  
    6. **[Numbers, Strings & Booleans](./[6]-Numbers-Strings-and-Booleans.md)**  
       6.1 Numeric Types  
       6.2 Strings  
       6.3 Booleans  
       6.4 Type Conversion  
    7. **[Operators & Expressions (arithmetic, comparison, logical, null-coalescing)](./[7]-Operators-and-Expressions.md)**  
       7.1 Arithmetic Operators  
       7.2 Comparison Operators  
       7.3 Logical Operators  
       7.4 Null-Coalescing Operators  
    8. **[Conditionals: if, else, switch expressions](./[8]-Conditionals.md)**  
       8.1 if / else  
       8.2 else if Chains  
       8.3 switch Statements  
       8.4 switch Expressions  
    9. **[Loops: for, foreach, while, do-while, break, continue](./[9]-Loops.md)**  
       9.1 for Loop  
       9.2 while and do-while  
       9.3 foreach  
       9.4 break and continue  
    10. **[Methods & Parameters (ref, out, params, overloading)](./[10]-Methods-and-Parameters.md)**  
        10.1 Defining Methods  
        10.2 Parameters and Return Values  
        10.3 ref, out, and params  
        10.4 Method Overloading  
    11. **[String Formatting & Manipulation (interpolation, `string` methods)](./[11]-String-Formatting.md)**  
        11.1 String Interpolation  
        11.2 Common String Methods  
        11.3 StringBuilder  
    12. **[Exception Handling: try, catch, finally, custom exceptions](./[12]-Exception-Handling.md)**  
        12.1 try / catch / finally  
        12.2 Common Exception Types  
        12.3 Custom Exceptions  
        12.4 Best Practices  

**Data Structures**  
    13. **[Arrays & Lists](./[13]-Arrays-and-Lists.md)**  
        13.1 Arrays  
        13.2 List\<T\>  
        13.3 Common Operations  
    14. **[Dictionaries & Sets](./[14]-Dictionaries-and-Sets.md)**  
        14.1 Dictionary\<TKey, TValue\>  
        14.2 HashSet\<T\>  
        14.3 Choosing the Right Collection  
    15. **[Collection Interfaces (IEnumerable, ICollection, IList)](./[15]-Collection-Interfaces.md)**  
        15.1 IEnumerable\<T\>  
        15.2 ICollection\<T\>  
        15.3 IList\<T\>  
    16. **[Tuples & Records](./[16]-Tuples-and-Records.md)**  
        16.1 Tuples  
        16.2 Records  
        16.3 Records vs Classes  

**Object-Oriented Programming**  
    17. **[Classes & Objects](./[17]-OOP-Classes-and-Objects.md)**  
        17.1 Defining a Class  
        17.2 Creating Objects  
        17.3 Constructors  
        17.4 Fields vs Properties  
    18. **[Inheritance & Polymorphism](./[18]-Inheritance-and-Polymorphism.md)**  
        18.1 Inheritance Basics  
        18.2 Overriding Methods  
        18.3 Polymorphism  
        18.4 The base Keyword  
    19. **[Encapsulation & Access Modifiers](./[19]-Encapsulation-and-Access-Modifiers.md)**  
        19.1 What is Encapsulation?  
        19.2 Access Modifiers  
        19.3 Encapsulation in Practice  
    20. **[Interfaces & Abstract Classes](./[20]-Interfaces-and-Abstract-Classes.md)**  
        20.1 Interfaces  
        20.2 Abstract Classes  
        20.3 Interfaces vs Abstract Classes  
    21. **[Properties, Indexers & Operator Overloading](./[21]-Properties-and-Indexers.md)**  
        21.1 Properties  
        21.2 Indexers  
        21.3 Operator Overloading  
    22. **[Generics](./[22]-Generics.md)**  
        22.1 Why Generics?  
        22.2 Generic Classes  
        22.3 Generic Methods  
        22.4 Constraints  
    23. **[Structs vs Classes (value vs reference types)](./[23]-Structs-vs-Classes.md)**  
        23.1 What is a Struct?  
        23.2 Value vs Reference Semantics  
        23.3 When to Use Which  

**Advanced Language Features**  
    24. **[Delegates, Events & Lambda Expressions](./[24]-Delegates-Events-and-Lambdas.md)**  
    25. **[LINQ (query syntax, method syntax, deferred execution)](./[25]-LINQ.md)**  
    26. **[Nullable Reference Types & Null Safety](./[26]-Nullable-Reference-Types.md)**  
    27. **[Pattern Matching (`switch` expressions, `is`, deconstruction)](./[27]-Pattern-Matching.md)**  
    28. **[Extension Methods](./[28]-Extension-Methods.md)**  
    29. **[Attributes & Reflection](./[29]-Attributes-and-Reflection.md)**  
    30. **[Iterators & `yield`](./[30]-Iterators-and-Yield.md)**  
    31. **[Memory Management & Garbage Collection](./[31]-Memory-Management.md)**  
    32. **[`Span<T>`, `Memory<T>` & Performance-Oriented Types](./[32]-Span-and-Memory.md)**  

**Concurrency & Asynchrony**  
    33. **[Threading & the Task Parallel Library](./[33]-Threading-and-TPL.md)**  
    34. **[Async/Await Deep Dive](./[34]-Async-Await.md)**  
    35. **[Channels & Concurrent Collections](./[35]-Channels-and-Concurrent-Collections.md)**  

**Standard Library**  
    36. **[Working with Files (System.IO, streams)](./[36]-Working-with-Files.md)**  
    37. **[Namespaces & Assemblies](./[37]-Namespaces-and-Assemblies.md)**  
    38. **[Working with Dates & Times (DateTime, DateOnly, TimeSpan)](./[38]-Dates-and-Times.md)**  
    39. **[Regular Expressions](./[39]-Regular-Expressions.md)**  
    40. **[Data Serialization (System.Text.Json, XML)](./[40]-Data-Serialization.md)**  
    41. **[Logging (ILogger, Serilog)](./[41]-Logging.md)**  
    42. **[Command-Line Tools (System.CommandLine)](./[42]-CLI-Tools.md)**  

**Networking**  
    43. **[HTTP Clients (HttpClient)](./[43]-HTTP-Clients.md)**  
    44. **[Working with APIs & JSON](./[44]-APIs-and-JSON.md)**  
    45. **[SignalR & Real-Time Communication](./[45]-SignalR.md)**  
    46. **[gRPC with C#](./[46]-gRPC.md)**  

**Security**  
    47. **[Hashing & Encryption (System.Security.Cryptography)](./[47]-Hashing-and-Encryption.md)**  
    48. **[Authentication & Authorization (JWT, OAuth2, Identity)](./[48]-Authentication-and-Authorization.md)**  
    49. **[Secure Coding Practices](./[49]-Secure-Coding-Practices.md)**  

**Web Development**  
    50. **[ASP.NET Core Fundamentals](./[50]-ASP.NET-Core-Fundamentals.md)**  
    51. **[Minimal APIs & Controllers](./[51]-Minimal-APIs-and-Controllers.md)**  
    52. **[Razor Pages & Blazor](./[52]-Razor-Pages-and-Blazor.md)**  
    53. **[Middleware & Dependency Injection](./[53]-Middleware-and-Dependency-Injection.md)**  
    54. **[API Documentation (Swagger/OpenAPI)](./[54]-API-Documentation-OpenAPI.md)**  

**Desktop & Cross-Platform**  
    55. **[WPF & WinForms Basics](./[55]-WPF-and-WinForms.md)**  
    56. **[.NET MAUI for Cross-Platform Apps](./[56]-DotNET-MAUI.md)**  

**Databases**  
    57. **[ADO.NET Basics](./[57]-ADO.NET-Basics.md)**  
    58. **[Entity Framework Core (ORM)](./[58]-Entity-Framework-Core.md)**  
    59. **[Connecting to SQL Server, PostgreSQL & MySQL](./[59]-SQL-Server-PostgreSQL-MySQL.md)**  
    60. **[Caching with Redis](./[60]-Caching-with-Redis.md)**  

**Design Patterns & Architecture**  
    61. **[SOLID Principles in C#](./[61]-SOLID-Principles.md)**  
    62. **[Creational, Structural & Behavioral Patterns](./[62]-Design-Patterns.md)**  
    63. **[Dependency Injection & IoC Containers](./[63]-Dependency-Injection-and-IoC.md)**  
    64. **[Clean Architecture & Layered Design](./[64]-Clean-Architecture.md)**  

**Testing & Code Quality**  
    65. **[Unit Testing (xUnit, NUnit, MSTest)](./[65]-Unit-Testing.md)**  
    66. **[Mocking (Moq, NSubstitute)](./[66]-Mocking.md)**  
    67. **[Integration Testing](./[67]-Integration-Testing.md)**  
    68. **[Code Analysis & Style (EditorConfig, Roslyn Analyzers)](./[68]-Code-Analysis-and-Style.md)**  

**Packaging & Deployment**  
    69. **[NuGet Packaging & Publishing](./[69]-NuGet-Packaging.md)**  
    70. **[Docker Basics for .NET Apps](./[70]-Docker-Basics.md)**  
    71. **[CI/CD Basics (GitHub Actions, Azure DevOps)](./[71]-CICD-Basics.md)**  
    72. **[Observability: Metrics, Tracing & Health Checks](./[72]-Observability.md)**  

**Performance & Optimization**  
    73. **[Profiling & Benchmarking (BenchmarkDotNet)](./[73]-Profiling-and-Benchmarking.md)**  
    74. **[Unsafe Code & Pointers](./[74]-Unsafe-Code-and-Pointers.md)**  

**Best Practices**  
    75. **[C# Style & .NET Design Guidelines](./[75]-Best-Practices-and-Style.md)**