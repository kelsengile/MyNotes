[Previous](./[34]-Integrating-Scripts-with-CI-CD-Pipelines.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[36]-Script-Security-Best-Practices.md)

*Integration & Tooling*

# Lesson 35 - Cross-Platform Scripting Considerations

## 35.1 Line Endings

Unix systems use `LF` line endings; Windows uses `CRLF`. A Bash script saved with `CRLF` will fail with a cryptic error because the shebang line becomes `#!/bin/bash\r`. Configure your editor and Git (`core.autocrlf`) to normalize this.

---

## 35.2 Path Separators

Unix paths use `/`; Windows uses `\` (though it accepts `/` in most contexts). Avoid hardcoding separators:

```python
from pathlib import Path
p = Path("data") / "input.csv"   # works correctly on every OS
```

```powershell
Join-Path -Path "data" -ChildPath "input.csv"
```

---

## 35.3 Case Sensitivity

Linux filesystems are typically case-sensitive (`File.txt` ≠ `file.txt`); Windows and default macOS filesystems are not. Scripts that work by accident on Windows can break on Linux if filenames only differ by case.

---

## 35.4 Choosing a Cross-Platform Approach

- **Python** is the most reliably cross-platform of the three languages in this course — the same script usually runs unmodified on Linux, macOS, and Windows.
- **PowerShell 7+** now also runs cross-platform, making it viable outside Windows-only contexts.
- **Bash** scripts generally need WSL, Git Bash, or a Linux/macOS environment on Windows machines.

When a script must run identically everywhere, prefer Python or PowerShell 7+ over Bash, or clearly document the required environment.

---

[Previous](./[34]-Integrating-Scripts-with-CI-CD-Pipelines.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[36]-Script-Security-Best-Practices.md)
