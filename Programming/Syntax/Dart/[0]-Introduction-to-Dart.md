[⬅ Back to README](../../../README.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/dart/dart-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Dart</h1>
</div>

Welcome! This is a self-paced course for learning Dart, a modern, strongly typed, object-oriented language optimized for building fast apps on any platform: mobile, web, desktop, servers, and command-line tools. It is the language behind Flutter.

Download: [https://dart.dev/get-dart](https://dart.dev/get-dart)

---

## What is Dart?

Dart lets you:
- Write clean, readable, type-safe code with sound null safety
- Build cross-platform apps (iOS, Android, web, Windows, macOS, Linux) from one codebase
- Use a rich object-oriented model with classes, mixins, extensions, and generics
- Write asynchronous code naturally with `Future`, `Stream`, `async`, and `await`
- Run heavy work in parallel using isolates, Dart's memory-safe take on concurrency
- Use modern features like records, pattern matching, and sealed classes
- Enjoy fast development with hot reload (JIT) and fast production code (AOT compiled)
- Compile to native machine code, JavaScript, or WebAssembly
- Build command-line tools, backend servers, and scripts
- Share code through the `pub.dev` package ecosystem
- Interoperate with C (FFI), JavaScript, and platform-specific code
- Test, analyze, format, and document your code with built-in tooling

## Table of Contents

**Getting Started**  

1. **[Installing the Dart SDK](./[1]-Installation-and-Setup.md)**  
    1.1 What Is the Dart SDK?  
    1.2 Installing on Windows, macOS, and Linux  
    1.3 Dart vs Flutter: Which Do You Need?  
    1.4 Verifying Your Installation  
    1.5 Using DartPad in the Browser  
2. **[Your First Dart Program](./[2]-Your-First-Program.md)**  
    2.1 The `main()` Function  
    2.2 Printing with `print()`  
    2.3 Running with `dart run`  
    2.4 Creating a Project with `dart create`  
    2.5 Anatomy of a Dart Project  
3. **[Your Development Environment (VS Code, IntelliJ, analyzer, formatter)](./[3]-Development-Environment.md)**  
    3.1 Choosing an Editor or IDE  
    3.2 The Dart Analyzer and Linter  
    3.3 `analysis_options.yaml` and Lint Rules  
    3.4 `dart format` and Code Style  
    3.5 Debugging and DevTools  
4. **[Comments & Documentation](./[4]-Comments-and-Documentation.md)**  
    4.1 Single-Line and Multi-Line Comments  
    4.2 Documentation Comments (`///`)  
    4.3 Generating Docs with `dart doc`  

**Core Syntax**  

5. **[Variables & Type Inference (`var`, `final`, `const`, `late`)](./[5]-Variables-and-Type-Inference.md)**  
    5.1 What Is a Variable?  
    5.2 Declaring with `var` and Explicit Types  
    5.3 `final` vs `const`  
    5.4 `late` Variables and Lazy Initialization  
    5.5 `dynamic` vs `Object`  
    5.6 Naming Conventions  
6. **[Built-in Types: Numbers & Booleans](./[6]-Numbers-and-Booleans.md)**  
    6.1 `int` and `double`  
    6.2 `num`  
    6.3 Parsing and Converting Numbers  
    6.4 `bool` and Truthiness Rules  
    6.5 Number Methods and `dart:math`  
7. **[Strings & Text (interpolation, raw strings, multiline)](./[7]-Strings.md)**  
    7.1 Creating Strings  
    7.2 String Interpolation  
    7.3 Multiline and Raw Strings  
    7.4 Common String Methods  
    7.5 `StringBuffer`  
    7.6 Runes and Grapheme Clusters  
8. **[Null Safety (`?`, `!`, `??`, `?.`, `??=`)](./[8]-Null-Safety.md)**  
    8.1 Nullable vs Non-Nullable Types  
    8.2 The Null-Aware Operators  
    8.3 The Null Assertion Operator (`!`)  
    8.4 Type Promotion and Flow Analysis  
    8.5 `late` and Null Safety  
    8.6 Migrating from Legacy Code  
9. **[Operators & Expressions](./[9]-Operators-and-Expressions.md)**  
    9.1 Arithmetic Operators  
    9.2 Equality and Relational Operators  
    9.3 Logical Operators  
    9.4 Bitwise and Shift Operators  
    9.5 Assignment and Compound Assignment  
    9.6 Type Test Operators (`is`, `is!`, `as`)  
    9.7 The Cascade Operator (`..` and `?..`)  
    9.8 Operator Precedence  
10. **[Conditionals: if, else, switch](./[10]-Conditionals.md)**  
    10.1 `if` and `else`  
    10.2 The Ternary Operator  
    10.3 `switch` Statements  
    10.4 `switch` Expressions  
    10.5 `if-case` Statements  
11. **[Loops: for, while, do-while, for-in](./[11]-Loops.md)**  
    11.1 The `for` Loop  
    11.2 The `for-in` Loop  
    11.3 `while` and `do-while`  
    11.4 `break`, `continue`, and Labels  
    11.5 `forEach` and Functional Iteration  
12. **[Functions (parameters, return values, closures)](./[12]-Functions.md)**  
    12.1 Declaring and Calling Functions  
    12.2 Positional, Named, and Optional Parameters  
    12.3 `required` and Default Values  
    12.4 Arrow Syntax (`=>`)  
    12.5 Functions as First-Class Objects  
    12.6 Anonymous Functions and Closures  
    12.7 Lexical Scope  
    12.8 Recursion  
    12.9 Tear-offs  
13. **[Collections: Lists](./[13]-Lists.md)**  
    13.1 Creating Lists  
    13.2 Accessing and Modifying Elements  
    13.3 Fixed-Length vs Growable Lists  
    13.4 Common List Methods  
    13.5 Sorting, Searching, and Slicing  
    13.6 Multidimensional Lists  
14. **[Collections: Sets & Maps](./[14]-Sets-and-Maps.md)**  
    14.1 Sets and Uniqueness  
    14.2 Set Operations (union, intersection, difference)  
    14.3 Maps and Key-Value Pairs  
    14.4 Iterating over Maps  
    14.5 `LinkedHashMap`, `SplayTreeMap`, and Other Variants  
15. **[Collection Operators & Iterables](./[15]-Collection-Operators-and-Iterables.md)**  
    15.1 The Spread Operators (`...` and `...?`)  
    15.2 Collection `if` and Collection `for`  
    15.3 `Iterable` vs `List`  
    15.4 `map`, `where`, `reduce`, `fold`, `expand`  
    15.5 Lazy Evaluation  
    15.6 Unmodifiable and Immutable Collections  
16. **[Records](./[16]-Records.md)**  
    16.1 What Is a Record?  
    16.2 Positional and Named Fields  
    16.3 Returning Multiple Values from Functions  
    16.4 Record Types and Equality  
17. **[Patterns & Destructuring](./[17]-Patterns-and-Destructuring.md)**  
    17.1 What Are Patterns?  
    17.2 Destructuring Lists, Maps, and Records  
    17.3 Object Patterns  
    17.4 Logical, Relational, and Cast Patterns  
    17.5 Guard Clauses (`when`)  
    17.6 Exhaustiveness Checking  
18. **[Enums](./[18]-Enums.md)**  
    18.1 Simple Enums  
    18.2 Enhanced Enums with Fields and Methods  
    18.3 Enums with `switch`  
    18.4 Useful Enum Properties (`name`, `index`, `values`)  
19. **[Typedefs & Type Aliases](./[19]-Typedefs.md)**  
    19.1 Function Type Aliases  
    19.2 Generic Type Aliases  
    19.3 Using Aliases for Readability  

**Object-Oriented Dart**  

20. **[Classes & Objects](./[20]-Classes-and-Objects.md)**  
    20.1 Declaring a Class  
    20.2 Instance Variables and Methods  
    20.3 `this` and Instance Access  
    20.4 Static Members  
    20.5 Privacy and Libraries (`_` prefix)  
21. **[Constructors](./[21]-Constructors.md)**  
    21.1 Default and Generative Constructors  
    21.2 Initializing Formal Parameters (`this.x`)  
    21.3 Named Constructors  
    21.4 Initializer Lists  
    21.5 Redirecting Constructors  
    21.6 `const` Constructors  
    21.7 Factory Constructors  
    21.8 Super Parameters  
22. **[Getters, Setters & Operators](./[22]-Getters-Setters-Operators.md)**  
    22.1 Computed Properties with `get`  
    22.2 Custom `set` Logic  
    22.3 Overloading Operators  
    22.4 `toString`, `==`, and `hashCode`  
    22.5 Callable Classes (`call()`)  
23. **[Inheritance & Polymorphism](./[23]-Inheritance.md)**  
    23.1 `extends` and `super`  
    23.2 Overriding Methods (`@override`)  
    23.3 Abstract Classes and Methods  
    23.4 Implicit Interfaces and `implements`  
    23.5 `noSuchMethod`  
24. **[Mixins](./[24]-Mixins.md)**  
    24.1 What Is a Mixin?  
    24.2 `with` and `on`  
    24.3 Mixin Classes  
    24.4 Mixins vs Inheritance vs Interfaces  
25. **[Class Modifiers (`base`, `final`, `interface`, `sealed`)](./[25]-Class-Modifiers.md)**  
    25.1 Why Class Modifiers Exist  
    25.2 `abstract`, `base`, `interface`, and `final`  
    25.3 `sealed` Classes and Exhaustive Switching  
    25.4 Combining Modifiers  
26. **[Extension Methods & Extension Types](./[26]-Extensions.md)**  
    26.1 Adding Methods to Existing Types  
    26.2 Extensions on Generic and Nullable Types  
    26.3 Extension Types  
    26.4 Static Extension Pitfalls  
27. **[Generics](./[27]-Generics.md)**  
    27.1 Why Generics?  
    27.2 Generic Classes and Functions  
    27.3 Bounded Type Parameters (`extends`)  
    27.4 Generic Collections and Type Safety  
    27.5 Reified Generics and Runtime Types  
28. **[Object Equality, Immutability & Value Types](./[28]-Equality-and-Immutability.md)**  
    28.1 Identity vs Equality  
    28.2 Overriding `==` and `hashCode`  
    28.3 Designing Immutable Classes  
    28.4 `copyWith` Pattern  

**Error Handling**  

29. **[Exceptions & Error Handling](./[29]-Exceptions.md)**  
    29.1 `throw`  
    29.2 `try`, `catch`, `on`, and `finally`  
    29.3 `rethrow`  
    29.4 `Exception` vs `Error`  
    29.5 Creating Custom Exceptions  
30. **[Assertions & Defensive Programming](./[30]-Assertions.md)**  
    30.1 `assert` and Debug Mode  
    30.2 Validating Inputs  
    30.3 Result-Style Error Handling with Sealed Classes  

**Asynchronous Programming**  

31. **[Futures, `async` & `await`](./[31]-Futures-Async-Await.md)**  
    31.1 Synchronous vs Asynchronous Code  
    31.2 The `Future` Class  
    31.3 `async` and `await`  
    31.4 Error Handling in Async Code  
    31.5 `Future.wait`, `Future.any`, and Timeouts  
    31.6 Completers  
32. **[Streams](./[32]-Streams.md)**  
    32.1 What Is a Stream?  
    32.2 Single-Subscription vs Broadcast Streams  
    32.3 Listening and `await for`  
    32.4 `StreamController`  
    32.5 Transforming Streams  
33. **[Generators (`sync*`, `async*`, `yield`)](./[33]-Generators.md)**  
    33.1 Synchronous Generators  
    33.2 Asynchronous Generators  
    33.3 `yield*` Delegation  
34. **[The Event Loop & Zones](./[34]-Event-Loop-and-Zones.md)**  
    34.1 Microtasks vs Event Queue  
    34.2 How Dart Schedules Work  
    34.3 Timers and `Future.delayed`  
    34.4 Zones and Error Handling  
35. **[Isolates & Concurrency](./[35]-Isolates.md)**  
    35.1 Why Isolates Instead of Threads?  
    35.2 `Isolate.run`  
    35.3 Spawning Isolates and Message Passing  
    35.4 `SendPort` and `ReceivePort`  
    35.5 When to Use Isolates  

**Libraries & Packages**  

36. **[Libraries, Imports & Exports](./[36]-Libraries-and-Imports.md)**  
    36.1 `import` and `export`  
    36.2 Prefixes (`as`), `show`, and `hide`  
    36.3 `part` and `part of`  
    36.4 Deferred (Lazy) Loading  
    36.5 Library-Private Members  
37. **[Packages & `pub` (pubspec.yaml)](./[37]-Packages-and-Pub.md)**  
    37.1 Understanding `pubspec.yaml`  
    37.2 `dart pub add`, `get`, and `upgrade`  
    37.3 Versioning and Constraints  
    37.4 Finding Packages on pub.dev  
    37.5 Publishing Your Own Package  
38. **[Project Structure & Organization](./[38]-Project-Structure.md)**  
    38.1 `bin/`, `lib/`, `test/`, and `example/`  
    38.2 Public vs Private APIs (`src/`)  
    38.3 Monorepos and Workspaces  

**Core Libraries**  

39. **[`dart:core` Essentials](./[39]-Dart-Core.md)**  
    39.1 `Object`, `Comparable`, and `Iterable`  
    39.2 `DateTime` and `Duration`  
    39.3 `RegExp` and `Pattern`  
    39.4 `Uri`, `BigInt`, and `Stopwatch`  
40. **[Math & Randomness (`dart:math`)](./[40]-Math-Library.md)**  
    40.1 Constants and Functions  
    40.2 `Random` and Secure Random  
    40.3 `Point` and `Rectangle`  
41. **[Files & I/O (`dart:io`)](./[41]-File-IO.md)**  
    41.1 Reading and Writing Files  
    41.2 Directories and Paths  
    41.3 Standard Input and Output  
    41.4 Processes and Platform Information  
42. **[JSON & Data Serialization (`dart:convert`)](./[42]-JSON-and-Serialization.md)**  
    42.1 Encoding and Decoding JSON  
    42.2 UTF-8, Base64, and Codecs  
    42.3 `fromJson` / `toJson` Patterns  
    42.4 Code Generation with `json_serializable`  
43. **[Command-Line Applications](./[43]-Command-Line-Apps.md)**  
    43.1 `main(List<String> args)`  
    43.2 Parsing Arguments with `package:args`  
    43.3 Exit Codes and `stdin`/`stdout`  
    43.4 Compiling Standalone Executables  
44. **[HTTP, Networking & Servers](./[44]-HTTP-and-Servers.md)**  
    44.1 Making Requests with `package:http`  
    44.2 `HttpServer` and Routing with `shelf`  
    44.3 WebSockets  
    44.4 Building a Simple REST API  

**Advanced Language Features**  

45. **[Metadata & Annotations](./[45]-Annotations.md)**  
    45.1 Built-in Annotations (`@override`, `@deprecated`, `@pragma`)  
    45.2 Creating Custom Annotations  
46. **[Type System Deep Dive](./[46]-Type-System.md)**  
    46.1 Sound Type System  
    46.2 Subtyping, Variance, and `covariant`  
    46.3 Type Promotion and `Never`  
    46.4 `Null`, `void`, and `Object?`  
    46.5 Runtime Type Checks and `runtimeType`  
47. **[Metaprogramming & Code Generation](./[47]-Code-Generation.md)**  
    47.1 `build_runner` and `source_gen`  
    47.2 Common Generators (`freezed`, `json_serializable`)  
    47.3 Reflection Limits in Dart  
48. **[Memory Management & Performance](./[48]-Memory-and-Performance.md)**  
    48.1 Garbage Collection in Dart  
    48.2 `const` Canonicalization  
    48.3 Efficient Collections and Strings  
    48.4 Profiling with DevTools  
49. **[Interoperability (FFI & JS Interop)](./[49]-Interoperability.md)**  
    49.1 Calling C with `dart:ffi`  
    49.2 JS Interop (`dart:js_interop`)  
    49.3 Platform Channels Overview  

**Testing & Debugging**  

50. **[Debugging Dart Code](./[50]-Debugging.md)**  
    50.1 Breakpoints and Stepping  
    50.2 Logging and `dart:developer`  
    50.3 Reading Stack Traces  
51. **[Unit Testing (`package:test`)](./[51]-Unit-Testing.md)**  
    51.1 Writing Tests with `test()` and `group()`  
    51.2 Matchers and `expect`  
    51.3 Async Tests  
    51.4 Mocking with `mocktail`  
    51.5 Code Coverage  
52. **[Static Analysis & Linting](./[52]-Analysis-and-Linting.md)**  
    52.1 Strict Analysis Modes  
    52.2 Recommended Lint Sets  
    52.3 Fixing Issues with `dart fix`  

**Compiling & Deployment**  

53. **[Compilation Targets (JIT, AOT, JS, Wasm)](./[53]-Compilation-Targets.md)**  
    53.1 `dart compile exe`, `aot-snapshot`, and `kernel`  
    53.2 `dart compile js`  
    53.3 `dart compile wasm`  
    53.4 Choosing a Target  

**Beyond the Language**  

54. **[Introduction to Flutter](./[54]-Introduction-to-Flutter.md)**  
    54.1 What Is Flutter?  
    54.2 Widgets and the Widget Tree  
    54.3 How Dart Powers Flutter (Hot Reload, AOT)  
55. **[Server-Side Dart](./[55]-Server-Side-Dart.md)**  
    55.1 Frameworks (Shelf, Dart Frog, Serverpod)  
    55.2 Databases and Persistence  
    55.3 Deploying with Docker  

**Best Practices**  

56. **[Effective Dart: Style, Usage & Design](./[56]-Effective-Dart.md)**  
    56.1 Style Guide  
    56.2 Usage Guidelines  
    56.3 API Design Guidelines  
57. **[Common Pitfalls & Idioms](./[57]-Pitfalls-and-Idioms.md)**  
    57.1 Null Safety Mistakes  
    57.2 Async Gotchas  
    57.3 Collection and Equality Traps  
58. **[Dart Cheat Sheet & Next Steps](./[58]-Cheat-Sheet-and-Next-Steps.md)**  
    58.1 Syntax Quick Reference  
    58.2 Project Ideas  
    58.3 Where to Go Next