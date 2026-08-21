[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[7]-Functions-and-Scope.md)

*Core Syntax*

# Lesson 6 - Control Flow: Conditionals & Loops

## 6.1 Conditionals

`if`/`else if`/`else` branches execute code based on a boolean condition; `switch`/`match` statements handle many discrete cases more readably than a long `if/else` chain:

```csharp
switch (fileExtension)
{
    case ".png":
    case ".jpg":
        LoadImage();
        break;
    case ".txt":
        LoadText();
        break;
    default:
        ShowUnsupportedFormatError();
        break;
}
```

---

## 6.2 Loops

`for` loops iterate a known number of times (or over a collection); `while` loops repeat while a condition holds; `do...while` guarantees at least one execution. Desktop apps commonly loop over UI elements, file lists, or database rows:

```csharp
foreach (var file in Directory.GetFiles(folderPath))
{
    ProcessFile(file);
}
```

`break` exits a loop early; `continue` skips to the next iteration. Both should be used sparingly — overusing them can make control flow hard to follow.

---

## 6.3 Guard Clauses and Early Returns

Rather than deeply nesting conditionals, exit a function early when a precondition fails. This keeps the "main path" of a function readable:

```csharp
void SaveDocument(string path)
{
    if (string.IsNullOrEmpty(path)) return;
    if (!Directory.Exists(Path.GetDirectoryName(path))) return;
    // main logic here, unindented
}
```

---

## 6.4 Control Flow and the UI Thread

In desktop apps, long-running loops on the main thread freeze the window (covered fully in Lessons 22–23). A rule of thumb: any loop or branch that could take more than a few milliseconds — file scanning, network calls, heavy computation — belongs off the UI thread, with only the final result flowing back to update the interface.

[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[7]-Functions-and-Scope.md)
