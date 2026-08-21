[Previous](./[26]-Logging-in-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[28]-User-and-Permission-Management-Scripts.md)

*Advanced Scripting Concepts*

# Lesson 27 - Debugging Scripts

## 27.1 Debugging Bash Scripts

```bash
bash -x script.sh          # print each command before it runs
set -x                      # enable tracing from this point in the script
set +x                      # disable tracing
```

Combine with `set -euo pipefail` (Lesson 16) so the script also stops immediately when something goes wrong, rather than continuing with a corrupted state.

---

## 27.2 Debugging Python Scripts

```python
import pdb
pdb.set_trace()   # drops into an interactive debugger at this line
```

Useful `pdb` commands: `n` (next line), `s` (step into), `c` (continue), `p variable` (print a value), `q` (quit).

For quick checks, `print()` statements or the `logging` module (Lesson 26) are often faster than a full debugger session.

---

## 27.3 Debugging PowerShell Scripts

```powershell
Set-PSBreakpoint -Script script.ps1 -Line 10
Write-Debug "Value of x: $x" -Debug
```

VS Code's PowerShell extension also supports full breakpoint debugging with a visual debugger, similar to Python's.

---

## 27.4 General Debugging Strategy

1. Reproduce the failure reliably.
2. Narrow down which command/line is responsible (tracing, print statements).
3. Inspect the actual values involved, don't assume.
4. Fix the smallest possible thing, then re-test the whole script.

---

[Previous](./[26]-Logging-in-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[28]-User-and-Permission-Management-Scripts.md)
