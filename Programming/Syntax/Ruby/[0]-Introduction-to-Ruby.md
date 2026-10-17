[⬅ Back to README](../../../README.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/ruby/ruby-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Ruby</h1>
</div>

Welcome! This is a self-paced course for learning Ruby, a dynamic, object-oriented programming language designed for programmer happiness, with clean, readable syntax and a powerful, flexible object model that powers everything from quick scripts to large web applications.

Download: [https://www.ruby-lang.org/en/downloads/](https://www.ruby-lang.org/en/downloads/)

---

## What is Ruby?

Ruby lets you:
- Write expressive, readable code that often reads like plain English
- Treat everything as an object, from numbers to classes themselves
- Use blocks, procs, and lambdas for powerful, flexible iteration and callbacks
- Model your programs with classes, modules, and mixins
- Reshape your own code at runtime with metaprogramming
- Process text, files, and data quickly with a rich standard library and built-in regular expressions
- Automate tasks and write command-line tools with minimal boilerplate
- Share and reuse code through RubyGems and Bundler
- Build web applications and APIs with frameworks like Rails and Sinatra
- Write concurrent programs with threads, fibers, and ractors
- Test your code thoroughly with Minitest and RSpec

## Table of Contents


**Getting Started**  


1. **[Installing Ruby (Installers, rbenv, RVM, asdf)](./[1]-Installation-and-Setup.md)**  
    1.1 Ruby Versions and Implementations  
    1.2 Installing on Windows (RubyInstaller)  
    1.3 Installing on macOS and Linux  
    1.4 Version Managers: rbenv, RVM, asdf  
    1.5 Verifying Your Installation  
2. **[Running Ruby: IRB, Scripts & the REPL](./[2]-Running-Ruby-Code.md)**  
    2.1 Interactive Ruby (IRB)  
    2.2 Running a Script File  
    2.3 Shebang Lines and Executable Scripts  
    2.4 One-Liners with ruby -e  
    2.5 How Ruby Interprets Your Code  
3. **[Your Development Environment (editors, Bundler, RuboCop, debugger)](./[3]-Development-Environment.md)**  
    3.1 Choosing an Editor or IDE  
    3.2 Ruby Language Servers and Extensions  
    3.3 Linting and Formatting with RuboCop  
    3.4 Bundler and the Gemfile at a Glance  
    3.5 A Recommended Project Layout  
4. **[Ruby Basics: Syntax, Comments, Output & Input](./[4]-Ruby-Basics.md)**  
    4.1 Your First Program  
    4.2 Everything Is an Object  
    4.3 Comments and Documentation Comments  
    4.4 puts, print, p, and pp  
    4.5 Reading Input with gets  
    4.6 Naming Conventions  

**Core Syntax**  


5. **[Variables & Constants (local, instance, class, global)](./[5]-Variables-and-Constants.md)**  
    5.1 What Is a Variable?  
    5.2 Dynamic Typing  
    5.3 Local, Instance, Class, and Global Variables  
    5.4 Constants and Reassignment Warnings  
    5.5 Parallel Assignment and Swapping  
6. **[Numbers (Integer, Float, Rational, Complex, BigDecimal)](./[6]-Numbers.md)**  
    6.1 Integers and Arbitrary Precision  
    6.2 Floats and Precision Issues  
    6.3 Rational and Complex Numbers  
    6.4 BigDecimal for Money  
    6.5 Number Methods and Conversions  
7. **[Strings (interpolation, heredocs, formatting, encodings)](./[7]-Strings.md)**  
    7.1 Single vs Double Quotes  
    7.2 String Interpolation  
    7.3 Heredocs  
    7.4 Common String Methods  
    7.5 Formatting with format and printf  
    7.6 Mutability and Frozen String Literals  
    7.7 Encodings and Unicode  
8. **[Symbols](./[8]-Symbols.md)**  
    8.1 What Is a Symbol?  
    8.2 Symbols vs Strings  
    8.3 Symbols as Hash Keys  
    8.4 Converting Between Symbols and Strings  
9. **[Operators & Expressions (arithmetic, comparison, logical, ranges, safe navigation)](./[9]-Operators-and-Expressions.md)**  
    9.1 Arithmetic Operators  
    9.2 Comparison Operators and the Spaceship (<=>)  
    9.3 Logical Operators: &&, ||, and, or, not  
    9.4 Assignment and ||= / &&=  
    9.5 The Safe Navigation Operator (&.)  
    9.6 Operator Precedence  
10. **[Conditionals (if, unless, case/when, ternary)](./[10]-Conditionals.md)**  
    10.1 if, elsif, and else  
    10.2 unless  
    10.3 case / when  
    10.4 The Ternary Operator  
    10.5 Statement Modifiers (one-line if/unless)  
    10.6 Conditionals Return Values  
11. **[Loops & Iteration (while, until, for, loop, times, each)](./[11]-Loops-and-Iteration.md)**  
    11.1 while and until  
    11.2 The for Loop  
    11.3 loop do  
    11.4 times, upto, downto, and step  
    11.5 break, next, and redo  
    11.6 Why Rubyists Prefer each  
12. **[Methods (arguments, keyword arguments, return values)](./[12]-Methods.md)**  
    12.1 Defining and Calling Methods  
    12.2 Default and Optional Arguments  
    12.3 Keyword Arguments  
    12.4 Splat (*) and Double Splat (**)  
    12.5 Implicit Return Values  
    12.6 Predicate (?) and Bang (!) Methods  
    12.7 Endless Methods and Method Aliasing  
    12.8 Recursion  
13. **[Blocks, Procs & Lambdas (yield, closures, &:symbol)](./[13]-Blocks-Procs-and-Lambdas.md)**  
    13.1 What Is a Block?  
    13.2 yield and block_given?  
    13.3 Procs and Lambdas  
    13.4 Proc vs Lambda Differences  
    13.5 Closures and Variable Scope  
    13.6 Symbol to Proc (&:name) and Method Objects  
    13.7 Currying and Composition  
14. **[Arrays](./[14]-Arrays.md)**  
    14.1 Creating Arrays  
    14.2 Accessing and Slicing Elements  
    14.3 Adding, Removing, and Modifying  
    14.4 Iterating and Transforming  
    14.5 Sorting, Searching, and Filtering  
    14.6 Multidimensional Arrays  
    14.7 Destructuring and Splatting Arrays  
15. **[Hashes](./[15]-Hashes.md)**  
    15.1 Creating Hashes  
    15.2 Reading and Writing Keys  
    15.3 Default Values  
    15.4 Iterating Over Hashes  
    15.5 Transforming and Merging Hashes  
    15.6 Nested Hashes  
    15.7 Hash Shorthand Syntax  
16. **[Ranges & Sets](./[16]-Ranges-and-Sets.md)**  
    16.1 Inclusive and Exclusive Ranges  
    16.2 Endless and Beginless Ranges  
    16.3 Ranges in Conditions and case  
    16.4 Working with Set  
    16.5 Set Operations: Union, Intersection, Difference  
17. **[Enumerable & Enumerators (map, select, reduce, lazy)](./[17]-Enumerable-and-Enumerators.md)**  
    17.1 The Enumerable Module  
    17.2 map, select, reject, and find  
    17.3 reduce / inject and sum  
    17.4 group_by, partition, tally, and each_with_object  
    17.5 Enumerators and External Iteration  
    17.6 Lazy Enumerators  
    17.7 Making Your Own Class Enumerable  
18. **[Truthiness, nil & Equality](./[18]-Truthiness-nil-and-Equality.md)**  
    18.1 What Counts as True and False  
    18.2 nil and NilClass  
    18.3 ==, eql?, equal?, and ===  
    18.4 Object Identity  
    18.5 Avoiding nil Errors  
19. **[Pattern Matching (case/in, destructuring)](./[19]-Pattern-Matching.md)**  
    19.1 case / in Basics  
    19.2 Array and Hash Patterns  
    19.3 Guards, Alternatives, and the Pin Operator  
    19.4 Find Patterns  
    19.5 Custom Matching with deconstruct and deconstruct_keys  
    19.6 Standalone Matching with => and in  
20. **[Error Handling & Exceptions (begin/rescue/ensure, raise, retry)](./[20]-Error-Handling.md)**  
    20.1 What Is an Exception?  
    20.2 begin / rescue / else / ensure  
    20.3 raise and Re-raising  
    20.4 retry  
    20.5 The Exception Hierarchy  
    20.6 Custom Exception Classes  
    20.7 catch and throw  
21. **[Regular Expressions](./[21]-Regular-Expressions.md)**  
    21.1 Regex Literals and Matching  
    21.2 Character Classes, Anchors, and Quantifiers  
    21.3 Capture Groups and Named Captures  
    21.4 match, scan, sub, and gsub  
    21.5 Special Variables and MatchData  
    21.6 Common Regex Pitfalls  

**Object-Oriented Ruby**  


22. **[Classes & Objects](./[22]-Classes-and-Objects.md)**  
    22.1 Defining a Class  
    22.2 initialize and Instance Variables  
    22.3 Instance Methods  
    22.4 self  
    22.5 to_s and inspect  
    22.6 Object Equality and hash  
23. **[Attributes & Accessors (attr_reader, attr_writer, attr_accessor)](./[23]-Attributes-and-Accessors.md)**  
    23.1 Getters and Setters  
    23.2 attr_reader, attr_writer, attr_accessor  
    23.3 Custom Setters with Validation  
    23.4 Keyword Arguments in Constructors  
24. **[Inheritance & super](./[24]-Inheritance-and-super.md)**  
    24.1 Subclassing  
    24.2 Overriding Methods  
    24.3 Calling super  
    24.4 Abstract Classes by Convention  
    24.5 Inheritance vs Composition  
25. **[Modules, Mixins & Namespaces (include, extend, prepend)](./[25]-Modules-and-Mixins.md)**  
    25.1 What Is a Module?  
    25.2 Modules as Namespaces  
    25.3 include, extend, and prepend  
    25.4 Mixins vs Inheritance  
    25.5 Module Functions and Constants  
    25.6 Comparable and Enumerable as Mixins  
26. **[Method Visibility (public, private, protected)](./[26]-Method-Visibility.md)**  
    26.1 public, private, and protected  
    26.2 private_constant  
    26.3 Private Methods and self  
    26.4 Choosing a Visibility  
27. **[Class Methods, Class Variables & Singletons](./[27]-Class-Methods-and-Singletons.md)**  
    27.1 Class Methods (self.method)  
    27.2 class << self  
    27.3 Class Instance Variables vs Class Variables  
    27.4 The Singleton Pattern  
    27.5 Singleton Methods on Objects  
28. **[Polymorphism & Duck Typing](./[28]-Polymorphism-and-Duck-Typing.md)**  
    28.1 Duck Typing  
    28.2 respond_to?  
    28.3 Polymorphism Through Inheritance and Modules  
    28.4 Coercion and Implicit Conversion (to_str, to_ary, coerce)  
29. **[The Ruby Object Model (method lookup, ancestors, singleton classes)](./[29]-Object-Model.md)**  
    29.1 Classes Are Objects  
    29.2 Method Lookup and the Ancestors Chain  
    29.3 Singleton Classes (Eigenclasses)  
    29.4 Open Classes and Monkey Patching  
    29.5 Kernel, Object, and BasicObject  
30. **[Operator Overloading & Comparable Objects](./[30]-Operator-Overloading.md)**  
    30.1 Defining Operators (+, -, ==, [], <<)  
    30.2 <=> and Comparable  
    30.3 Unary Operators  
    30.4 Conversion Methods: to_s, to_i, to_a, to_h  
31. **[Struct, Data & OpenStruct](./[31]-Struct-Data-and-OpenStruct.md)**  
    31.1 Struct Basics  
    31.2 Keyword-Init Structs  
    31.3 Data for Immutable Value Objects  
    31.4 OpenStruct and Its Trade-offs  
32. **[Immutability, Copying & Freezing (freeze, dup, clone)](./[32]-Immutability-and-Copying.md)**  
    32.1 freeze and frozen?  
    32.2 dup vs clone  
    32.3 Shallow vs Deep Copies  
    32.4 Immutable Design  
    32.5 Object Mutation Pitfalls  
33. **[Refinements](./[33]-Refinements.md)**  
    33.1 Why Refinements Exist  
    33.2 refine and using  
    33.3 Scope Rules  
    33.4 Refinements vs Monkey Patching  

**Metaprogramming**  

34. **[Reflection & Introspection (`methods`, `instance_variables`, `ancestors`, `Method` objects)](./[34]-Reflection-and-Introspection.md)**  
35. **[Dynamic Methods (`send`, `public_send`, `define_method`, `method_missing`, `respond_to_missing?`)](./[35]-Dynamic-Methods.md)**  
36. **[The eval Family (`instance_eval`, `class_eval`, `module_eval`, `binding`, `eval`)](./[36]-The-Eval-Family.md)**  
37. **[Hooks & Callbacks (`inherited`, `included`, `extended`, `prepended`, `method_added`)](./[37]-Hooks-and-Callbacks.md)**  
38. **[Dynamic Constants & Variables (`const_get`, `instance_variable_get/set`, `class_variable_get`)](./[38]-Dynamic-Constants-and-Variables.md)**  
39. **[Building DSLs (blocks, `instance_exec`, fluent interfaces)](./[39]-Building-DSLs.md)**  
40. **[Metaprogramming Best Practices & Pitfalls](./[40]-Metaprogramming-Best-Practices.md)**  

**Organizing Code, Gems & Bundler**  

41. **[Requiring Code (`require`, `require_relative`, `load`, `autoload`, `$LOAD_PATH`)](./[41]-Requiring-Code.md)**  
42. **[RubyGems (installing, searching, versioning, `gem` command)](./[42]-RubyGems.md)**  
43. **[Bundler & the Gemfile (`Gemfile.lock`, groups, `bundle exec`, version constraints)](./[43]-Bundler-and-Gemfile.md)**  
44. **[Project Structure & Autoloading (Zeitwerk, file naming, namespaces)](./[44]-Project-Structure-and-Autoloading.md)**  
45. **[Creating Your Own Gem (`bundle gem`, gemspec, semantic versioning)](./[45]-Creating-a-Gem.md)**  
46. **[Publishing & Maintaining Gems (RubyGems.org, changelogs, deprecation)](./[46]-Publishing-Gems.md)**  

**Standard Library**  

47. **[File & Directory I/O (`File`, `IO`, `Dir`, `File.open`, `File.read`, `File.write`)](./[47]-File-and-Directory-IO.md)**  
48. **[Paths & File Utilities (`Pathname`, `FileUtils`, `Tempfile`, `Find`)](./[48]-Paths-and-File-Utilities.md)**  
49. **[Time & Date (`Time`, `Date`, `DateTime`, `strftime`, time zones)](./[49]-Time-and-Date.md)**  
50. **[Data Formats (`JSON`, `YAML`, `CSV`, `Marshal`, `Psych`)](./[50]-Data-Formats.md)**  
51. **[Templating with ERB](./[51]-ERB-Templating.md)**  
52. **[Math, Randomness & Numeric Tools (`Math`, `Random`, `SecureRandom`, `Integer.sqrt`)](./[52]-Math-and-Randomness.md)**  
53. **[Logging (`Logger`, log levels, formatters)](./[53]-Logging.md)**  
54. **[Command-Line Arguments & Options (`ARGV`, `OptionParser`, `ENV`, `$stdin`/`$stdout`)](./[54]-Command-Line-Arguments.md)**  
55. **[Running Shell Commands (`system`, backticks, `Open3`, `spawn`, exit statuses)](./[55]-Running-Shell-Commands.md)**  
56. **[Networking Basics (`Net::HTTP`, `URI`, `Socket`, `TCPServer`, `open-uri`)](./[56]-Networking-Basics.md)**  
57. **[Handy Utility Libraries (`Set`, `Forwardable`, `Observable`, `Singleton`, `Timeout`, `StringIO`, `Digest`, `Benchmark`)](./[57]-Utility-Libraries.md)**  
58. **[Encodings, Unicode & Text Processing (`String#encode`, `unicode_normalize`, `StringScanner`)](./[58]-Encodings-and-Text-Processing.md)**  

**Concurrency & Parallelism**  

59. **[Threads (`Thread`, `join`, thread-local variables)](./[59]-Threads.md)**  
60. **[Synchronization (`Mutex`, `Queue`, `ConditionVariable`, `Monitor`)](./[60]-Synchronization.md)**  
61. **[The Global VM Lock (GVL) & What It Means for Performance](./[61]-GVL.md)**  
62. **[Fibers & the Fiber Scheduler (`Fiber`, `Enumerator`, non-blocking I/O)](./[62]-Fibers.md)**  
63. **[Ractors (isolation, message passing, parallel execution)](./[63]-Ractors.md)**  
64. **[Processes & `fork` (`Process`, `Process.wait`, pipes, `IO.popen`)](./[64]-Processes-and-Fork.md)**  
65. **[Concurrency Libraries (`concurrent-ruby`, `async`, thread pools, promises)](./[65]-Concurrency-Libraries.md)**  

**Testing & Debugging**  

66. **[Testing Fundamentals (TDD, BDD, the test pyramid)](./[66]-Testing-Fundamentals.md)**  
67. **[Unit Testing with Minitest (assertions, setup/teardown, test suites)](./[67]-Minitest.md)**  
68. **[Behavior-Driven Testing with RSpec (`describe`, `it`, matchers, `let`, `before`)](./[68]-RSpec.md)**  
69. **[Mocks, Stubs & Test Doubles (Minitest mocks, RSpec doubles, WebMock, VCR)](./[69]-Mocks-and-Stubs.md)**  
70. **[Test Data & Fixtures (FactoryBot, Faker, fixtures)](./[70]-Test-Data-and-Fixtures.md)**  
71. **[Debugging (`binding.irb`, the `debug` gem, `pry`, `byebug`, `p`/`pp`, stack traces)](./[71]-Debugging.md)**  
72. **[Code Coverage & Static Analysis (SimpleCov, RuboCop, Reek, Brakeman)](./[72]-Coverage-and-Static-Analysis.md)**  

**Type Checking**  

73. **[Gradual Typing in Ruby (why types, trade-offs)](./[73]-Gradual-Typing.md)**  
74. **[RBS & Steep (type signatures, `rbs` CLI, `steep check`)](./[74]-RBS-and-Steep.md)**  
75. **[Sorbet & TypeProf (`sig`, `T.let`, inferred types)](./[75]-Sorbet-and-TypeProf.md)**  

**Performance & Internals**  

76. **[Benchmarking & Profiling (`Benchmark`, `benchmark-ips`, `stackprof`, `ruby-prof`)](./[76]-Benchmarking-and-Profiling.md)**  
77. **[Memory & Garbage Collection (`GC`, `ObjectSpace`, `memory_profiler`, tuning)](./[77]-Memory-and-Garbage-Collection.md)**  
78. **[YJIT & JIT Compilation](./[78]-YJIT-and-JIT.md)**  
79. **[Writing Faster Ruby (algorithms, `freeze`, avoiding allocations, `Enumerable` costs)](./[79]-Writing-Faster-Ruby.md)**  
80. **[C Extensions & FFI (`Fiddle`, `ffi` gem, native extensions)](./[80]-C-Extensions-and-FFI.md)**  
81. **[Ruby Implementations (CRuby/MRI, JRuby, TruffleRuby, mruby)](./[81]-Ruby-Implementations.md)**  

**Ruby in the Real World**  

82. **[Scripting & Automation (file processing, cron jobs, one-off scripts)](./[82]-Scripting-and-Automation.md)**  
83. **[Task Automation with Rake (`Rakefile`, tasks, dependencies, namespaces)](./[83]-Rake.md)**  
84. **[Building Command-Line Apps (Thor, TTY toolkit, colorized output, exit codes)](./[84]-Building-CLI-Apps.md)**  
85. **[Web Fundamentals with Rack (middleware, request/response cycle, `config.ru`)](./[85]-Rack.md)**  
86. **[Lightweight Web Apps with Sinatra (routes, templates, JSON APIs)](./[86]-Sinatra.md)**  
87. **[Introduction to Ruby on Rails (MVC, conventions, generators, where Ruby meets Rails)](./[87]-Introduction-to-Rails.md)**  
88. **[Working with Databases (SQLite3, `pg`, ActiveRecord, Sequel, migrations)](./[88]-Working-with-Databases.md)**  
89. **[Consuming & Building APIs (HTTP clients like Faraday, REST, JSON, authentication)](./[89]-APIs.md)**  
90. **[Background Jobs & Scheduling (Sidekiq, Active Job, cron-style scheduling)](./[90]-Background-Jobs.md)**  
91. **[Web Scraping & Data Processing (Nokogiri, CSV pipelines)](./[91]-Scraping-and-Data-Processing.md)**  
92. **[Deploying Ruby Apps (Docker, Puma, environment config, hosting options)](./[92]-Deploying-Ruby-Apps.md)**  

**Best Practices**  

93. **[Ruby Style Guide & Idiomatic Ruby (community style guide, RuboCop conventions)](./[93]-Style-and-Idioms.md)**  
94. **[Design Principles & Patterns in Ruby (SOLID, composition, service objects, decorators)](./[94]-Design-Patterns.md)**  
95. **[Documentation (RDoc, YARD, README, inline comments)](./[95]-Documentation.md)**  
96. **[Secure Ruby Coding Practices (input validation, injection, `eval` risks, dependency auditing)](./[96]-Secure-Coding-Practices.md)**  
97. **[Managing Ruby Versions & Upgrading (deprecations, compatibility, `.ruby-version`)](./[97]-Versions-and-Upgrading.md)**  
98. **[Refactoring & Code Smells (small methods, readability, dead code)](./[98]-Refactoring-and-Code-Smells.md)**