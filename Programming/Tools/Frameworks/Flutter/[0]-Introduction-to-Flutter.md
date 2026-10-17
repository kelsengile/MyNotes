[⬅ Go to Frameworks](../[0]-Introduction-to-Frameworks.md) or [⬅ Go to Dart](/Programming/Syntax/Dart/[0]-Introduction-to-Dart.md)


<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/flutter/flutter-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Flutter</h1>
</div>


Flutter is Google's open-source UI toolkit for building natively compiled applications for mobile, web, and desktop from a single codebase. Instead of wrapping native components, Flutter draws every pixel itself with its own rendering engine, which gives you consistent, highly customizable interfaces on every platform.

Everything in Flutter is a widget, and apps are written in the Dart language. This Topic covers Dart essentials, the widget system, layout, navigation, state management, networking, storage, and publishing, so you can go from your first screen to an app in the stores.

Download: [flutter.dev/docs/get-started/install](https://docs.flutter.dev/get-started/install)

### Why Study Flutter?

- **One codebase, many platforms** — target Android, iOS, web, and desktop without maintaining separate apps.
- **Fast development loop** — stateful hot reload shows UI changes in under a second.
- **Full control over the UI** — custom designs and animations are first-class, not an afterthought.
- **Growing demand** — startups and large companies alike use Flutter for cross-platform products.

### Flutter At A Glance

| Attribute | Details |
|---|---|
| Language | Dart |
| Type | UI toolkit / framework |
| Created by | Google |
| Best for | Cross-platform mobile apps with custom UIs |
| Pairs well with | Firebase, Riverpod, Bloc, REST APIs |

---

### Table of Contents

**Basics**

   1. **[What Is Flutter](./[1]-What-Is-Flutter.md)**  
       1.1 Cross-platform development explained  
       1.2 How Flutter renders UI  
       1.3 Installing the SDK and flutter doctor  
       1.4 Tour of a Flutter project  
   2. **[Dart Essentials](./[2]-Dart-Essentials.md)**  
       2.1 Variables and null safety  
       2.2 Functions and classes  
       2.3 Collections  
       2.4 Futures, async, and await  
   3. **[Widgets Fundamentals](./[3]-Widgets-Fundamentals.md)**  
       3.1 Everything is a widget  
       3.2 StatelessWidget vs StatefulWidget  
       3.3 The widget tree and build method  
       3.4 Keys and widget identity  

**Building The UI**

   4. **[Layout And Constraints](./[4]-Layout-And-Constraints.md)**  
       4.1 Row, Column, and Stack  
       4.2 Expanded and Flexible  
       4.3 How constraints work  
       4.4 Lists and grids  
   5. **[Material And Cupertino](./[5]-Material-And-Cupertino.md)**  
       5.1 Material components  
       5.2 Cupertino (iOS-style) widgets  
       5.3 Scaffold and AppBar  
       5.4 Adaptive widgets  
   6. **[Styling And Theming](./[6]-Styling-And-Theming.md)**  
       6.1 ThemeData and color schemes  
       6.2 Typography  
       6.3 Dark mode  
       6.4 Custom fonts and assets  
   7. **[Forms And User Input](./[7]-Forms-And-User-Input.md)**  
       7.1 TextField and controllers  
       7.2 Form and validation  
       7.3 Gestures and taps  
       7.4 Keyboard handling  
   8. **[Responsive Design](./[8]-Responsive-Design.md)**  
       8.1 MediaQuery and LayoutBuilder  
       8.2 Breakpoints  
       8.3 Orientation changes  
       8.4 Adapting to web and desktop  
   9. **[Animations](./[9]-Animations.md)**  
       9.1 Implicit animations  
       9.2 Explicit animations  
       9.3 Hero transitions  
       9.4 Animation performance  

**Navigation And State**

   10. **[Navigation And Routing](./[10]-Navigation-And-Routing.md)**  
       10.1 Navigator basics  
       10.2 Named routes and go_router  
       10.3 Passing data between screens  
       10.4 Deep links  
   11. **[State Management](./[11]-State-Management.md)**  
       11.1 setState and its limits  
       11.2 InheritedWidget and Provider  
       11.3 Riverpod  
       11.4 Bloc and choosing an approach  

**Data And Platform Features**

   12. **[Networking And APIs](./[12]-Networking-And-APIs.md)**  
       12.1 The http and dio packages  
       12.2 JSON serialization  
       12.3 FutureBuilder and async UI  
       12.4 Error handling  
   13. **[Local Storage](./[13]-Local-Storage.md)**  
       13.1 shared_preferences  
       13.2 SQLite and Drift  
       13.3 Hive and Isar  
       13.4 Secure storage  
   14. **[Packages And Platform Features](./[14]-Packages-And-Platform-Features.md)**  
       14.1 pub.dev and dependencies  
       14.2 Permissions, camera, and location  
       14.3 Platform channels  
       14.4 Firebase basics  

**Quality And Delivery**

   15. **[Testing Flutter Apps](./[15]-Testing-Flutter-Apps.md)**  
       15.1 Unit tests  
       15.2 Widget tests  
       15.3 Integration tests  
       15.4 Mocking dependencies  
   16. **[Debugging And Performance](./[16]-Debugging-And-Performance.md)**  
       16.1 Flutter DevTools  
       16.2 The widget inspector  
       16.3 Reducing rebuilds  
       16.4 Profiling jank  
   17. **[Building And Publishing](./[17]-Building-And-Publishing.md)**  
       17.1 Release builds  
       17.2 Android signing and Play Store  
       17.3 iOS builds and App Store  
       17.4 CI/CD for Flutter  
   18. **[Next Steps With Flutter](./[18]-Next-Steps-With-Flutter.md)**  
       18.1 Clean architecture  
       18.2 Flutter for web and desktop  
       18.3 Native integration  
       18.4 Portfolio app ideas
