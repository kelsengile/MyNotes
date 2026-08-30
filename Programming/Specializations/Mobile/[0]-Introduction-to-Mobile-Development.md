[⬅ Back to README](../../../README.md)

# Mobile Development

Welcome! This is a self-paced course for learning Mobile Development, the practice of building apps that run on phones and tablets, whether natively per-platform or across platforms with a shared codebase.

---

## What is Mobile Development?

Mobile Development lets you:
- Build native apps for iOS (Swift) and Android (Kotlin)
- Build cross-platform apps with a single codebase using Flutter or React Native
- Design touch-friendly UIs that adapt to different screen sizes, orientations, and gestures
- Manage state, navigation, and user input across multiple screens
- Persist data locally and sync it with remote APIs
- Access device features like the camera, GPS, sensors, and push notifications
- Run background work, deep link into your app, and support multiple languages
- Test, debug, and profile apps on real devices and simulators
- Package, sign, and publish apps to the App Store and Google Play
- Track usage with analytics and grow an app with in-app purchases

## Table of Contents

**Getting Started**  

1. **[What is Mobile Development? Native vs Cross-Platform](./[1]-What-is-Mobile-Development.md)**  
    1.1 What is Mobile Development?  
    1.2 Native Development  
    1.3 Cross-Platform Development  
    1.4 Choosing an Approach  
2. **[Development Environment & Toolchains (Xcode, Android Studio, Flutter/RN CLI)](./%5B2%5D-Development-Environment%20%282%29.md)**  
    2.1 Xcode (iOS)  
    2.2 Android Studio  
    2.3 Cross-Platform CLIs  
    2.4 Simulators, Emulators, and Physical Devices  
    2.5 Version Control  
3. **[Anatomy of a Mobile App Project](./[3]-Anatomy-of-a-Mobile-App-Project.md)**  
    3.1 The Entry Point  
    3.2 Folder Structure  
    3.3 The Manifest / Configuration Files  
    3.4 Build Outputs  

**Core Syntax**  

4. **[Variables, Data Types & Operators](./[4]-Variables-and-Data-Types.md)**  
    4.1 Declaring Variables  
    4.2 Common Data Types  
    4.3 Operators  
    4.4 Type Inference vs Explicit Types  
5. **[Control Flow: Conditionals & Loops](./[5]-Control-Flow.md)**  
    5.1 Conditionals  
    5.2 Loops  
    5.3 Guard Clauses and Early Returns  
6. **[Functions & Methods](./[6]-Functions-and-Methods.md)**  
    6.1 Defining Functions  
    6.2 Named and Default Parameters  
    6.3 Closures / Lambdas / Callbacks  
    6.4 Async Functions (Preview)  
7. **[Classes, Objects & Object-Oriented Basics](./[7]-Classes-and-Objects.md)**  
    7.1 Classes and Instances  
    7.2 Inheritance  
    7.3 Interfaces / Protocols  
    7.4 Structs vs Classes (Value vs Reference Types)  
8. **[Collections: Arrays, Lists & Maps](./[8]-Collections.md)**  
    8.1 Arrays and Lists  
    8.2 Maps / Dictionaries  
    8.3 Sets  
    8.4 Functional Collection Operations  

**UI Fundamentals**  

9. **[Screens, Views & Layouts](./[9]-Screens-Views-and-Layouts.md)**  
    9.1 What is a Screen?  
    9.2 The View Tree  
    9.3 Layout Building Blocks  
    9.4 Declarative vs Imperative UI  
10. **[Widgets & Components](./[10]-Widgets-and-Components.md)**  
    10.1 What is a Widget/Component?  
    10.2 Common Built-In Widgets  
    10.3 Building Custom, Reusable Components  
    10.4 Component Props / Parameters  
11. **[Styling, Theming & Typography](./[11]-Styling-and-Theming.md)**  
    11.1 Applying Styles  
    11.2 Themes  
    11.3 Dark Mode  
    11.4 Typography Basics  
12. **[Responsive Layouts & Screen Sizes](./[12]-Responsive-Layouts.md)**  
    12.1 Why Responsiveness Matters  
    12.2 Flexible Sizing  
    12.3 Breakpoints and Adaptive Layouts  
    12.4 Safe Areas and Device Quirks  
    12.5 Density-Independent Units  
13. **[Navigation Between Screens](./[13]-Navigation-Between-Screens.md)**  
    13.1 The Navigation Stack  
    13.2 Passing Data Between Screens  
    13.3 Tabs and Drawers  
    13.4 Named Routes and Deep Linking (Preview)  
14. **[Animations & Gestures](./[14]-Animations-and-Gestures.md)**  
    14.1 Why Animation Matters  
    14.2 Implicit vs Explicit Animations  
    14.3 Gesture Recognition  
    14.4 Common Gesture-Driven Patterns  

**State & Data**  

15. **[Managing UI State](./[15]-Managing-UI-State.md)**  
    15.1 What is "State"?  
    15.2 Local Component State  
    15.3 Lifting State Up  
    15.4 Global / App-Wide State  
16. **[Handling User Input & Forms](./[16]-Handling-User-Input-and-Forms.md)**  
    16.1 Text Input Basics  
    16.2 Input Types and Keyboards  
    16.3 Validation  
    16.4 Building a Complete Form  
17. **[Local Storage & Persistence](./[17]-Local-Storage-and-Persistence.md)**  
    17.1 Why Persist Data Locally?  
    17.2 Key-Value Storage  
    17.3 File Storage  
    17.4 Secure Storage  
    17.5 Caching Strategy  
18. **[Working with Databases (SQLite, Realm)](./[18]-Working-with-Databases.md)**  
    18.1 When You Need a Real Database  
    18.2 SQLite  
    18.3 NoSQL / Object Databases: Realm  
    18.4 Migrations  

**Networking**  

19. **[Making Network Requests (REST APIs)](./[19]-Making-Network-Requests.md)**  
    19.1 What is a REST API?  
    19.2 Making a Request  
    19.3 Headers and Authentication  
    19.4 API Clients and Base Configuration  
20. **[Working with JSON](./[20]-Working-with-JSON.md)**  
    20.1 What is JSON?  
    20.2 Parsing JSON into Objects  
    20.3 Encoding Objects Back to JSON  
    20.4 Handling Optional and Unexpected Fields  
21. **[Async Programming in Mobile Apps](./[21]-Async-Programming.md)**  
    21.1 The Main Thread  
    21.2 async/await  
    21.3 Futures, Promises, and Coroutines  
    21.4 Running Work in Parallel  
    21.5 Background Threads for Heavy Work  
22. **[Handling Errors & Offline States](./[22]-Handling-Errors-and-Offline-States.md)**  
    22.1 Why Mobile Networking Fails Often  
    22.2 Try/Catch and Error Types  
    22.3 UI States: Loading, Error, Empty, Success  
    22.4 Detecting Connectivity and Offline Support  

**Device Features**  

23. **[Camera & Media Access](./[23]-Camera-and-Media-Access.md)**  
    23.1 Requesting Camera Access  
    23.2 Picking from the Photo Library  
    23.3 Displaying and Uploading Media  
    23.4 Video and Audio  
24. **[Location & Maps](./[24]-Location-and-Maps.md)**  
    24.1 Requesting Location Permission  
    24.2 Getting the Current Location  
    24.3 Displaying Maps  
    24.4 Geocoding  
    24.5 Battery and Accuracy Tradeoffs  
25. **[Push Notifications](./[25]-Push-Notifications.md)**  
    25.1 How Push Notifications Work  
    25.2 Registering for Push  
    25.3 Local vs Remote Notifications  
    25.4 Handling Notification Taps  
    25.5 Notification Best Practices  
26. **[Sensors & Permissions](./[26]-Sensors-and-Permissions.md)**  
    26.1 Common Device Sensors  
    26.2 The Permission Model  
    26.3 Requesting Permissions Gracefully  
    26.4 Biometric Authentication  
27. **[Deep Linking & App Links](./[27]-Deep-Linking-and-App-Links.md)**  
    27.1 What is a Deep Link?  
    27.2 Custom URL Schemes vs Universal/App Links  
    27.3 Handling an Incoming Link  
    27.4 Deferred Deep Linking  

**Background & System Integration**  

28. **[Background Tasks & Services](./[28]-Background-Tasks-and-Services.md)**  
    28.1 The App Lifecycle  
    28.2 Why Background Work is Restricted  
    28.3 Background Work APIs  
    28.4 Common Background Use Cases  
29. **[Localization & Internationalization](./[29]-Localization-and-Internationalization.md)**  
    29.1 Internationalization (i18n) vs Localization (l10n)  
    29.2 Externalizing Strings  
    29.3 Formatting Dates, Numbers, and Currency  
    29.4 Layout Considerations  

**Platform-Specific Development**  

30. **[Introduction to iOS Development (Swift, SwiftUI/UIKit)](./[30]-Introduction-to-iOS-Development.md)**  
    30.1 The Swift Language  
    30.2 SwiftUI vs UIKit  
    30.3 Xcode Project Structure  
    30.4 iOS-Specific Concepts  
31. **[Introduction to Android Development (Kotlin, Jetpack Compose/XML)](./[31]-Introduction-to-Android-Development.md)**  
    31.1 The Kotlin Language  
    31.2 Jetpack Compose vs the XML View System  
    31.3 Android Project Structure  
    31.4 Android-Specific Concepts  
32. **[Cross-Platform Frameworks (Flutter, React Native)](./[32]-Cross-Platform-Frameworks.md)**  
    32.1 Why Cross-Platform?  
    32.2 Flutter  
    32.3 React Native  
    32.4 Choosing a Cross-Platform Approach  

**Architecture & Best Practices**  

33. **[App Architecture Patterns (MVC, MVVM, MVI)](./[33]-App-Architecture-Patterns.md)**  
    33.1 Why Architecture Matters  
    33.2 MVC (Model-View-Controller)  
    33.3 MVVM (Model-View-ViewModel)  
    33.4 MVI (Model-View-Intent)  
    33.5 Choosing a Pattern  
34. **[Dependency Management & Package Managers](./[34]-Dependency-Management.md)**  
    34.1 What is a Dependency?  
    34.2 iOS Package Managers  
    34.3 Android Package Managers  
    34.4 Cross-Platform Package Managers  
    34.5 Semantic Versioning  
35. **[Testing Mobile Apps (Unit, Widget, UI Tests)](./[35]-Testing-Mobile-Apps.md)**  
    35.1 The Testing Pyramid  
    35.2 Unit Testing  
    35.3 Widget / Component Testing  
    35.4 UI / Integration Testing  
    35.5 Test-Driven Habits  
36. **[Debugging & Profiling](./%5B36%5D-Debugging-and-Profiling%20%281%29.md)**  
    36.1 Breakpoints and Step Debugging  
    36.2 Logging  
    36.3 Profiling Performance  
    36.4 Crash Analysis  
    36.5 Debugging on Real Devices  

**Publishing & Distribution**  

37. **[App Icons, Splash Screens & Assets](./[37]-App-Icons-and-Splash-Screens.md)**  
    37.1 App Icons  
    37.2 Splash Screens / Launch Screens  
    37.3 Managing Image Assets  
    37.4 Asset Catalogs and Organization  
38. **[Preparing for Release (Signing, Build Variants)](./[38]-Preparing-for-Release.md)**  
    38.1 Why Signing Matters  
    38.2 iOS Code Signing  
    38.3 Android Signing  
    38.4 Build Variants  
    38.5 Pre-Release Checklist  
39. **[Publishing to the App Store & Google Play](./[39]-Publishing-to-App-Stores.md)**  
    39.1 Developer Accounts  
    39.2 Store Listing Requirements  
    39.3 Apple's App Review Process  
    39.4 Google Play's Review Process  
    39.5 Staged Rollouts and Beta Testing  
40. **[App Updates & Versioning](./[40]-App-Updates-and-Versioning.md)**  
    40.1 Version Numbers vs Build Numbers  
    40.2 Handling Breaking Changes  
    40.3 Force Updates vs Optional Updates  
    40.4 Over-the-Air (OTA) Updates  
    40.5 Rollback Strategy  

**Growth & Analytics**  

41. **[Analytics & Crash Reporting](./[41]-Analytics-and-Crash-Reporting.md)**  
    41.1 Why Track Usage?  
    41.2 Event Tracking  
    41.3 Crash Reporting  
    41.4 Non-Fatal Errors and Logging  
    41.5 Privacy Considerations  
42. **[In-App Purchases & Monetization](./[42]-In-App-Purchases-and-Monetization.md)**  
    42.1 Common Monetization Models  
    42.2 In-App Purchases (IAP)  
    42.3 Receipt Validation  
    42.4 Store Commission  
    42.5 Advertising SDKs  

**Best Practices** 

43. **[Mobile Accessibility](./[43]-Mobile-Accessibility.md)**  
    43.1 Why Accessibility Matters  
    43.2 Screen Readers  
    43.3 Touch Target Sizes  
    43.4 Color Contrast and Dynamic Type  
    43.5 Testing Accessibility  
44. **[Performance Optimization for Mobile](./%5B44%5D-Performance-Optimization%20%281%29.md)**  
    44.1 Why Mobile Performance Is Different  
    44.2 Rendering Performance  
    44.3 Memory Management  
    44.4 App Startup Time  
    44.5 Network and Battery Efficiency  
45. **[Mobile Security Basics](./[45]-Mobile-Security-Basics.md)**  
    45.1 Secure Storage of Sensitive Data  
    45.2 Network Security  
    45.3 Authentication and Tokens  
    45.4 Protecting Source Code and Secrets  
    45.5 Input Validation  
46. **[Battery & Resource Efficiency](./[46]-Battery-and-Resource-Efficiency.md)**
    46.1 Why Battery Efficiency Matters  
    46.2 Location and Sensor Usage  
    46.3 Background Work Efficiency  
    46.4 Network Efficiency  
    46.5 CPU and Rendering Efficiency  
    46.6 Wrapping Up  