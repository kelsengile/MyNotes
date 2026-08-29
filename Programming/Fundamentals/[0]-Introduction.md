# Introduction to Programming Fundamentals

[⬅ Back to README](../../README.md)

This note kicks off the "Programming Fundamentals" section — the core building blocks that show up in almost every language: variables, data types, control flow, functions, and basic problem-solving patterns.

## Why Fundamentals Matter
Languages and frameworks change constantly, but the underlying concepts stay the same. Once you understand how a computer stores data, makes decisions, and repeats actions, picking up a new language becomes a matter of learning syntax rather than relearning how to think.

---

## Table of Contents  

1. **[Introduction to Programming](./[1]-Introduction-to-Programming.md)**  
   1.1 What is Programming?  
   1.2 How Computers Execute Code (CPU, memory, compilers/interpreters)  
   1.3 Compiled vs. Interpreted vs. JIT Languages  
   1.4 Choosing a Programming Language  

2. **[Computer Science Foundations](./[2]-Computer-Science-Foundations.md)**  
   2.1 Number Systems (Binary, Hex, Octal)  
   2.2 Character Encoding (ASCII, Unicode, UTF-8)  
   2.3 Basic Computer Architecture (CPU, RAM, Storage, Cache)  
   2.4 Operating System Basics (processes, file systems, permissions)  

3. **[Programmer Mindset & Thinking Skills](./[3]-Programmer-Mindset-Thinking-Skills.md)**  
   3.1 Computational Thinking  
   3.2 Decomposition  
   3.3 Pattern Recognition & Abstraction  
   3.4 Algorithmic Thinking vs. Just "Getting Code to Run"  
   3.5 Debugging Mindset  
   3.6 Tolerance for Ambiguity & Incomplete Information  
   3.7 Systems Thinking  
   3.8 "Rubber Duck" Reasoning  
   3.9 Reading Code (not just writing it)  
   3.10 Embracing Failure & Iteration  
   3.11 Curiosity-Driven Learning  
   3.12 Managing Frustration & Cognitive Load  
   3.13 Knowing When to Ask for Help vs. Struggle Through  
   3.14 Trade-off Thinking  
   3.15 Growth Mindset in a Fast-Changing Field  

4. **[Setting Up Your Environment](./[4]-Setting-Up-Your-Environment.md)**  
   4.1 Installing a Code Editor / IDE  
   4.2 Command Line Basics  
   4.3 Version Control (Git & GitHub Basics)  
   4.4 Package Managers & Dependencies  
   4.5 Environment Variables & Configuration  

5. **[Core Syntax & Basics](./[5]-Core-Syntax-Basics.md)**  
   5.1 Variables and Data Types  
   5.2 Type Systems (Static vs. Dynamic, Strong vs. Weak)  
   5.3 Operators (Arithmetic, Comparison, Logical, Bitwise)  
   5.4 Input and Output  
   5.5 Comments and Code Style  

6. **[Control Flow](./[6]-Control-Flow.md)**  
   6.1 Conditional Statements (if/else, switch/match)  
   6.2 Loops (for, while, do-while)  
   6.3 Break, Continue, and Nested Loops  

7. **[Data Structures](./[7]-Data-Structures.md)**  
   7.1 Arrays / Lists  
   7.2 Strings  
   7.3 Dictionaries / Maps / Hash Tables  
   7.4 Sets  
   7.5 Stacks and Queues  
   7.6 Linked Lists  
   7.7 Trees and Graphs  

8. **[Functions & Program Structure](./[8]-Functions-Program-Structure.md)**  
   8.1 Defining and Calling Functions  
   8.2 Parameters, Return Values, Default Args  
   8.3 Scope and Lifetime of Variables  
   8.4 Recursion  
   8.5 Higher-Order Functions & Closures  
   8.6 Pure Functions & Side Effects  

9. **[Memory Management](./[9]-Memory-Management.md)**  
   9.1 Stack vs. Heap  
   9.2 Pointers & References  
   9.3 Manual vs. Garbage-Collected Memory  
   9.4 Common Memory Bugs (leaks, dangling pointers)  

10. **[Object-Oriented Programming](./[10]-Object-Oriented-Programming.md)**  
    10.1 Classes and Objects  
    10.2 Encapsulation  
    10.3 Inheritance  
    10.4 Polymorphism  
    10.5 Abstraction  
    10.6 Composition over Inheritance  
    10.7 SOLID Principles  

11. **[Functional Programming Concepts](./[11]-Functional-Programming-Concepts.md)**  
    11.1 Immutability  
    11.2 First-Class & Pure Functions  
    11.3 Map, Filter, Reduce  
    11.4 When to Use FP vs. OOP  

12. **[Error Handling & Debugging](./[12]-Error-Handling-Debugging.md)**  
    12.1 Common Bug Types  
    12.2 Try/Catch, Exceptions, Error Codes  
    12.3 Debugging Tools and Techniques  
    12.4 Logging  

13. **[Text Processing](./[13]-Text-Processing.md)**  
    13.1 Regular Expressions (Regex)  
    13.2 String Parsing & Manipulation Patterns  

14. **[Working with Files & Data](./[14]-Working-with-Files-Data.md)**  
    14.1 Reading and Writing Files  
    14.2 Working with JSON/CSV/XML  
    14.3 Databases (SQL Basics, NoSQL Intro)  

15. **[Networking & Web Basics](./[15]-Networking-Web-Basics.md)**  
    15.1 How the Internet Works (IP, DNS, TCP/UDP)  
    15.2 HTTP/HTTPS Fundamentals  
    15.3 Client-Server Model  
    15.4 REST & API Design Basics  

16. **[Algorithms & Problem Solving](./[16]-Algorithms-Problem-Solving.md)**  
    16.1 Time and Space Complexity (Big O)  
    16.2 Sorting Algorithms  
    16.3 Searching Algorithms  
    16.4 Recursion & Divide-and-Conquer  
    16.5 Problem-Solving Strategies  

17. **[Software Design & Architecture](./[17]-Software-Design-Architecture.md)**  
    17.1 Design Patterns (Singleton, Factory, Observer, etc.)  
    17.2 Modular Design & Separation of Concerns  
    17.3 Basic System Design Concepts  
    17.4 APIs, Libraries, and Frameworks  

18. **[Testing & Quality](./[18]-Testing-Quality.md)**  
    18.1 Unit Testing  
    18.2 Integration & End-to-End Testing  
    18.3 Test-Driven Development (TDD)  
    18.4 Code Reviews  

19. **[Concurrency & Performance](./[19]-Concurrency-Performance.md)**  
    19.1 Processes vs. Threads  
    19.2 Synchronous vs. Asynchronous Code  
    19.3 Race Conditions & Deadlocks  
    19.4 Basic Performance Optimization  

20. **[Security Basics](./[20]-Security-Basics.md)**  
    20.1 Common Vulnerabilities (injection, XSS, etc.)  
    20.2 Input Validation & Sanitization  
    20.3 Authentication vs. Authorization  
    20.4 Secure Coding Habits  

21. **[Deployment & DevOps Basics](./[21]-Deployment-DevOps-Basics.md)**  
    21.1 Containers Basics (Docker concepts)  
    21.2 Cloud Basics (hosting, servers, deployment)  
    21.3 Build, Deploy, and CI/CD Basics  

22. **[Software Development Lifecycle](./[22]-Software-Development-Lifecycle.md)**  
    22.1 Requirements & Planning  
    22.2 Agile, Scrum, Kanban Basics  
    22.3 Maintenance & Refactoring  

23. **[Supporting Math Foundations](./[23]-Supporting-Math-Foundations-optional.md)**  
    23.1 Discrete Math  
    23.2 Linear Algebra / Statistics  
    23.3 Calculus  

24. **[Legal, Ethical & Professional Practice](./[24]-Legal-Ethical-Professional-Practice.md)**  
    24.1 Licensing (Open Source vs. Proprietary)  
    24.2 Intellectual Property Basics  
    24.3 Accessibility (a11y) Basics  
    24.4 Internationalization/Localization (i18n)  

25. **[Best Practices](./[25]-Best-Practices.md)**  
    25.1 Naming Conventions  
    25.2 Code Readability & DRY Principle  
    25.3 Documentation  
    25.4 Technical Debt  

26. **[Soft Skills & Career Development](./[26]-Soft-Skills-Career-Development.md)**  
    26.1 Reading Documentation Effectively  
    26.2 Communicating Technical Ideas  
    26.3 Estimating Work & Time Management  
    26.4 Contributing to Open Source  

27. **[Next Steps](./[27]-Next-Steps.md)**  
    27.1 Choosing a Specialization  
    27.2 Building Projects & Portfolio  
    27.3 Resources for Continued Learning