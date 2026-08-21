[Previous](./[23]-Async-Programming-for-Responsive-UIs.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[25]-macOS-Development.md)

*Platform-Specific & Cross-Platform Frameworks*

# Lesson 24 - Windows Development (WinForms/WPF/.NET)

## 24.1 The .NET Windows UI Landscape

Windows desktop development is dominated by .NET, with several UI frameworks depending on the era and needs of the project:

- **WinForms** — the oldest, event-driven, drag-and-drop designer style; simple but limited styling.
- **WPF** (Windows Presentation Foundation) — uses XAML for declarative UI with strong data binding, styling, and vector graphics support.
- **WinUI 3** — the modern framework, used with the Windows App SDK, offering the latest Windows 11 look and Fluent Design controls.

---

## 24.2 XAML Basics (WPF/WinUI)

XAML is a declarative XML-based markup for describing UI, separate from the C# code that implements behavior — this split mirrors HTML/JS in Electron:

```xml
<StackPanel Orientation="Vertical" Margin="10">
  <TextBlock Text="Enter your name:"/>
  <TextBox x:Name="NameBox"/>
  <Button Content="Submit" Click="OnSubmit"/>
</StackPanel>
```

```csharp
private void OnSubmit(object sender, RoutedEventArgs e)
{
    MessageBox.Show($"Hello, {NameBox.Text}!");
}
```

---

## 24.3 Windows-Specific Integration

Windows apps commonly integrate with: the taskbar (jump lists, progress overlays), the Registry for legacy settings storage, COM interop for older Windows APIs, and the Windows notification system (toast notifications). The Windows App SDK bridges older Win32 capabilities with modern UI, letting WinUI apps still call into native Win32 APIs when needed.

---

## 24.4 Packaging for Windows

Windows apps distribute either as a traditional installer (`.exe`/`.msi`, often via WiX or Inno Setup) or as an **MSIX** package for Microsoft Store distribution and modern sandboxed installation. MSIX provides cleaner uninstalls and automatic updates, covered further in Lessons 40–41.

[Previous](./[23]-Async-Programming-for-Responsive-UIs.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[25]-macOS-Development.md)
