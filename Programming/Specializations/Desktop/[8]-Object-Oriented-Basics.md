[Previous](./[7]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[9]-Collections-and-Data-Structures.md)

*Core Syntax*

# Lesson 8 - Object-Oriented Basics

## 8.1 Classes and Objects

A class is a blueprint describing data (fields/properties) and behavior (methods); an object is a specific instance of that blueprint. Desktop UI frameworks are almost universally object-oriented — a window, a button, and a text field are each objects, usually instances of framework-provided classes you extend or compose.

```csharp
public class MainWindow : Window
{
    private Button _saveButton;

    public MainWindow()
    {
        _saveButton = new Button { Text = "Save" };
        _saveButton.Click += OnSaveClicked;
    }

    private void OnSaveClicked(object sender, EventArgs e) { /* ... */ }
}
```

---

## 8.2 Encapsulation

Encapsulation hides an object's internal state behind a public interface (properties and methods), preventing other code from putting it into an invalid state directly. Marking fields `private` and exposing controlled `public` getters/setters is standard practice, especially for UI state that must stay in sync with what's rendered on screen.

---

## 8.3 Inheritance and Polymorphism

Inheritance lets a class reuse and extend another class's behavior — every custom window in a framework typically inherits from a base `Window`/`Form` class. Polymorphism lets code work with objects through a shared base type or interface without knowing the concrete subclass, which is how a framework can call `Draw()` on any control and let each control type render itself differently.

---

## 8.4 Composition Over Inheritance

Deep inheritance chains become brittle. Many modern UI frameworks favor **composition** — building complex UI by nesting simple, independent components — over long inheritance hierarchies. Reason about whether a relationship is truly "is-a" (inheritance) or "has-a" (composition) before extending a base class.

[Previous](./[7]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[9]-Collections-and-Data-Structures.md)
