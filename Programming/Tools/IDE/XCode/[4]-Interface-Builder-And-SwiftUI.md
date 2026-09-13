[Previous](./[3]-Swift-And-Project-Structure-Basics.md) | [Table of Contents](./[0]-Introduction-to-XCode.md) | [Next](./[5]-Building,-Running,-And-Debugging.md)

*Interface And Project Structure*

# Lesson 4 - Interface Builder And SwiftUI

## 4.1 Storyboards And .xib Files (UIKit)

**Interface Builder** is Xcode's visual editor for UIKit layouts, working with two file types:

- **Storyboards (`.storyboard`)** — represent multiple screens and the segues (transitions) between them in a single visual canvas, showing the app's overall flow.
- **`.xib` files** (also called NIBs) — represent a single view or view controller in isolation, without the multi-screen flow a Storyboard shows.

```
┌─────────────┐   segue    ┌─────────────┐
│ Login Screen  │ ─────────▶ │ Home Screen   │
│  [Username]    │             │  [Feed List]   │
│  [Password]    │             │                │
│  [Login Button]│             │                │
└─────────────┘             └─────────────┘
```

- **Segues** — represented as arrows between screens in a Storyboard, triggered either automatically (e.g. tapping a button wired to it) or manually in code via `performSegue(withIdentifier:sender:)`.
- **Outlets and Actions** — as introduced in Lesson 2.4, dragging from a UI element to code creates an `@IBOutlet` (a reference) or `@IBAction` (an event handler), connecting the visual canvas to Swift logic.

Storyboards were the dominant way to build UIKit interfaces for years, though many teams now build UIKit screens entirely in code (programmatic UIKit) or have migrated to SwiftUI, covered in 4.3.

---

## 4.2 Auto Layout And Constraints

**Auto Layout** is UIKit's system for describing a UI element's position and size relative to other elements or its container, rather than fixed pixel coordinates — necessary because iOS devices span many screen sizes.

```
┌──────────────────────────┐
│                            │
│     ┌──────────────┐       │
│     │   Button       │       │ ← centered horizontally,
│     └──────────────┘       │    20pt from top
│                            │
└──────────────────────────┘

Constraints:
 • centerX = superview.centerX
 • top = superview.top + 20
 • width = 200
 • height = 44
```

- **Constraints** — rules like "this button's leading edge is 16 points from its container's leading edge," added by dragging in Interface Builder or written in code.
- **Ambiguous/conflicting layout warnings** — Xcode flags these directly in the canvas (a yellow or red arrow icon) with a description of which constraints conflict, before you even run the app.
- **Stack Views (`UIStackView`)** — group elements in a row or column that automatically handles spacing and distribution, reducing the number of manual constraints needed for common layouts.

---

## 4.3 SwiftUI Previews And Declarative UI

**SwiftUI** describes UI declaratively — you state what the UI should look like for a given state, and SwiftUI handles updating the screen when that state changes, rather than manually mutating views like UIKit requires.

```swift
struct ContentView: View {
    @State private var count = 0

    var body: some View {
        VStack {
            Text("Count: \(count)")
            Button("Increment") {
                count += 1
            }
        }
    }
}

#Preview {
    ContentView()
}
```

- **`@State`** — marks a property whose changes automatically trigger SwiftUI to redraw any view that reads it — no manual "refresh the UI" call needed.
- **Live Preview (the canvas)** — press `Cmd+Option+Return` or click "Resume" in the canvas to see the view rendered live next to the code, updating within seconds as you edit — without rebuilding and relaunching the whole app in a simulator.
- **Interactive preview mode** — the canvas preview can even be clicked and interacted with (e.g. tapping the "Increment" button above) directly in the editor, not just viewed statically.

---

## 4.4 Choosing UIKit vs SwiftUI

Both frameworks are fully supported by Apple, and many real-world apps use both together via interop (`UIViewRepresentable` to embed a UIKit view in SwiftUI, or `UIHostingController` to embed SwiftUI in UIKit).

| | UIKit | SwiftUI |
|---|---|---|
| Style | Imperative (you manually update views) | Declarative (UI reflects state automatically) |
| Introduced | 2008 (iPhone OS 2) | 2019 (iOS 13) |
| Minimum OS support | Very broad, back to old iOS versions | Requires iOS 13+ (some APIs need newer) |
| Preview workflow | Interface Builder canvas or run in Simulator | Live, near-instant canvas preview |
| Maturity for edge cases | Extremely mature, most third-party libraries | Rapidly maturing, occasional gaps for advanced customization |

General guidance:

- **New, greenfield projects** — SwiftUI is Apple's recommended default going forward, and typically requires noticeably less code for the same UI.
- **Existing large UIKit codebases** — often stay UIKit for existing screens while introducing SwiftUI incrementally for new features, via the interop APIs mentioned above.
- **Apps needing very old OS support or highly specialized UI behavior** — UIKit's maturity and finer-grained control can still make it the more practical choice.

[Previous](./[3]-Swift-And-Project-Structure-Basics.md) | [Table of Contents](./[0]-Introduction-to-XCode.md) | [Next](./[5]-Building,-Running,-And-Debugging.md)
