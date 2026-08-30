[⬅ Back to README](../../../README.md)

# Desktop Development

Welcome! This is a self-paced course for learning Desktop Development, the practice of building applications that run natively on Windows, macOS, and Linux, whether platform-specific or built once and shared across all three.

---

## What is Desktop Development?

Desktop Development lets you:
- Build native apps for Windows, macOS, and Linux, as well as command-line tools
- Build cross-platform desktop apps with a single codebase using Electron, Qt, .NET MAUI, or Tauri
- Design windowed UIs with menus, toolbars, dialogs, and rich controls
- Handle mouse, keyboard, and shortcut-driven interaction
- Read and write files, manage local databases, and store app settings
- Run background work without freezing the UI
- Integrate with the operating system (tray icons, notifications, IPC, plugins)
- Log, diagnose, and monitor an app in production
- Package, sign, and distribute an app so users can install and auto-update it
- Support multiple languages and keep the app accessible and secure

## Table of Contents

**Getting Started**  

1. **[What is Desktop Development? Native vs Cross-Platform](./[1]-What-is-Desktop-Development.md)**  
    1.1 What Counts as a Desktop App  
    1.2 Native Development  
    1.3 Cross-Platform Development  
    1.4 Choosing an Approach  
2. **[Development Environment & Toolchains](./[2]-Development-Environment.md)**  
    2.1 Editors and IDEs  
    2.2 Compilers, SDKs, and Runtimes  
    2.3 Package Managers  
    2.4 Verifying Your Setup  
3. **[Anatomy of a Desktop App Project](./[3]-Anatomy-of-a-Desktop-App-Project.md)**  
    3.1 Typical Folder Layout  
    3.2 The Entry Point  
    3.3 Resources and Assets  
    3.4 Configuration Files  
4. **[Command-Line Interfaces & Console Apps](./[4]-Command-Line-Interfaces-and-Console-Apps.md)**  
    4.1 Why CLIs Matter in Desktop Development  
    4.2 Reading Arguments and Producing Output  
    4.3 Building Friendlier CLIs  
    4.4 CLIs vs GUI Apps  

**Core Syntax**  

5. **[Variables, Data Types & Operators](./[5]-Variables-and-Data-Types.md)**  
    5.1 Declaring Variables  
    5.2 Common Primitive Types  
    5.3 Constants and Immutability  
    5.4 Operators  
6. **[Control Flow: Conditionals & Loops](./[6]-Control-Flow.md)**  
    6.1 Conditionals  
    6.2 Loops  
    6.3 Guard Clauses and Early Returns  
    6.4 Control Flow and the UI Thread  
7. **[Functions & Scope](./[7]-Functions-and-Scope.md)**  
    7.1 Defining Functions  
    7.2 Parameters: By Value vs By Reference  
    7.3 Scope  
    7.4 Closures and Callbacks  
8. **[Object-Oriented Basics](./[8]-Object-Oriented-Basics.md)**  
    8.1 Classes and Objects  
    8.2 Encapsulation  
    8.3 Inheritance and Polymorphism  
    8.4 Composition Over Inheritance  
9. **[Collections & Data Structures](./[9]-Collections-and-Data-Structures.md)**  
    9.1 Arrays and Lists  
    9.2 Dictionaries / Maps  
    9.3 Sets, Stacks, and Queues  
    9.4 Choosing the Right Structure  

**UI Fundamentals**  

10. **[Windows, Dialogs & the Application Lifecycle](./[10]-Windows-Dialogs-and-App-Lifecycle.md)**  
    10.1 The Main Window  
    10.2 Modal vs Modeless Dialogs  
    10.3 The Application Lifecycle  
    10.4 Single-Instance Apps  
11. **[Layouts & Containers](./[11]-Layouts-and-Containers.md)**  
    11.1 Why Layout Managers Exist  
    11.2 Common Layout Types  
    11.3 Nesting Containers  
    11.4 Responsive Sizing  
12. **[Widgets & Controls](./[12]-Widgets-and-Controls.md)**  
    12.1 What Widgets Are  
    12.2 Common Control Categories  
    12.3 Data-Driven Controls  
    12.4 Custom Controls  
13. **[Styling & Theming](./[13]-Styling-and-Theming.md)**  
    13.1 Separating Style from Structure  
    13.2 Light and Dark Mode  
    13.3 Design Tokens and Consistency  
    13.4 Platform-Native Look and Feel  
14. **[Menus, Toolbars & Status Bars](./[14]-Menus-Toolbars-and-Status-Bars.md)**  
    14.1 Menu Bars  
    14.2 Toolbars  
    14.3 Context Menus  
    14.4 Status Bars  

**Events & Interaction** 

15. **[Event-Driven Programming](./[15]-Event-Driven-Programming.md)**  
    15.1 The Event Loop  
    15.2 Events and Handlers  
    15.3 Event Propagation  
    15.4 Decoupling with Events  
16. **[Handling User Input (Mouse, Keyboard, Shortcuts)](./[16]-Handling-User-Input.md)**  
    16.1 Mouse Events  
    16.2 Keyboard Events  
    16.3 Keyboard Shortcuts and Accelerators  
    16.4 Focus  
17. **[Data Binding & UI Updates](./[17]-Data-Binding-and-UI-Updates.md)**  
    17.1 The Problem Data Binding Solves  
    17.2 One-Way and Two-Way Binding  
    17.3 Observable Data  
    17.4 Avoiding Binding Pitfalls  

**Data & Storage** 

18. **[File I/O & the Filesystem](./[18]-File-IO-and-the-Filesystem.md)**  
    18.1 Reading and Writing Files  
    18.2 Native File Pickers  
    18.3 Platform-Specific Paths  
    18.4 Watching for Changes  
19. **[Local Databases (SQLite)](./[19]-Local-Databases.md)**  
    19.1 Why Desktop Apps Use SQLite  
    19.2 Basic Schema and Queries  
    19.3 Accessing SQLite from Code  
    19.4 Migrations  
20. **[Application Settings & Configuration](./[20]-Application-Settings-and-Configuration.md)**  
    20.1 What Belongs in Settings  
    20.2 Where Settings Live  
    20.3 A Simple Settings Pattern  
    20.4 Sensitive Settings  
21. **[Working with JSON & Serialization](./[21]-Working-with-JSON-and-Serialization.md)**  
    21.1 Why Serialize  
    21.2 JSON Basics  
    21.3 Handling Schema Evolution  
    21.4 Beyond JSON  

**Concurrency**  

22. **[Background Tasks & Threading](./[22]-Background-Tasks-and-Threading.md)**  
    22.1 The UI Thread  
    22.2 Spawning Background Threads  
    22.3 Thread Safety  
    22.4 Thread Pools  
23. **[Async Programming for Responsive UIs](./[23]-Async-Programming-for-Responsive-UIs.md)**  
    23.1 Async vs Threading  
    23.2 async/await Syntax  
    23.3 Cancellation  
    23.4 Error Handling in Async Code  

**Platform-Specific & Cross-Platform Frameworks**  

24. **[Windows Development (WinForms/WPF/.NET)](./[24]-Windows-Development.md)**  
    24.1 The .NET Windows UI Landscape  
    24.2 XAML Basics (WPF/WinUI)  
    24.3 Windows-Specific Integration  
    24.4 Packaging for Windows  
25. **[macOS Development (AppKit/SwiftUI)](./[25]-macOS-Development.md)**  
    25.1 AppKit vs SwiftUI  
    25.2 SwiftUI Basics  
    25.3 macOS Conventions  
    25.4 Sandboxing and Distribution  
26. **[Linux Desktop Development (GTK/Qt)](./[26]-Linux-Desktop-Development.md)**  
    26.1 A Fragmented but Standardized Ecosystem  
    26.2 A Basic GTK Example  
    26.3 Desktop Integration Standards  
    26.4 Packaging Challenges  
27. **[Cross-Platform Frameworks (Electron, Qt, .NET MAUI, Tauri)](./[27]-Cross-Platform-Frameworks.md)**  
    27.1 Electron  
    27.2 Tauri  
    27.3 Qt  
    27.4 .NET MAUI  
    27.5 Comparing the Options  

**System Integration**  

28. **[Working with the Operating System (Notifications, Tray Icons)](./[28]-Working-with-the-OS.md)**  
    28.1 System Notifications  
    28.2 Tray/Menu Bar Icons  
    28.3 Clipboard Access  
    28.4 Other OS Integration Points  
29. **[Inter-Process Communication](./[29]-Inter-Process-Communication.md)**  
    29.1 Why IPC Matters  
    29.2 IPC in Electron  
    29.3 IPC Beyond a Single App  
    29.4 Serialization Across the Boundary  
30. **[Packaging Native Dependencies](./[30]-Packaging-Native-Dependencies.md)**  
    30.1 What Counts as a Native Dependency  
    30.2 Platform and Architecture Matrices  
    30.3 Bundling Native Binaries  
    30.4 Licensing Native Code  
31. **[Plugin & Extension Systems](./[31]-Plugin-and-Extension-Systems.md)**  
    31.1 Why Build a Plugin System  
    31.2 Defining a Plugin Interface  
    31.3 Discovering and Loading Plugins  
    31.4 Sandboxing and Trust  

**Architecture & Best Practices**  

32. **[App Architecture Patterns (MVC, MVVM)](./[32]-App-Architecture-Patterns.md)**  
    32.1 Why Architecture Matters  
    32.2 MVC (Model-View-Controller)  
    32.3 MVVM (Model-View-ViewModel)  
    32.4 Choosing a Pattern  
33. **[Dependency Management & Package Managers](./[33]-Dependency-Management.md)**  
    33.1 What a Package Manager Does  
    33.2 Semantic Versioning  
    33.3 Transitive Dependencies and Conflicts  
    33.4 Auditing and Updating  
34. **[Version Control with Git](./[34]-Version-Control-with-Git.md)**  
    34.1 Why Version Control  
    34.2 Core Workflow  
    34.3 What to Exclude  
    34.4 Merge Conflicts  
35. **[Testing Desktop Applications](./[35]-Testing-Desktop-Applications.md)**  
    35.1 Unit Tests  
    35.2 Integration Tests  
    35.3 UI/End-to-End Tests  
    35.4 The Testing Pyramid  
36. **[Debugging & Profiling](./[36]-Debugging-and-Profiling.md)**  
    36.1 Using a Debugger  
    36.2 Debugging Across Process Boundaries  
    36.3 Profiling Performance  
    36.4 Common Desktop Performance Culprits  
37. **[Logging & Diagnostics](./[37]-Logging-and-Diagnostics.md)**  
    37.1 Why Logging Matters for Desktop Apps  
    37.2 Log Levels  
    37.3 Where Logs Go  
    37.4 Privacy in Logs  

**Distribution**  

38. **[Building & Packaging for Release](./[38]-Building-and-Packaging-for-Release.md)**  
    38.1 Debug vs Release Builds  
    38.2 Bundling Everything the App Needs  
    38.3 Self-Contained vs Framework-Dependent  
    38.4 Multi-Platform Builds  
39. **[Code Signing & Notarization](./[39]-Code-Signing-and-Notarization.md)**  
    39.1 Why Sign an App  
    39.2 Windows Code Signing  
    39.3 macOS Signing and Notarization  
    39.4 Linux Signing  
40. **[Installers & Auto-Updates](./[40]-Installers-and-Auto-Updates.md)**  
    40.1 Installer Formats  
    40.2 Why Auto-Update Matters  
    40.3 Implementing Auto-Updates  
    40.4 Update Safety  
41. **[Distributing via App Stores & Direct Download](./[41]-Distributing-via-App-Stores.md)**  
    41.1 App Store Distribution  
    41.2 Direct Download Distribution  
    41.3 Choosing a Distribution Strategy  
    41.4 Store Submission Basics  

**Best Practices**  

42. **[Desktop Accessibility](./[42]-Desktop-Accessibility.md)**  
    42.1 Why Accessibility Matters  
    42.2 Keyboard Navigation  
    42.3 Screen Reader Support  
    42.4 Visual and Cognitive Accessibility  
43. **[Localization & Internationalization](./[43]-Localization-and-Internationalization.md)**  
    43.1 Internationalization vs Localization  
    43.2 Externalizing Strings  
    43.3 Locale-Aware Formatting  
    43.4 Layout for Different Languages  
44. **[Performance Optimization](./[44]-Performance-Optimization.md)**  
    44.1 Startup Time  
    44.2 UI Responsiveness  
    44.3 Memory Usage  
    44.4 Measuring Before Optimizing  
45. **[Desktop App Security Basics](./[45]-Desktop-App-Security-Basics.md)**  
    45.1 The Desktop Threat Model  
    45.2 Securing Multi-Process Frameworks  
    45.3 Handling Untrusted Input  
    45.4 Secrets and Credentials  
46. **[Crash Reporting & Analytics](./[46]-Crash-Reporting-and-Analytics.md)**
    46.1 Why Crash Reporting Matters  
    46.2 Capturing Crash Data  
    46.3 Product Analytics  
    46.4 Closing the Loop  