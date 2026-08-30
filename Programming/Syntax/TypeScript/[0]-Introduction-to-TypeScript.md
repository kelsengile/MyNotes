[⬅ Back to README](../../../README.md)

# TypeScript


Welcome! This is a self-paced course for learning TypeScript, the statically-typed superset of JavaScript that adds a powerful type system to the language of the web — catching bugs at compile time and powering most large-scale modern JavaScript codebases.

---

## What is TypeScript?

TypeScript lets you:
- Add static types on top of JavaScript to catch errors before runtime
- Describe the shape of objects, functions, and data with interfaces and types
- Build safer, more maintainable large-scale applications
- Get rich autocomplete, refactoring, and tooling support in your editor
- Use every JavaScript feature, browser API, and npm package, fully typed
- Build type-safe backend services (Node.js), frontend apps (React/Vue/Angular), and full-stack applications
- Model complex domains with generics, unions, and advanced type utilities
- Gradually adopt types into existing JavaScript codebases
- Compile down to clean, compatible JavaScript for any target environment

## Table of Contents

**Getting Started**  

1. **[Installing TypeScript & First-Time Setup](./[1]-Installation-and-Setup.md)**  
    1.1 What You Need Before Starting  
    1.2 Installing TypeScript  
    1.3 Verifying the Compiler  
    1.4 Setting Up Your First Project Folder  
    1.5 Writing and Compiling Your First File  
2. **[Compiling & Running TypeScript (tsc, ts-node, tsx)](./[2]-Compiling-and-Running-TypeScript.md)**  
    2.1 The Core Idea: TypeScript Doesn't Run Directly  
    2.2 Using `tsc` (the TypeScript Compiler)  
    2.3 Using `ts-node` for Instant Execution  
    2.4 Using `tsx` for Fast, Modern Execution  
    2.5 Choosing the Right Tool  
3. **[tsconfig.json Fundamentals](./[3]-tsconfig-Fundamentals.md)**  
    3.1 What tsconfig.json Does  
    3.2 The `compilerOptions` Object  
    3.3 Including and Excluding Files  
    3.4 Extending a Base Config  
    3.5 A Minimal, Sensible Starting Config  
4. **[TypeScript in the Editor (IntelliSense, type checking)](./[4]-TypeScript-in-the-Editor.md)**  
    4.1 Why the Editor Experience Matters  
    4.2 Setting Up VS Code  
    4.3 IntelliSense: Autocomplete and Signatures  
    4.4 Inline Type Errors  
    4.5 Quick Fixes and Refactoring Tools  

**Core Types**  

5. **[Basic Types (string, number, boolean, null, undefined)](./[5]-Basic-Types.md)**  
    5.1 Annotating Variables  
    5.2 `string`, `number`, and `boolean`  
    5.3 `null` and `undefined`  
    5.4 Letting TypeScript Infer Basic Types  
    5.5 `bigint` and `symbol`  
6. **[Arrays & Tuples](./[6]-Arrays-and-Tuples.md)**  
    6.1 Typing Arrays  
    6.2 Arrays of Complex Types  
    6.3 Readonly Arrays  
    6.4 What Tuples Are  
    6.5 Named Tuples and Optional Elements  
7. **[Objects & Type Literals](./[7]-Objects-and-Type-Literals.md)**  
    7.1 Typing an Object Inline  
    7.2 Optional Properties  
    7.3 Nested Object Shapes  
    7.4 Excess Property Checks  
    7.5 Object Types vs. `object`  
8. **[Any, Unknown, Void & Never](./[8]-Any-Unknown-Void-and-Never.md)**  
    8.1 `any`: Opting Out of Type Checking  
    8.2 `unknown`: The Safe Alternative  
    8.3 `void`: No Meaningful Return Value  
    8.4 `never`: A Value That Can't Exist  
    8.5 Choosing Between Them  
9. **[Type Inference & Contextual Typing](./[9]-Type-Inference.md)**  
    9.1 What Type Inference Is  
    9.2 Inference in Function Return Types  
    9.3 Best Common Type  
    9.4 Contextual Typing  
    9.5 When to Annotate Anyway  
10. **[Type Assertions & Type Casting](./[10]-Type-Assertions.md)**  
    10.1 What a Type Assertion Is  
    10.2 The `as` Syntax  
    10.3 The Angle-Bracket Syntax  
    10.4 The `as const` Assertion  
    10.5 The Double Assertion, and When to Avoid Assertions  
11. **[Enums (numeric, string, const)](./[11]-Enums.md)**  
    11.1 What Enums Are For  
    11.2 Numeric Enums  
    11.3 String Enums  
    11.4 `const enum`  
    11.5 An Alternative: Union of String Literals  

**Functions**  

12. **[Typing Functions: Parameters & Return Types](./[12]-Typing-Functions.md)**  
    12.1 Typing Parameters  
    12.2 Typing the Return Value  
    12.3 Arrow Functions  
    12.4 Function Types  
    12.5 Void Functions and Unused Return Values  
13. **[Optional, Default & Rest Parameters](./[13]-Optional-Default-and-Rest-Parameters.md)**  
    13.1 Optional Parameters  
    13.2 Default Parameters  
    13.3 Rest Parameters  
    13.4 Combining All Three  
    13.5 Destructured Parameters with Defaults  
14. **[Function Overloads](./[14]-Function-Overloads.md)**  
    14.1 The Problem Overloads Solve  
    14.2 Overload Signatures vs. the Implementation Signature  
    14.3 Why Not Just Use a Union?  
    14.4 Ordering Matters  
    14.5 A Practical Example  
15. **[`this` Parameters & Typing Callbacks](./[15]-This-Parameters-and-Typing-Callbacks.md)**  
    15.1 Why `this` Needs Special Handling  
    15.2 The `this` Parameter  
    15.3 Arrow Functions and `this`  
    15.4 Typing Callback Parameters  
    15.5 `noImplicitThis`  

**Interfaces & Type Aliases**  

16. **[Interfaces](./[16]-Interfaces.md)**  
    16.1 Giving a Shape a Name  
    16.2 Optional and Readonly Members  
    16.3 Method Signatures  
    16.4 Interfaces Describing Function Types  
    16.5 Implementing an Interface in a Class  
17. **[Type Aliases](./[17]-Type-Aliases.md)**  
    17.1 What a Type Alias Is  
    17.2 Aliasing Object Shapes  
    17.3 Aliasing Unions, Tuples, and Functions  
    17.4 Combining Types with `&`  
    17.5 Generic Type Aliases  
18. **[Interfaces vs. Type Aliases: When to Use Which](./[18]-Interfaces-vs-Type-Aliases.md)**  
    18.1 What They Have in Common  
    18.2 What Only Type Aliases Can Do  
    18.3 What Only Interfaces Can Do: Declaration Merging  
    18.4 Extending Shapes  
    18.5 A Practical Rule of Thumb  
19. **[Extending Interfaces & Declaration Merging](./[19]-Extending-Interfaces-and-Declaration-Merging.md)**  
    19.1 Basic Extension with `extends`  
    19.2 Extending Multiple Interfaces  
    19.3 Overriding a Property  
    19.4 Declaration Merging  
    19.5 Practical Use: Augmenting Third-Party Types  
20. **[Readonly & Optional Properties](./[20]-Readonly-and-Optional-Properties.md)**  
    20.1 Optional Properties Recap  
    20.2 The `readonly` Modifier  
    20.3 `readonly` Only Prevents Reassignment, Not Deep Mutation  
    20.4 Combining Optional and Readonly  
    20.5 Deriving Readonly/Partial Versions of a Type  
21. **[Index Signatures](./[21]-Index-Signatures.md)**  
    21.1 The Problem: Unknown Property Names  
    21.2 String vs. Number Index Signatures  
    21.3 Combining an Index Signature with Known Properties  
    21.4 Index Signatures with `Record`  
    21.5 A Caveat: No Guarantee the Key Exists  

**Union, Intersection & Narrowing**  

22. **[Union & Intersection Types](./[22]-Union-and-Intersection-Types.md)**  
    22.1 What a Union Type Is  
    22.2 Unions of Literal Types  
    22.3 Working with Union Values Safely  
    22.4 What an Intersection Type Is  
    22.5 Intersections with Overlapping Properties  
23. **[Type Narrowing (typeof, instanceof, in)](./[23]-Type-Narrowing.md)**  
    23.1 What Narrowing Means  
    23.2 Narrowing with `typeof`  
    23.3 Narrowing with `instanceof`  
    23.4 Narrowing with `in`  
    23.5 Truthiness and Equality Narrowing  
24. **[Discriminated Unions](./[24]-Discriminated-Unions.md)**  
    24.1 The Problem with Loosely Related Shapes  
    24.2 Narrowing on the Discriminant  
    24.3 Exhaustiveness Checking with `never`  
    24.4 Discriminated Unions Beyond `switch`  
    24.5 A Common Real-World Shape: Result Types  
25. **[Type Guards & User-Defined Type Predicates](./[25]-Type-Guards.md)**  
    25.1 What a Type Guard Is  
    25.2 The Problem: Logic TypeScript Can't See Through  
    25.3 Writing a Type Predicate  
    25.4 Type Guards for Custom Object Shapes  
    25.5 Array Filtering with Type Predicates  

**Generics**  

26. **[Generic Functions & Interfaces](./[26]-Generic-Functions-and-Interfaces.md)**  
    26.1 The Problem Generics Solve  
    26.2 Writing a Generic Function  
    26.3 Multiple Type Parameters  
    26.4 Generic Interfaces  
    26.5 A Practical Generic Interface: A Typed Cache  
27. **[Generic Constraints (`extends`)](./[27]-Generic-Constraints.md)**  
    27.1 The Problem: Unconstrained Generics Know Nothing  
    27.2 Constraining a Type Parameter  
    27.3 Constraining with `keyof`  
    27.4 Default Type Parameters with Constraints  
    27.5 Why Constraints Matter  
28. **[Generic Classes](./[28]-Generic-Classes.md)**  
    28.1 Declaring a Generic Class  
    28.2 A Practical Example: A Generic Stack  
    28.3 Constraining a Class's Type Parameter  
    28.4 Multiple Type Parameters in a Class  
    28.5 Generic Methods Inside a Non-Generic Class  
29. **[Default Generic Parameters](./[29]-Default-Generic-Parameters.md)**  
    29.1 The Problem: Always Specifying a Type Gets Repetitive  
    29.2 Giving a Type Parameter a Default  
    29.3 Defaults on Generic Functions and Classes  
    29.4 Combining Defaults with Constraints  
    29.5 When to Reach for a Default  

**Object-Oriented TypeScript**  

30. **[Classes & Access Modifiers (public, private, protected)](./[30]-Classes-and-Access-Modifiers.md)**  
    30.1 Declaring a Class  
    30.2 Constructors and Properties  
    30.3 Access Modifiers: public, private, protected  
    30.4 Why Access Modifiers Matter  
31. **[Readonly, Static & Abstract Members](./[31]-Readonly-Static-and-Abstract-Members.md)**  
    31.1 Readonly Properties  
    31.2 Static Properties and Methods  
    31.3 Abstract Classes and Methods  
    31.4 Combining Readonly, Static, and Abstract  
32. **[Interfaces with Classes (`implements`)](./[32]-Interfaces-with-Classes.md)**  
    32.1 Implementing an Interface  
    32.2 Implementing Multiple Interfaces  
    32.3 Interfaces vs Abstract Classes for Contracts  
    32.4 Common Pitfalls  
33. **[Parameter Properties & Constructor Shorthand](./[33]-Parameter-Properties.md)**  
    33.1 The Verbose Way  
    33.2 Parameter Properties Shorthand  
    33.3 Mixing Access Modifiers in Parameter Properties  
    33.4 When Not to Use Shorthand  
34. **[Decorators (class, method, property decorators)](./[34]-Decorators.md)**  
    34.1 What Are Decorators  
    34.2 Enabling Decorators  
    34.3 Class Decorators  
    34.4 Method and Property Decorators  
    34.5 Practical Use Cases  

**Advanced Types**  
    35. **[Mapped Types](./[35]-Mapped-Types.md)**  
    36. **[Conditional Types](./[36]-Conditional-Types.md)**  
    37. **[The `infer` Keyword](./[37]-Infer-Keyword.md)**  
    38. **[Template Literal Types](./[38]-Template-Literal-Types.md)**  
    39. **[Utility Types (Partial, Pick, Omit, Record, ReturnType, and more)](./[39]-Utility-Types.md)**  
    40. **[Recursive Types](./[40]-Recursive-Types.md)**  

**Modules & Namespaces**  
    41. **[ES Modules in TypeScript (import/export)](./[41]-ES-Modules.md)**  
    42. **[Namespaces (legacy module pattern)](./[42]-Namespaces.md)**  
    43. **[Ambient Declarations & `.d.ts` Files](./[43]-Ambient-Declarations-and-d-ts-Files.md)**  
    44. **[Type Declaration Packages (DefinitelyTyped, @types)](./[44]-Type-Declaration-Packages.md)**  
    45. **[Module Resolution Strategies](./[45]-Module-Resolution-Strategies.md)**  

**Working with JavaScript**  
    46. **[Typing Third-Party & Untyped JavaScript](./[46]-Typing-Untyped-JavaScript.md)**  
    47. **[Migrating a JavaScript Project to TypeScript](./[47]-Migrating-JS-to-TypeScript.md)**  
    48. **[JSDoc-Based Type Checking (checkJs)](./[48]-JSDoc-Based-Type-Checking.md)**  

**Async & Error Handling**  
    49. **[Typing Promises & async/await](./[49]-Typing-Promises-and-Async-Await.md)**  
    50. **[Error Handling & Typed Errors](./[50]-Error-Handling-and-Typed-Errors.md)**  
    51. **[Result/Either Patterns for Type-Safe Errors](./[51]-Result-Either-Patterns.md)**  

**Tooling & Configuration**  
    52. **[Strict Mode & Compiler Flags](./[52]-Strict-Mode-and-Compiler-Flags.md)**  
    53. **[Linting (ESLint with typescript-eslint)](./[53]-Linting.md)**  
    54. **[Formatting (Prettier)](./[54]-Formatting.md)**  
    55. **[Path Aliases & Project References](./[55]-Path-Aliases-and-Project-References.md)**  
    56. **[Monorepos with TypeScript (Turborepo, Nx)](./[56]-Monorepos-with-TypeScript.md)**  

**TypeScript with Frontend Frameworks**  
    57. **[React & TypeScript (typed props, hooks, events)](./[57]-React-and-TypeScript.md)**  
    58. **[Vue & TypeScript](./[58]-Vue-and-TypeScript.md)**  
    59. **[Angular & TypeScript (Angular is TypeScript-first)](./[59]-Angular-and-TypeScript.md)**  
    60. **[Next.js & TypeScript](./[60]-Nextjs-and-TypeScript.md)**  

**TypeScript on the Backend**  
    61. **[Node.js & TypeScript Setup](./[61]-Nodejs-and-TypeScript-Setup.md)**  
    62. **[Express & TypeScript](./[62]-Express-and-TypeScript.md)**  
    63. **[NestJS (TypeScript-first framework)](./[63]-NestJS.md)**  
    64. **[Building Type-Safe REST APIs](./[64]-Type-Safe-REST-APIs.md)**  
    65. **[End-to-End Type Safety (tRPC)](./[65]-End-to-End-Type-Safety-tRPC.md)**  
    66. **[GraphQL & TypeScript (Apollo, TypeGraphQL)](./[66]-GraphQL-and-TypeScript.md)**  
    67. **[Runtime Validation with Static Types (Zod, io-ts)](./[67]-Runtime-Validation.md)**  

**Databases with TypeScript**  
    68. **[Type-Safe ORMs (Prisma, Drizzle)](./[68]-Type-Safe-ORMs.md)**  
    69. **[TypeORM & Sequelize with TypeScript](./[69]-TypeORM-and-Sequelize.md)**  

**Testing & Code Quality**  
    70. **[Testing with Jest & Vitest in TypeScript](./[70]-Testing-Jest-and-Vitest.md)**  
    71. **[Type Testing (tsd, expect-type)](./[71]-Type-Testing.md)**  
    72. **[Mocking in TypeScript](./[72]-Mocking.md)**  

**Design Patterns & Architecture**  
    73. **[SOLID Principles in TypeScript](./[73]-SOLID-Principles.md)**  
    74. **[Design Patterns in TypeScript (Factory, Builder, Strategy)](./[74]-Design-Patterns.md)**  
    75. **[Domain Modeling with Types](./[75]-Domain-Modeling-with-Types.md)**  

**Packaging & Deployment**  
    76. **[Building & Bundling TypeScript (tsc, esbuild, Vite)](./[76]-Building-and-Bundling.md)**  
    77. **[Publishing Typed npm Packages](./[77]-Publishing-Typed-npm-Packages.md)**  
    78. **[Docker Basics for TypeScript Apps](./[78]-Docker-Basics.md)**  
    79. **[CI/CD Basics (GitHub Actions)](./[79]-CICD-Basics.md)**  

**Best Practices**  
    80. **[Idiomatic TypeScript & Style Guides](./[80]-Best-Practices-and-Style.md)**
