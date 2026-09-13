[⬅ Back to IDE Fundamentals](../[0]-Introduction-to-IDEs.md)

# Introduction to Xcode

Xcode is Apple's official integrated development environment for building software across every Apple platform — iOS, iPadOS, macOS, watchOS, and tvOS. It bundles the Swift and Objective-C compilers, Interface Builder (for UIKit) and SwiftUI previews, simulators for every Apple device, and the signing and submission tools needed to ship an app to the App Store, all in one editor that only runs on macOS.

This Topic covers Xcode's interface and project structure, how Swift-based projects are organized, the two ways of building UI (UIKit's Storyboards and the newer, declarative SwiftUI), and the build, debug, test, and distribution pipeline that's unique to developing for Apple's platforms.

## Why Learn Xcode?

- **The only way to ship to Apple platforms** — publishing an app to the App Store requires building and archiving it through Xcode; there's no way around this requirement.
- **Everything in one place** — simulators, a debugger, performance profiling (Instruments), and unit/UI testing are all built in, rather than being separate installs.
- **SwiftUI previews** — changes to a SwiftUI view can be seen live in the canvas without rebuilding and rerunning the whole app.
- **Tight OS integration** — because Apple builds both the IDE and the platforms it targets, new OS features are usually supported in Xcode from day one.
- **Free to download** — Xcode itself has no cost, though publishing to the App Store requires a paid Apple Developer Program membership.

Download: [Xcode on the Mac App Store](https://apps.apple.com/us/app/xcode/id497799835)

## Table of Contents

**Getting Started**
   1. **[Installing Xcode](./[1]-Installing-Xcode.md)**  
       1.1 Xcode And The Mac App Store  
       1.2 Apple Developer Account Tiers  
       1.3 Creating A New Project And Templates  
       1.4 Command Line Tools  
   2. **[The Xcode Interface](./[2]-The-Xcode-Interface.md)**  
       2.1 The Navigator, Editor, And Inspector Panes  
       2.2 The Project Navigator And File Structure  
       2.3 The Toolbar And Scheme Selector  
       2.4 The Assistant Editor  

**Interface And Project Structure**
   3. **[Swift And Project Structure Basics](./[3]-Swift-And-Project-Structure-Basics.md)**  
       3.1 Swift As Xcode's Primary Language  
       3.2 The App Entry Point (App Delegate/Scene Delegate vs SwiftUI's App Struct)  
       3.3 Info.plist And Project Settings  
       3.4 Frameworks And Dependencies (Swift Package Manager)  
   4. **[Interface Builder And SwiftUI](./[4]-Interface-Builder-And-SwiftUI.md)**  
       4.1 Storyboards And .xib Files (UIKit)  
       4.2 Auto Layout And Constraints  
       4.3 SwiftUI Previews And Declarative UI  
       4.4 Choosing UIKit vs SwiftUI  

**Building And Debugging Apps**
   5. **[Building, Running, And Debugging](./[5]-Building,-Running,-And-Debugging.md)**  
       5.1 Simulators vs Physical Devices  
       5.2 Build Configurations (Debug/Release) And Schemes  
       5.3 Breakpoints And The Debug Navigator  
       5.4 The Console And LLDB  
   6. **[Testing In Xcode](./[6]-Testing-In-Xcode.md)**  
       6.1 XCTest And Unit Tests  
       6.2 UI Testing And The Test Recorder  
       6.3 Instruments For Performance Profiling  
       6.4 Code Coverage Reports  

**Shipping**
   7. **[Archiving And Distributing An App](./[7]-Archiving-And-Distributing-An-App.md)**  
       7.1 Signing And Provisioning Profiles  
       7.2 Archiving A Build  
       7.3 TestFlight For Beta Testing  
       7.4 Submitting To The App Store  
