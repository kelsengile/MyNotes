[Previous](./[12]-Platform-Specific-Code.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[14]-Debugging-React-Native-Apps.md)

*Device Features*

# Lesson 13 - Native Modules And The New Architecture

Sometimes JavaScript is not enough. You may need a platform feature that no library exposes, or code that must run at native speed. A **native module** is code written in Swift/Objective-C (iOS) or Kotlin/Java (Android) that your JavaScript can call.

React Native has also rebuilt how JavaScript and native code communicate. This is called the **New Architecture**, and it is the default in React Native 0.76 and newer.

## 13.1 JSI

The **old architecture** used a **bridge**: JavaScript and native code sent JSON messages to each other asynchronously. This caused overhead, batching delays, and made synchronous calls impossible.

**JSI (JavaScript Interface)** replaces the bridge. It is a lightweight C++ layer that lets JavaScript hold **direct references** to native objects and call native functions directly.

```
Old:  JS  →  serialize to JSON  →  Bridge (async queue)  →  Native
New:  JS  →  JSI (direct C++ call)  →  Native
```

Benefits:

- **No JSON serialization** between the two sides.
- **Synchronous calls** are possible when needed.
- **Faster startup and lower overhead**, since modules can be loaded lazily.
- JSI works with any JavaScript engine, and Hermes is the default.

Libraries like Reanimated, MMKV, and Gesture Handler use JSI to achieve their speed.

---

## 13.2 Fabric and TurboModules

The New Architecture has several parts that work together:

| Part | Replaces | Purpose |
|---|---|---|
| **JSI** | The bridge | Direct JS to native communication |
| **Fabric** | The old UI renderer | Renders views with a shared C++ core, supports synchronous layout and concurrent React features |
| **TurboModules** | Old native modules | Native modules that load lazily and are typed |
| **Codegen** | Manual glue code | Generates the native interface code from a typed spec |

**Fabric** lets React Native work with modern React features such as Suspense and transitions. It also reduces the gap between the JavaScript tree and the native view tree.

**TurboModules** are loaded only when first used, which improves startup time. They are described by a typed specification, so mistakes are caught earlier.

**Codegen** reads your TypeScript (or Flow) spec file and generates the matching C++, Objective-C++, and Java/Kotlin interfaces.

What this means for you as an app developer:

- Most apps just work. Update React Native and your libraries.
- Prefer **libraries that support the New Architecture**. Most popular ones already do. Check the React Native Directory (reactnative.directory) for compatibility.
- You usually do not write JSI or C++ yourself.
- The old architecture is being phased out, so new projects should use the New Architecture.

---

## 13.3 Writing native modules

When you need your own native code in a plain React Native project, you write a **TurboModule**.

**1. Define a typed spec** (TypeScript):

```ts
// NativeGreeter.ts
import type { TurboModule } from 'react-native';
import { TurboModuleRegistry } from 'react-native';

export interface Spec extends TurboModule {
  greet(name: string): string;
  getBatteryLevel(): Promise<number>;
}

export default TurboModuleRegistry.getEnforcing<Spec>('NativeGreeter');
```

**2. Configure Codegen** in `package.json`:

```json
{
  "codegenConfig": {
    "name": "NativeGreeterSpec",
    "type": "modules",
    "jsSrcsDir": "src",
    "android": {
      "javaPackageName": "com.myapp.greeter"
    }
  }
}
```

**3. Implement the native side.** Codegen produces an interface, and you write the iOS (Objective-C++ or Swift via a wrapper) and Android (Kotlin or Java) implementations that fulfill it.

**4. Use it from JavaScript:**

```jsx
import NativeGreeter from './NativeGreeter';

const message = NativeGreeter.greet('Ana');
const level = await NativeGreeter.getBatteryLevel();
```

Writing TurboModules by hand involves a lot of setup and native knowledge. The exact steps change between React Native versions, so follow the official "Turbo Native Modules" guide for your version.

**Choosing the approach:**

- First, check whether a maintained library already does what you need.
- If you need custom native code in an Expo project, the Expo Modules API (next section) is much simpler.
- Use TurboModules directly when working in a bare React Native project or when contributing to libraries.

---

## 13.4 Expo modules

The **Expo Modules API** is a friendlier way to write native modules. You write Swift and Kotlin with a clean, declarative syntax, and the tooling handles the glue code.

**Create a module**

```bash
npx create-expo-module@latest my-module
```

**iOS (Swift)**

```swift
import ExpoModulesCore

public class HelloModule: Module {
  public func definition() -> ModuleDefinition {
    Name("Hello")

    Function("greet") { (name: String) -> String in
      return "Hello, \(name)!"
    }

    AsyncFunction("fetchValue") { (id: Int) -> String in
      return "Value \(id)"
    }
  }
}
```

**Android (Kotlin)**

```kotlin
package expo.modules.hello

import expo.modules.kotlin.modules.Module
import expo.modules.kotlin.modules.ModuleDefinition

class HelloModule : Module() {
  override fun definition() = ModuleDefinition {
    Name("Hello")

    Function("greet") { name: String ->
      "Hello, $name!"
    }

    AsyncFunction("fetchValue") { id: Int ->
      "Value $id"
    }
  }
}
```

**JavaScript**

```jsx
import { requireNativeModule } from 'expo';

const Hello = requireNativeModule('Hello');

console.log(Hello.greet('Ana'));            // "Hello, Ana!"
const value = await Hello.fetchValue(7);
```

Features of the Expo Modules API:

- **Functions and async functions**, with automatic type conversion.
- **Events** that native code can send to JavaScript.
- **Native views** that you can use as React components.
- Works in Expo projects and in bare React Native projects.

**Running custom native code in Expo**

Expo Go only includes a fixed set of native modules. Once you add a library or module with custom native code, you need a **development build**, which is your own version of the Expo Go app that includes your native code:

```bash
npx expo install expo-dev-client
npx expo run:ios      # or run:android, builds locally
# or build in the cloud:
eas build --profile development --platform ios
```

Expo uses **prebuild** to generate the `ios/` and `android/` folders from your config when needed (`npx expo prebuild`). These folders can be regenerated at any time, so many teams keep them out of source control.

---

[Previous](./[12]-Platform-Specific-Code.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[14]-Debugging-React-Native-Apps.md)