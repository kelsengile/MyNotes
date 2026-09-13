[Table of Contents](./[0]-Introduction-to-XCode.md) | [Next](./[2]-The-Xcode-Interface.md)

*Getting Started*

# Lesson 1 - Installing Xcode

## 1.1 Xcode And The Mac App Store

Xcode is distributed exclusively through the Mac App Store (or as a direct `.xip` download from the Apple Developer website for versions not yet on the App Store). It only installs and runs on macOS — there is no Windows or Linux version, which is why any platform Xcode targets (iOS, iPadOS, macOS, watchOS, tvOS) requires a Mac somewhere in the workflow.

```
Mac App Store
 └── Xcode  [Get] → [Install]
       ~ multi-GB download
       includes: Simulators, SDKs, Instruments, Swift/Obj-C compilers
```

A few practical notes before installing:

- **Storage space** — Xcode itself plus its bundled simulators for multiple iOS versions can total well over 20-40 GB; keep this in mind on smaller-capacity Macs.
- **macOS version requirement** — each Xcode release supports a specific minimum macOS version, and a very old macOS install may only be able to run an older Xcode version, which in turn may not support the newest SDKs.
- **Multiple Xcode versions** — different versions can coexist by renaming the `.app` (e.g. `Xcode-15.app`), useful when one project needs an older SDK than another.

---

## 1.2 Apple Developer Account Tiers

Using Xcode to write and run code locally is free, but two Apple Developer account tiers unlock further capabilities:

| Tier | Cost | Unlocks |
|---|---|---|
| Free Apple ID | $0 | Run apps on your own physical device (7-day signing limit), use Simulators |
| Apple Developer Program | $99/year | Unlimited-duration signing, TestFlight beta distribution, App Store submission, advanced capabilities (Push Notifications, HealthKit, etc.) |

```
                Free Apple ID
                       │
        ┌──────────────┴──────────────┐
        ▼                              ▼
  Run in Simulator              Run on real device
  (no account needed             (signed app expires
   beyond Xcode itself)           after 7 days)

                Apple Developer Program ($99/yr)
                       │
        ┌──────────────┴──────────────┐
        ▼                              ▼
   TestFlight beta               App Store
   distribution                  submission
```

You don't need to pay anything to learn Xcode, build apps, and test them in the Simulator — the paid tier only becomes necessary once you want to distribute an app beyond your own device, covered fully in [Lesson 7](./[7]-Archiving-And-Distributing-An-App.md).

---

## 1.3 Creating A New Project And Templates

Xcode's **File → New → Project** wizard starts with a template picker, grouped by platform tab (iOS, macOS, watchOS, tvOS, visionOS, Multiplatform).

Common templates:

- **App** — the standard starting point for a new iOS/macOS app, offering a choice between SwiftUI and UIKit for the interface, and Swift or Objective-C for the language.
- **Game** — preconfigures a graphics framework (SpriteKit, SceneKit, or Metal).
- **Framework/Library** — for building reusable code shared across your own apps, rather than a standalone app.

After picking a template, Xcode asks for:

1. **Product Name** — becomes the app's display name and default bundle identifier suffix.
2. **Team** — your Apple Developer account, used for code signing.
3. **Organization Identifier** — reverse-DNS style (e.g. `com.yourcompany`), combined with the product name to form the unique **Bundle Identifier** (e.g. `com.yourcompany.MyApp`).
4. **Interface** — SwiftUI or Storyboard (UIKit), covered in depth in [Lesson 4](./[4]-Interface-Builder-And-SwiftUI.md).

---

## 1.4 Command Line Tools

Beyond the full Xcode.app, Apple provides the standalone **Command Line Tools** package — compilers, Git, and other developer utilities — usable without the full IDE, and required by many command-line workflows (like Homebrew) even on machines that do use full Xcode.

```
xcode-select --install
```

Key distinctions:

- **`xcodebuild`** — a command-line tool bundled with full Xcode that can build, test, and archive a project without opening the GUI at all, essential for CI/CD pipelines.
- **`xcrun`** — locates and runs tools from the currently selected Xcode/Command Line Tools installation (e.g. `xcrun simctl list` to list available simulators).
- **Switching the active toolchain** — `sudo xcode-select --switch /Applications/Xcode.app` points command-line tools at a specific full Xcode install, relevant when multiple Xcode versions are installed side by side (see 1.1).

Even developers who work primarily in the Xcode GUI often end up using these command-line tools eventually — most commonly `xcodebuild` for automated builds and `pod`/`swift package` commands for dependency management (covered in [Lesson 3.4](./[3]-Swift-And-Project-Structure-Basics.md)).

[Table of Contents](./[0]-Introduction-to-XCode.md) | [Next](./[2]-The-Xcode-Interface.md)
