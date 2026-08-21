[Previous](./[16]-Handling-User-Input.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[18]-File-IO-and-the-Filesystem.md)

*Events & Interaction*

# Lesson 17 - Data Binding & UI Updates

## 17.1 The Problem Data Binding Solves

Without binding, keeping a UI in sync with underlying data means manually writing code every time either side changes: update the label whenever the value changes, and update the value whenever the user edits the label. **Data binding** automates this synchronization declaratively.

---

## 17.2 One-Way and Two-Way Binding

- **One-way binding**: data flows from source to UI only (e.g. a "last saved" timestamp label). The UI updates when the data changes; editing the UI doesn't change the data.
- **Two-way binding**: data flows in both directions (e.g. a text field bound to a `Name` property) — typing in the field updates the underlying value, and changing the value elsewhere updates the field.

```xml
<TextBox Text="{Binding UserName, Mode=TwoWay}"/>
```

---

## 17.3 Observable Data

For binding to update automatically, the underlying data needs to notify the UI when it changes — via an "observable" pattern (`INotifyPropertyChanged` in .NET, `ObservableCollection<T>` for lists, reactive state in Electron front-ends built with React/Vue). Plain fields that don't raise change notifications won't trigger a UI refresh no matter how they're bound.

```csharp
public class DocumentViewModel : INotifyPropertyChanged
{
    private string _title;
    public string Title
    {
        get => _title;
        set { _title = value; OnPropertyChanged(nameof(Title)); }
    }
}
```

---

## 17.4 Avoiding Binding Pitfalls

Binding can hide performance problems: a list bound naively can re-render entirely on every small change instead of just the changed row, and binding chains that are too deep make debugging "why did this update?" difficult. Prefer fine-grained observable properties, and profile UI updates (Lesson 36) if a data-heavy view feels sluggish.

[Previous](./[16]-Handling-User-Input.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[18]-File-IO-and-the-Filesystem.md)
