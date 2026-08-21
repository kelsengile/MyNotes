[Previous](./[41]-Distributing-via-App-Stores.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[43]-Localization-and-Internationalization.md)

*Best Practices*

# Lesson 42 - Desktop Accessibility

## 42.1 Why Accessibility Matters

Accessible apps can be used by people with visual, motor, auditory, or cognitive disabilities — via screen readers, keyboard-only navigation, magnification, or switch devices. Beyond being the right thing to build, accessibility is often a legal requirement (e.g. government and enterprise procurement standards) and improves usability for everyone, not just users with disabilities.

---

## 42.2 Keyboard Navigation

Every interactive control must be reachable and operable without a mouse: logical `Tab` order, visible focus indicators, and keyboard equivalents for mouse-only interactions like drag-and-drop. Test your app by unplugging the mouse and trying to complete a core task using only the keyboard.

---

## 42.3 Screen Reader Support

Screen readers (Narrator on Windows, VoiceOver on macOS, Orca on Linux) announce UI content aloud by reading a control's **accessible name**, **role**, and **state** — exposed through the framework's accessibility tree (`AutomationProperties` in .NET, `aria-*` attributes in Electron's HTML, `NSAccessibility` on macOS). An icon-only button needs an explicit accessible label, since a screen reader can't infer meaning from an image alone.

```xml
<Button AutomationProperties.Name="Save document" Content="💾"/>
```

---

## 42.4 Visual and Cognitive Accessibility

Beyond screen readers: ensure sufficient color contrast (don't rely on color alone to convey meaning — pair it with text or icons), support text scaling/zoom without breaking layout, and avoid interfaces that depend on precise timing or rapid input. Testing with the OS's built-in accessibility inspector (Accessibility Insights on Windows, Accessibility Inspector on macOS) catches many issues before real users do.

[Previous](./[41]-Distributing-via-App-Stores.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[43]-Localization-and-Internationalization.md)
