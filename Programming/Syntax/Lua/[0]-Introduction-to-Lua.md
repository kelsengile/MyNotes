[⬅ Back to README](../../../README.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/lua/lua-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Lua</h1>
</div>


Welcome! This is a self-paced course for learning Lua, a lightweight, fast, and embeddable scripting language known for its tiny footprint, simple syntax, and powerful tables. It is used in game development, embedded systems, application scripting, and more. This course targets **Lua 5.4**, and calls out differences for 5.1, 5.2, 5.3, and LuaJIT.

Download: [https://www.lua.org/download.html](https://www.lua.org/download.html)

---

## What is Lua?

Lua lets you:
- Write small, readable scripts with minimal syntax
- Use one flexible data structure, the table, for arrays, dictionaries, objects, and modules
- Treat functions as first-class values with closures
- Build objects, classes, and inheritance using metatables
- Pause and resume code with coroutines
- Embed a scripting language inside C/C++ applications
- Script games and engines such as LÖVE, Roblox (Luau), Defold, and many game mods
- Extend tools such as Neovim, Redis, Nginx/OpenResty, and Wireshark
- Run fast with LuaJIT and its C foreign function interface
- Package and share code with LuaRocks

## Table of Contents

**Getting Started**  

1. **[Installing Lua & First-Time Setup](./[1]-Installation-And-Setup.md)**  
    1.1 What You Need Before You Start  
    1.2 Installing Lua on Windows  
    1.3 Installing Lua on macOS  
    1.4 Installing Lua on Linux  
    1.5 Building Lua from Source  
    1.6 Verifying Your Installation (`lua -v`)  
    1.7 Lua vs LuaJIT vs Luau: Which One Do You Need?  
    1.8 Choosing a Code Editor / IDE (VS Code + Lua Language Server, ZeroBrane, Neovim)  
2. **[Running Code: Scripts, the REPL & Online Tools](./[2]-Running-Lua-Code.md)**  
    2.1 The Lua Interpreter  
    2.2 Running Scripts (`.lua` files)  
    2.3 The Interactive REPL  
    2.4 Command-Line Arguments (`arg` table)  
    2.5 Running Code Inside Other Programs (LÖVE, Neovim, Roblox)  
    2.6 Online Playgrounds  
    2.7 Which Should You Use?  
3. **[Package Management with LuaRocks](./[3]-LuaRocks-And-Packages.md)**  
    3.1 Why Package Management Matters  
    3.2 Installing LuaRocks  
    3.3 Installing, Updating, and Removing Rocks  
    3.4 Local vs Global Installs (`--local`, `--tree`)  
    3.5 Writing a `.rockspec` File  
    3.6 Using Rocks in Your Scripts (`package.path` / `package.cpath`)  

**Core Syntax**  

4. **[Variables & Basic Data Types](./[4]-Variables-And-Data-Types.md)**  
    4.1 What is a Variable?  
    4.2 Naming Rules, Reserved Words & Conventions  
    4.3 Comments (`--` and `--[[ ]]`)  
    4.4 Global vs Local Variables (`local`)  
    4.5 Dynamic Typing  
    4.6 The Eight Basic Types (`nil`, `boolean`, `number`, `string`, `function`, `table`, `userdata`, `thread`)  
    4.7 Type Checking with `type()` and `math.type()`  
    4.8 Type Coercion and Conversion (`tonumber()`, `tostring()`)  
    4.9 Multiple Assignment and Swapping Values  
    4.10 Constants (`<const>`) and Attributes  
5. **[Numbers, Strings & Booleans](./[5]-Numbers-Strings-And-Booleans.md)**  
    5.1 Integers and Floats (Lua 5.3+)  
    5.2 Arithmetic with Numbers  
    5.3 Number Formats (Hex, Scientific Notation)  
    5.4 Strings: Creation and Basics (Quotes, Long Strings `[[ ]]`)  
    5.5 Escape Sequences  
    5.6 String Immutability and Interning  
    5.7 Booleans and Truthiness (Only `nil` and `false` are False)  
    5.8 `nil`: The Absence of a Value  
6. **[Operators & Expressions](./[6]-Operators-And-Expressions.md)**  
    6.1 Arithmetic Operators (`+ - * / // % ^`)  
    6.2 Relational Operators (`== ~= < > <= >=`)  
    6.3 Logical Operators (`and`, `or`, `not`) and Short-Circuiting  
    6.4 The `and` / `or` Ternary Idiom and Its Pitfall  
    6.5 String Concatenation (`..`)  
    6.6 The Length Operator (`#`)  
    6.7 Bitwise Operators (`& | ~ << >>`)  
    6.8 Operator Precedence  
    6.9 Why There Is No `+=` or `++`  
7. **[Conditionals: if, elseif, else](./[7]-Conditionals.md)**  
    7.1 The `if` Statement  
    7.2 `elseif` and `else`  
    7.3 Nested Conditionals  
    7.4 Truthy and Falsy Values in Conditions  
    7.5 Simulating Ternary Expressions  
    7.6 Simulating `switch` with Tables  
    7.7 Guard Clauses and Early Returns  
8. **[Loops: while, repeat, for](./[8]-Loops.md)**  
    8.1 The `while` Loop  
    8.2 The `repeat ... until` Loop  
    8.3 The Numeric `for` Loop  
    8.4 The Generic `for` Loop (`pairs`, `ipairs`)  
    8.5 `break`  
    8.6 `goto` and Simulating `continue`  
    8.7 Nested Loops and Labels  
    8.8 Loop Variable Scope  
9. **[Functions](./[9]-Functions.md)**  
    9.1 Defining and Calling Functions  
    9.2 Parameters, Arguments & Return Values  
    9.3 Multiple Return Values  
    9.4 Variadic Functions (`...`, `select`, `table.pack`, `table.unpack`)  
    9.5 Default Arguments (via `or`)  
    9.6 Named Arguments via Tables  
    9.7 Functions as First-Class Values  
    9.8 Anonymous Functions  
    9.9 Recursion and Proper Tail Calls  
    9.10 Method Syntax (`:` vs `.`)  
10. **[Scope, Closures & Upvalues](./[10]-Scope-And-Closures.md)**  
    10.1 Block Scope and `do ... end`  
    10.2 Lexical Scoping  
    10.3 Global Variables and the `_G` Table  
    10.4 Closures  
    10.5 Upvalues  
    10.6 Counters, Factories, and Private State  
    10.7 Common Closure Pitfalls in Loops  
11. **[String Library & Pattern Matching](./[11]-String-Library.md)**  
    11.1 Basic Methods (`len`, `upper`, `lower`, `sub`, `rep`, `reverse`)  
    11.2 `string.format()` (and `%d`, `%s`, `%f`, `%q`)  
    11.3 Finding Text (`find`, `match`)  
    11.4 Iterating with `gmatch`  
    11.5 Replacing with `gsub`  
    11.6 Lua Patterns (Character Classes, Anchors, Quantifiers, Captures)  
    11.7 Lua Patterns vs Regular Expressions  
    11.8 `string.byte` and `string.char`  
    11.9 The Method Call Syntax on Strings (`("x"):upper()`)  
    11.10 Splitting, Trimming, and Joining Strings  
12. **[Error Handling](./[12]-Error-Handling.md)**  
    12.1 Errors in Lua  
    12.2 `error()` and Error Levels  
    12.3 `assert()`  
    12.4 Protected Calls with `pcall()`  
    12.5 `xpcall()` and Message Handlers  
    12.6 Tracebacks (`debug.traceback`)  
    12.7 Throwing Tables as Custom Error Objects  
    12.8 Return `nil, err` Convention  
    12.9 Reading Common Error Messages  

**Data Structures**  

13. **[Tables](./[13]-Tables.md)**  
    13.1 What is a Table?  
    13.2 Table Constructors  
    13.3 Arrays and 1-Based Indexing  
    13.4 Dictionaries and Record-Style Access (`t.key` vs `t["key"]`)  
    13.5 Mixed Tables  
    13.6 Adding, Updating, and Removing Items  
    13.7 The Length Operator and Holes (`nil` in Arrays)  
    13.8 The `table` Library (`insert`, `remove`, `concat`, `sort`, `unpack`, `move`)  
    13.9 Sorting with Custom Comparators  
    13.10 Nested Tables and Multidimensional Arrays  
    13.11 Copying Tables: Shallow vs Deep  
    13.12 Tables as References  
14. **[Iterating Over Tables](./[14]-Iterating-Over-Tables.md)**  
    14.1 `ipairs` for Sequences  
    14.2 `pairs` for All Keys  
    14.3 Iteration Order Guarantees  
    14.4 `next` and Checking for Empty Tables  
    14.5 Modifying a Table During Iteration  
    14.6 Reverse and Custom Iteration Orders  
15. **[Common Data Structures](./[15]-Common-Data-Structures.md)**  
    15.1 Stacks  
    15.2 Queues and Deques  
    15.3 Sets  
    15.4 Linked Lists  
    15.5 Matrices and Grids  
    15.6 Sparse Arrays  
    15.7 String Buffers (`table.concat`)  
16. **[Metatables & Metamethods](./[16]-Metatables-And-Metamethods.md)**  
    16.1 What is a Metatable?  
    16.2 `setmetatable()` and `getmetatable()`  
    16.3 `__index` (Tables and Functions)  
    16.4 `__newindex`  
    16.5 `rawget()` and `rawset()`  
    16.6 Arithmetic Metamethods (`__add`, `__sub`, `__mul`, `__div`, etc.)  
    16.7 Comparison Metamethods (`__eq`, `__lt`, `__le`)  
    16.8 `__tostring`, `__len`, `__concat`, `__call`  
    16.9 `__pairs`, `__close`, `__gc`, `__mode`, `__metatable`  
    16.10 Read-Only Tables and Default Values  
    16.11 Operator Overloading Example: Vectors  

**Object-Oriented Programming**  

17. **[Objects & Classes with Tables](./[17]-Objects-And-Classes.md)**  
    17.1 Objects as Tables with Functions  
    17.2 The `self` Parameter and Colon Syntax  
    17.3 Building a Class with `__index`  
    17.4 Constructors (`new`)  
    17.5 Instance Fields vs Class Fields  
    17.6 Methods and Class-Level Functions  
    17.7 `__tostring` and Other Metamethods on Classes  
18. **[Inheritance & Polymorphism](./[18]-Inheritance-And-Polymorphism.md)**  
    18.1 What is Inheritance in Lua?  
    18.2 Prototype-Based Inheritance  
    18.3 Subclassing with Metatable Chains  
    18.4 Overriding Methods  
    18.5 Calling a Parent Method (Manual `super`)  
    18.6 Polymorphism and Duck Typing  
    18.7 A Reusable `class()` Helper  
    18.8 Popular Class Libraries (`middleclass`, `30log`, `classic`)  
19. **[Encapsulation & Privacy](./[19]-Encapsulation-And-Privacy.md)**  
    19.1 What is Encapsulation in Lua?  
    19.2 Naming Conventions (`_private`)  
    19.3 Privacy with Closures  
    19.4 Privacy with Proxy Tables  
    19.5 Read-Only Objects  
    19.6 Interfaces via Duck Typing  
20. **[Multiple Inheritance & Mixins](./[20]-Multiple-Inheritance-And-Mixins.md)**  
    20.1 Multiple Inheritance with `__index` Functions  
    20.2 Method Lookup Order  
    20.3 Mixins and Composition  
    20.4 Composition vs Inheritance  

**Advanced Language Features**  

21. **[Iterators & Generators](./[21]-Iterators-and-Generators.md)**  
    21.1 How the Generic `for` Works  
    21.2 Stateless Iterators  
    21.3 Stateful Iterators with Closures  
    21.4 Writing Your Own `range` and `chars` Iterators  
    21.5 Generators with Coroutines  
22. **[Coroutines](./[22]-Coroutines.md)**  
    22.1 What is a Coroutine?  
    22.2 `coroutine.create`, `resume`, `yield`, `status`  
    22.3 `coroutine.wrap`  
    22.4 Passing Values In and Out  
    22.5 Coroutine States (suspended, running, normal, dead)  
    22.6 Error Handling in Coroutines  
    22.7 Producer/Consumer Pattern  
    22.8 Cooperative Multitasking and Simple Schedulers  
    22.9 `coroutine.close` and `coroutine.isyieldable`  
23. **[Modules & `require`](./[23]-Modules-and-Require.md)**  
    23.1 What is a Module?  
    23.2 Writing a Module (Returning a Table)  
    23.3 `require` and `package.loaded`  
    23.4 `package.path` and `package.cpath`  
    23.5 Organizing Multi-File Projects  
    23.6 Circular Dependencies  
    23.7 Module Patterns (Old `module()` vs Modern Style)  
24. **[Environments & Metaprogramming](./[24]-Environments-and-Metaprogramming.md)**  
    24.1 `_G` and Global Access  
    24.2 `_ENV` (Lua 5.2+) and `setfenv`/`getfenv` (5.1)  
    24.3 Detecting Accidental Globals (Strict Mode)  
    24.4 `load`, `loadstring`, `loadfile`, and `dofile`  
    24.5 Sandboxing Untrusted Code  
    24.6 The `debug` Library  
    24.7 Introspection and Reflection  
25. **[Functional Programming in Lua](./[25]-Functional-Programming.md)**  
    25.1 Higher-Order Functions  
    25.2 `map`, `filter`, `reduce` Implementations  
    25.3 Currying and Partial Application  
    25.4 Function Composition  
    25.5 Memoization  
    25.6 Immutability Patterns  
26. **[Memory Management & Garbage Collection](./[26]-Memory-Management.md)**  
    26.1 Automatic Memory Management  
    26.2 How Lua's Garbage Collector Works  
    26.3 `collectgarbage()` Options (`collect`, `count`, `step`, generational/incremental modes)  
    26.4 Weak Tables (`__mode`)  
    26.5 Finalizers (`__gc`)  
    26.6 To-Be-Closed Variables (`<close>`)  
    26.7 Avoiding Memory Leaks  
27. **[Version Differences: Lua 5.1 to 5.4 & LuaJIT](./[27]-Version-Differences.md)**  
    27.1 Lua 5.1 Highlights (and Why It Still Matters)  
    27.2 Lua 5.2: `_ENV`, `goto`, `bit32`  
    27.3 Lua 5.3: Integers, Bitwise Operators, `utf8`  
    27.4 Lua 5.4: `<const>`, `<close>`, Generational GC  
    27.5 LuaJIT: What It Supports (5.1 + Extensions)  
    27.6 Luau (Roblox) Differences  
    27.7 Writing Portable Code Across Versions  

**Standard Library**  

28. **[Math Library & Random Numbers](./[28]-Math-and-Random.md)**  
    28.1 Common Functions (`floor`, `ceil`, `abs`, `max`, `min`, `sqrt`, `huge`)  
    28.2 Trigonometry and Constants (`pi`, `sin`, `cos`, `atan`)  
    28.3 Integer vs Float Math (`math.tointeger`, `math.type`, `//`)  
    28.4 Random Numbers (`math.random`, `math.randomseed`)  
    28.5 Rounding Recipes and Floating-Point Pitfalls  
    28.6 Clamping, Lerping, and Common Game Math Helpers  
29. **[Input, Output & Files](./[29]-IO-and-Files.md)**  
    29.1 `print`, `io.write`, and `io.read`  
    29.2 Opening, Reading, and Writing Files (`io.open`)  
    29.3 Read Modes (`"l"`, `"a"`, `"n"`, `"L"`) and Write Modes  
    29.4 File Handles and `file:close()`  
    29.5 Reading Line by Line (`io.lines`)  
    29.6 Binary Files  
    29.7 `io.stdin`, `io.stdout`, `io.stderr`  
    29.8 Safe File Handling and Error Checks  
30. **[OS, Dates & Times](./[30]-OS-Dates-and-Times.md)**  
    30.1 `os.time()` and `os.clock()`  
    30.2 `os.date()` and Format Strings  
    30.3 Building Date Tables and Converting Back  
    30.4 `os.getenv()`, `os.execute()`, `os.remove()`, `os.rename()`, `os.tmpname()`  
    30.5 `os.exit()`  
    30.6 Timing Code  
31. **[Data Serialization](./[31]-Data-Serialization.md)**  
    31.1 Serializing Tables to Lua Source Code  
    31.2 JSON with `dkjson`, `cjson`, or `lunajson`  
    31.3 CSV Parsing  
    31.4 `string.pack` and `string.unpack`  
    31.5 Saving and Loading Game State  
    31.6 Safe Deserialization with `load` and Sandboxes  
32. **[UTF-8 & Bitwise Operations](./[32]-UTF8-and-Bitwise.md)**  
    32.1 Strings as Byte Sequences  
    32.2 The `utf8` Library (`char`, `codepoint`, `len`, `codes`, `offset`)  
    32.3 Handling Unicode Safely  
    32.4 Bitwise Operators in Practice (Flags and Masks)  
    32.5 `bit` (LuaJIT) and `bit32` (5.2)  

**Embedding & Interoperability**  

33. **[The C API](./[33]-The-C-API.md)**  
    33.1 How Lua and C Talk (the Virtual Stack)  
    33.2 `lua_State`, `luaL_newstate`, and `luaL_openlibs`  
    33.3 Pushing and Reading Values  
    33.4 Calling Lua from C (`lua_call`, `lua_pcall`)  
    33.5 Calling C from Lua (`lua_CFunction`, `lua_register`)  
    33.6 Userdata and Metatables from C  
    33.7 Writing a C Module (`luaopen_*`)  
    33.8 The Registry and References  
34. **[LuaJIT & the FFI Library](./[34]-LuaJIT-and-FFI.md)**  
    34.1 What is LuaJIT?  
    34.2 JIT Compilation Basics  
    34.3 `ffi.cdef` and `ffi.C`  
    34.4 C Types, Structs, and Pointers  
    34.5 Loading Shared Libraries (`ffi.load`)  
    34.6 Performance Considerations  
35. **[Embedding Lua in Applications](./[35]-Embedding-Lua.md)**  
    35.1 Why Embed a Scripting Language?  
    35.2 Designing a Scripting API  
    35.3 Exposing Host Functions and Objects  
    35.4 Sandboxing and Resource Limits  
    35.5 Hot Reloading Scripts  
    35.6 Binding Libraries (`sol2`, `LuaBridge`, `LuaWrapper`, `mlua` for Rust)  

**Lua in Practice**  

36. **[Game Development with LÖVE](./[36]-LOVE2D.md)**  
    36.1 What is LÖVE?  
    36.2 Project Structure (`main.lua`, `conf.lua`)  
    36.3 `love.load`, `love.update`, `love.draw`  
    36.4 Drawing Shapes, Images, and Text  
    36.5 Keyboard, Mouse, and Gamepad Input  
    36.6 Audio  
    36.7 Collision and Physics (`love.physics`)  
    36.8 Building a Simple Game  
37. **[Roblox & Luau](./[37]-Roblox-and-Luau.md)**  
    37.1 What is Luau?  
    37.2 Differences from Standard Lua (Types, `continue`, Compound Assignment)  
    37.3 Type Annotations in Luau  
    37.4 Roblox Services, Instances, and Events  
    37.5 Scripts, LocalScripts, and ModuleScripts  
38. **[Scripting Neovim with Lua](./[38]-Neovim.md)**  
    38.1 Why Lua in Neovim?  
    38.2 `init.lua` and the `vim` Global  
    38.3 Options, Keymaps, and Autocommands  
    38.4 Creating Commands and Functions  
    38.5 Plugin Managers and Writing a Plugin  
39. **[Other Hosts: Defold, Redis, OpenResty & Game Mods](./[39]-Other-Lua-Hosts.md)**  
    39.1 Defold and Solar2D  
    39.2 Redis Scripting with `EVAL`  
    39.3 OpenResty / Nginx  
    39.4 Game Modding (Garry's Mod, Factorio, World of Warcraft, etc.)  
    39.5 Embedded Devices (NodeMCU, ESP32)  

**Testing & Code Quality**  

40. **[Testing in Lua](./[40]-Testing.md)**  
    40.1 Why Write Tests?  
    40.2 Simple `assert`-Based Tests  
    40.3 `busted` Framework (`describe`, `it`, assertions)  
    40.4 Spies, Stubs, and Mocks  
    40.5 `luaunit`  
    40.6 Code Coverage with `luacov`  
41. **[Linters, Formatters & Language Server](./[41]-Linters-and-Formatters.md)**  
    41.1 `luacheck`  
    41.2 `StyLua` and Other Formatters  
    41.3 Lua Language Server (LuaLS) and Type Annotations  
    41.4 Editor Integration  
42. **[Debugging](./[42]-Debugging.md)**  
    42.1 Print Debugging  
    42.2 Reading Stack Traces  
    42.3 The `debug` Library for Debugging  
    42.4 Debuggers (ZeroBrane Studio, `mobdebug`, VS Code Debug Adapters)  
    42.5 Common Pitfalls (Globals, 1-Based Indexes, `nil` Errors)  
43. **[Performance & Profiling](./[43]-Performance-and-Profiling.md)**  
    43.1 Measuring Time with `os.clock()`  
    43.2 Profiling Tools (`LuaProfiler`, `jit.p` in LuaJIT)  
    43.3 Localizing Globals and Function Lookups  
    43.4 Table Preallocation and Reuse  
    43.5 Avoiding String Concatenation in Loops  
    43.6 Reducing Garbage Creation  

**Best Practices**  

44. **[Idiomatic Lua & Style Guide](./[44]-Best-Practices-and-Style.md)**  
    44.1 Naming Conventions  
    44.2 Always Use `local`  
    44.3 Formatting and Indentation  
    44.4 Error Handling Conventions  
    44.5 Organizing Projects and Modules  
    44.6 Documenting with LDoc / LuaLS Annotations  
    44.7 Common Gotchas Checklist  
    44.8 Capstone Projects (Text Adventure, To-Do CLI, Simple LÖVE Game)