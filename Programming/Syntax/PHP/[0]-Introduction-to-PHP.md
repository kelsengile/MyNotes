[⬅ Back to README](../../../README.md)

# PHP

Welcome! This is a self-paced course for learning PHP, the widely-used, server-side scripting language that powers a huge share of the web — from WordPress sites to large-scale applications built with Laravel and Symfony.

---

## What is PHP?

PHP lets you:
- Write server-side scripts that generate dynamic web pages
- Build full-featured web applications, APIs, and CMS platforms
- Work with files, databases, and external APIs
- Organize code using functions, classes, namespaces, and Composer packages
- Handle sessions, cookies, and user authentication
- Power major platforms and frameworks (WordPress, Laravel, Symfony, Drupal)
- Process forms, uploads, and user input securely
- Build, test, and deploy production-ready web applications

## Table of Contents

**Getting Started**  

1. **[Installing PHP & First-Time Setup](./[1]-Installation-and-Setup.md)**  
    1.1 Why Install PHP Locally  
    1.2 Installing PHP  
    1.3 Verifying Your Installation  
    1.4 Choosing an Editor  
2. **[Running Code: CLI, Built-in Server & Web Servers (Apache/Nginx)](./[2]-Running-PHP-Code.md)**  
    2.1 The CLI (Command Line Interface)  
    2.2 PHP's Built-in Development Server  
    2.3 Running PHP with Apache/Nginx  
    2.4 Choosing the Right Method  
3. **[Composer & Dependency Management](./[3]-Composer-and-Dependency-Management.md)**  
    3.1 What is Composer?  
    3.2 Installing Composer  
    3.3 composer.json and Requiring Packages  
    3.4 Autoloading with Composer  
4. **[php.ini & Configuration Basics](./[4]-php-ini-and-Configuration.md)**  
    4.1 What is php.ini?  
    4.2 Finding Your php.ini File  
    4.3 Common Settings to Know  
    4.4 Changing Settings at Runtime  

**Core Syntax**  

5. **[Variables & Basic Data Types](./%5B5%5D-Variables-and-Data-Types%20%281%29.md)**  
    5.1 Declaring Variables  
    5.2 Variable Naming Rules  
    5.3 PHP's Data Types Overview  
    5.4 Type Juggling and Loose Typing  
    5.5 Checking and Converting Types  
6. **[Numbers, Strings & Booleans](./[6]-Numbers-Strings-and-Booleans.md)**  
    6.1 Working with Integers and Floats  
    6.2 Arithmetic and Number Functions  
    6.3 Strings: Single vs Double Quotes  
    6.4 Booleans and Truthy/Falsy Values  
7. **[Operators & Expressions (arithmetic, comparison, logical, spaceship, null coalescing)](./%5B7%5D-Operators-and-Expressions%20%281%29.md)**  
    7.1 Arithmetic Operators  
    7.2 Comparison Operators (== vs ===)  
    7.3 Logical Operators  
    7.4 The Spaceship Operator (<=>)  
    7.5 The Null Coalescing Operator (?? and ??=)  
8. **[Conditionals: if, elseif, else, switch, match](./%5B8%5D-Conditionals%20%281%29.md)**  
    8.1 if, elseif, else  
    8.2 switch Statements  
    8.3 The match Expression  
    8.4 Ternary and Short Ternary Operators  
9. **[Loops: for, while, do-while, foreach, break, continue](./%5B9%5D-Loops%20%281%29.md)**  
    9.1 for Loops  
    9.2 while and do-while Loops  
    9.3 foreach Loops  
    9.4 break and continue  
10. **[Functions & Scope (default args, variadics, arrow functions)](./[10]-Functions-and-Scope.md)**  
    10.1 Defining and Calling Functions  
    10.2 Parameters, Default Arguments, and Type Hints  
    10.3 Variadic Functions  
    10.4 Variable Scope  
    10.5 Arrow Functions (fn)  
11. **[String Formatting & Manipulation (interpolation, heredoc/nowdoc, string functions)](./[11]-String-Formatting.md)**  
    11.1 String Interpolation  
    11.2 Heredoc and Nowdoc  
    11.3 Common String Functions  
    11.4 sprintf and Formatting Output  
12. **[Error & Exception Handling (try, catch, finally, custom exceptions)](./[12]-Error-and-Exception-Handling.md)**  
    12.1 Why Handle Errors?  
    12.2 try, catch, finally  
    12.3 Throwing and Creating Custom Exceptions  
    12.4 Multiple catch Blocks and Exception Hierarchies  

**Data Structures**  
13. **[Arrays: Indexed, Associative & Multidimensional](./[13]-Arrays.md)**  
    13.1 Indexed Arrays  
    13.2 Associative Arrays  
    13.3 Multidimensional Arrays  
    13.4 Common Array Operations  
14. **[Array Functions (map, filter, reduce, sort variants)](./[14]-Array-Functions.md)**  
    14.1 array_map  
    14.2 array_filter  
    14.3 array_reduce  
    14.4 Sorting Arrays  

**Object-Oriented Programming**  

15. **[Classes & Objects](./[15]-OOP-Classes-and-Objects.md)**  
    15.1 What is Object-Oriented Programming?  
    15.2 Defining a Class  
    15.3 Creating Objects (Instances)  
    15.4 Properties and Methods  
16. **[Constructors, Destructors & the `this` Keyword](./[16]-Constructors-and-Destructors.md)**  
    16.1 The __construct Method  
    16.2 Constructor Property Promotion  
    16.3 The $this Keyword  
    16.4 The __destruct Method  
17. **[Inheritance & Polymorphism](./[17]-Inheritance-and-Polymorphism.md)**  
    17.1 Extending a Class  
    17.2 Overriding Methods  
    17.3 The parent Keyword  
    17.4 Polymorphism in Practice  
18. **[Encapsulation & Visibility (public, private, protected)](./[18]-Encapsulation-and-Visibility.md)**  
    18.1 What is Encapsulation?  
    18.2 public, private, and protected  
    18.3 Getters and Setters  
    18.4 readonly Properties  
19. **[Interfaces & Abstract Classes](./[19]-Interfaces-and-Abstract-Classes.md)**  
    19.1 Interfaces  
    19.2 Abstract Classes  
    19.3 Interfaces vs Abstract Classes  
    19.4 Implementing Multiple Interfaces  
20. **[Traits](./[20]-Traits.md)**  
    20.1 What Problem Do Traits Solve?  
    20.2 Defining and Using a Trait  
    20.3 Conflict Resolution  
    20.4 Traits vs Interfaces vs Abstract Classes  
21. **[Magic Methods (`__construct`, `__get`, `__call`, `__toString`, etc.)](./[21]-Magic-Methods.md)**  
    21.1 What Are Magic Methods?  
    21.2 __toString  
    21.3 __get and __set  
    21.4 __call and __callStatic  
22. **[Static Members, Class Constants & Final Keyword](./[22]-Static-Members-and-Constants.md)**  
    22.1 Static Properties and Methods  
    22.2 Class Constants  
    22.3 The self vs static Keyword  
    22.4 The final Keyword  
23. **[Enums](./[23]-Enums.md)**  
    23.1 What Are Enums?  
    23.2 Pure Enums  
    23.3 Backed Enums  
    23.4 Enum Methods and Interfaces  
24. **[Namespaces & Autoloading (PSR-4)](./[24]-Namespaces-and-Autoloading.md)**  
    24.1 What Are Namespaces?  
    24.2 Declaring and Using Namespaces  
    24.3 PSR-4 Autoloading  
    24.4 use Statements and Aliasing  

**Advanced Language Features**  

25. **[Closures & Anonymous Functions](./[25]-Closures-and-Anonymous-Functions.md)**  
    25.1 Anonymous Functions  
    25.2 Closures and the use Keyword  
    25.3 Arrow Functions Revisited  
    25.4 Passing Closures as Callbacks  
26. **[Generators & Iterators (yield)](./[26]-Generators-and-Iterators.md)**  
    26.1 What Are Iterators?  
    26.2 Introducing Generators  
    26.3 The yield Keyword  
    26.4 Generators with Keys and yield from  
27. **[Type Declarations & Strict Typing (`declare(strict_types=1)`)](./[27]-Type-Declarations-and-Strict-Typing.md)**  
    27.1 Type Declarations for Parameters and Returns  
    27.2 Nullable and Union Types  
    27.3 declare(strict_types=1)  
    27.4 Strict vs Coercive Typing  
28. **[Attributes (PHP 8 annotations)](./[28]-Attributes.md)**  
    28.1 What Are Attributes?  
    28.2 Attribute Syntax  
    28.3 Built-in Attributes  
    28.4 Reading Attributes with Reflection (Preview)  
29. **[Reflection API](./[29]-Reflection.md)**  
    29.1 What is the Reflection API?  
    29.2 Inspecting Classes with ReflectionClass  
    29.3 Inspecting Methods and Properties  
    29.4 Practical Use Cases  
30. **[PHP Version Highlights (PHP 7 through PHP 8.x)](./[30]-PHP-Version-Highlights.md)**  
    30.1 PHP 7.x Highlights  
    30.2 PHP 8.0 Highlights  
    30.3 PHP 8.1 Highlights  
    30.4 PHP 8.2 – 8.4 Highlights  

**Standard Library**  
    31. **[Working with Files (fopen, file_get_contents, filesystem functions)](./[31]-Working-with-Files.md)**  
    32. **[Working with Dates & Times (DateTime, DateTimeImmutable)](./[32]-Dates-and-Times.md)**  
    33. **[Regular Expressions (PCRE, preg_* functions)](./[33]-Regular-Expressions.md)**  
    34. **[Data Serialization (json_encode/decode, serialize, XML)](./[34]-Data-Serialization.md)**  
    35. **[Logging (Monolog)](./[35]-Logging.md)**  
    36. **[Command-Line Scripts (CLI SAPI, argv, Symfony Console)](./[36]-CLI-Scripts.md)**  
    37. **[Math & Random Functions](./[37]-Math-and-Random.md)**  

**Web Fundamentals**  
    38. **[Superglobals (`$_GET`, `$_POST`, `$_SERVER`, `$_REQUEST`)](./[38]-Superglobals.md)**  
    39. **[Forms & User Input Handling](./[39]-Forms-and-User-Input-Handling.md)**  
    40. **[File Uploads](./[40]-File-Uploads.md)**  
    41. **[Sessions & Cookies](./[41]-Sessions-and-Cookies.md)**  
    42. **[Headers & Redirects](./[42]-Headers-and-Redirects.md)**  

**Security**  
    43. **[Input Validation & Sanitization](./[43]-Input-Validation-and-Sanitization.md)**  
    44. **[SQL Injection Prevention (prepared statements)](./[44]-SQL-Injection-Prevention.md)**  
    45. **[XSS, CSRF & Common Web Vulnerabilities](./[45]-XSS-CSRF-and-Common-Vulnerabilities.md)**  
    46. **[Password Hashing (password_hash, password_verify)](./[46]-Password-Hashing.md)**  
    47. **[Authentication & Authorization (JWT, OAuth2)](./[47]-Authentication-and-Authorization.md)**  

**Databases**  
    48. **[PDO Fundamentals](./[48]-PDO-Fundamentals.md)**  
    49. **[MySQLi](./[49]-MySQLi.md)**  
    50. **[Working with PostgreSQL](./[50]-PostgreSQL.md)**  
    51. **[Query Builders & ORMs (Eloquent, Doctrine)](./[51]-Query-Builders-and-ORMs.md)**  
    52. **[MongoDB with PHP](./[52]-MongoDB-with-PHP.md)**  
    53. **[Caching with Redis & Memcached](./[53]-Caching-with-Redis-and-Memcached.md)**  

**Frameworks & CMS**  
    54. **[Laravel Fundamentals (routing, controllers, views)](./[54]-Laravel-Fundamentals.md)**  
    55. **[Eloquent ORM & Migrations](./[55]-Eloquent-ORM-and-Migrations.md)**  
    56. **[Blade Templating](./[56]-Blade-Templating.md)**  
    57. **[Symfony Fundamentals](./[57]-Symfony-Fundamentals.md)**  
    58. **[Twig Templating](./[58]-Twig-Templating.md)**  
    59. **[WordPress Development Basics (themes, plugins, hooks)](./[59]-WordPress-Development-Basics.md)**  

**Web Development & APIs**  
    60. **[Building REST APIs](./[60]-Building-REST-APIs.md)**  
    61. **[API Documentation (OpenAPI/Swagger)](./[61]-API-Documentation-OpenAPI.md)**  
    62. **[GraphQL with PHP](./[62]-GraphQL-with-PHP.md)**  
    63. **[Working with External APIs (cURL, Guzzle)](./[63]-Working-with-External-APIs.md)**  
    64. **[WebSockets in PHP (Ratchet, Swoole)](./[64]-WebSockets.md)**  

**Messaging & Event-Driven Systems**  
    65. **[Message Queues (RabbitMQ with php-amqplib)](./[65]-Message-Queues.md)**  
    66. **[Job Queues & Background Processing (Laravel Queues)](./[66]-Job-Queues-and-Background-Processing.md)**  

**Testing & Code Quality**  
    67. **[Testing: PHPUnit](./[67]-Testing-PHPUnit.md)**  
    68. **[Mocking with PHPUnit](./[68]-Mocking-with-PHPUnit.md)**  
    69. **[Behavior-Driven Development (Behat, Pest)](./[69]-Behavior-Driven-Development.md)**  
    70. **[Static Analysis (PHPStan, Psalm)](./[70]-Static-Analysis.md)**  
    71. **[Linters & Formatters (PHP_CodeSniffer, PHP-CS-Fixer)](./[71]-Linters-and-Formatters.md)**  
    72. **[Code Coverage](./[72]-Code-Coverage.md)**  

**Packaging & Deployment**  
    73. **[Deployment Basics (Apache, Nginx, PHP-FPM)](./[73]-Deployment-Basics.md)**  
    74. **[Docker Basics for PHP Apps](./[74]-Docker-Basics.md)**  
    75. **[CI/CD Basics (GitHub Actions)](./[75]-CICD-Basics.md)**  
    76. **[Observability: Logging & Monitoring](./[76]-Observability.md)**  

**Performance & Optimization**  
    77. **[Profiling (Xdebug, Blackfire)](./[77]-Profiling.md)**  
    78. **[OPcache & Performance Tuning](./[78]-OPcache-and-Performance-Tuning.md)**  

**Best Practices**  
    79. **[PSR Standards & Idiomatic PHP](./[79]-Best-Practices-and-PSR-Standards.md)**
