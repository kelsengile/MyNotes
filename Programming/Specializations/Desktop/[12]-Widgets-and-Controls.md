[Previous](./[11]-Layouts-and-Containers.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[13]-Styling-and-Theming.md)

*UI Fundamentals*

# Lesson 12 - Widgets & Controls

## 12.1 What Widgets Are

Widgets (also called controls) are the reusable building blocks of a UI: buttons, labels, text fields, checkboxes, radio buttons, sliders, list/grid views, combo boxes, progress bars, and tabs. Every framework ships a standard set, and most also let you compose custom controls out of simpler ones.

---

## 12.2 Common Control Categories

| Category | Examples | Typical use |
|---|---|---|
| Input | TextBox, ComboBox, Slider | Collecting user data |
| Selection | CheckBox, RadioButton, ToggleSwitch | Boolean/exclusive choices |
| Display | Label, Image, ProgressBar | Showing information |
| Container-like | TabControl, Accordion, SplitView | Organizing other controls |
| Data | ListView, DataGrid, TreeView | Showing collections/hierarchies |

---

## 12.3 Data-Driven Controls

List, grid, and tree controls are typically backed by a data source rather than manually populated one item at a time. Binding a `DataGrid` to a collection means the control automatically re-renders rows when the underlying collection changes — this ties directly into data binding, covered in Lesson 17.

```csharp
dataGrid.ItemsSource = customers; // an ObservableCollection<Customer>
```

---

## 12.4 Custom Controls

When the standard set doesn't cover a need — a color picker, a waveform viewer, a custom chart — frameworks let you build a **custom control** or **user control**: a reusable component composed of existing controls plus your own drawing and logic, exposing its own properties and events just like a built-in widget.

[Previous](./[11]-Layouts-and-Containers.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[13]-Styling-and-Theming.md)
