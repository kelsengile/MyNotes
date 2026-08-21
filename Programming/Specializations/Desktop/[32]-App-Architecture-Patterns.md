[Previous](./[31]-Plugin-and-Extension-Systems.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[33]-Dependency-Management.md)

*Architecture & Best Practices*

# Lesson 32 - App Architecture Patterns (MVC, MVVM)

## 32.1 Why Architecture Matters

Without a consistent structure, UI code, business logic, and data access tend to blur together into large, hard-to-test classes where a button's click handler directly queries a database and updates five other controls. Architectural patterns separate these concerns so each piece can be understood, tested, and changed independently.

---

## 32.2 MVC (Model-View-Controller)

- **Model** — the data and business logic, unaware of the UI.
- **View** — displays data and forwards user actions.
- **Controller** — mediates between the two, updating the model in response to view events and telling the view what to display.

MVC is common in frameworks with less built-in data binding, where the controller explicitly pushes updates to the view.

---

## 32.3 MVVM (Model-View-ViewModel)

MVVM, dominant in XAML-based frameworks (WPF, .NET MAUI) and popular elsewhere, replaces the controller with a **ViewModel**: a class exposing observable properties and commands that the View binds to directly (Lesson 17), removing most manual "update the view" code.

```csharp
public class NoteViewModel : INotifyPropertyChanged
{
    public string Title { get => _title; set { _title = value; OnPropertyChanged(); } }
    public ICommand SaveCommand { get; }
}
```

The View never touches the Model directly, and the ViewModel never references UI controls — this separation makes the ViewModel unit-testable without spinning up any UI.

---

## 32.4 Choosing a Pattern

Pick the pattern your framework's binding system is built around rather than fighting it — MVVM in a heavily binding-oriented framework, a simpler MVC/Presenter split where binding is weaker (many Qt or GTK apps). Whatever pattern you choose, the underlying goal is the same: keep business logic out of UI event handlers so it can be tested and reused.

[Previous](./[31]-Plugin-and-Extension-Systems.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[33]-Dependency-Management.md)
