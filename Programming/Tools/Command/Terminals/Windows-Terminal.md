[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Windows Terminal

Windows Terminal is Microsoft's modern terminal application, capable of hosting multiple shells — Command Prompt, PowerShell, WSL distributions, and Azure Cloud Shell — in a single tabbed, splittable, customizable window. Released in 2019, it replaced the old, feature-poor `conhost.exe` console window as the recommended way to work at the command line on Windows.

Reference: [https://learn.microsoft.com/en-us/windows/terminal/](https://learn.microsoft.com/en-us/windows/terminal/)

---

## What Is Windows Terminal?

Before Windows Terminal, Windows' built-in console host (`conhost.exe`) was notoriously limited: no tabs, poor Unicode/emoji rendering, limited color support, and awkward copy-paste. Windows Terminal is a ground-up rewrite (open-source, built largely in C++/WinUI) that acts purely as a **terminal emulator/host** — it doesn't run commands itself, but hosts sessions of whatever shell you configure as a "profile," rendering their output with GPU-accelerated text, full 24-bit color, ligature-capable fonts, and modern text layout including proper emoji and complex script support.

Because it's a hosting shell rather than a shell itself, a single Windows Terminal window can have one tab running PowerShell, another running Command Prompt, another running a full Ubuntu WSL session, and another SSHed into a remote Linux server — each with its own profile, color scheme, and starting directory.

---

## Core Features

- **Tabs and panes** — split any tab into multiple resizable panes, each an independent shell session.
- **Profiles** — per-shell configuration (icon, color scheme, starting directory, font, command line) defined in a JSON settings file or through the GUI Settings UI.
- **Custom color schemes and themes**, including support for many popular schemes (Solarized, Dracula, One Half, etc.) and background images/acrylic transparency.
- **Command Palette** (`Ctrl+Shift+P`) — a searchable list of every action Windows Terminal can perform, similar to VS Code's command palette.
- **Quake mode** — a global hotkey to drop down a terminal from the top of the screen, similar to dedicated tools like Yakuake on Linux.
- **Unicode and emoji rendering**, complex script shaping, and ligature support for programming fonts like Cascadia Code (which Microsoft develops alongside Windows Terminal).

---

## Common Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl+Shift+T` | New tab (default profile) |
| `Ctrl+Shift+(number)` | New tab with a specific profile |
| `Alt+Shift+D` / `Alt+Shift+-` | Split pane vertically / horizontally |
| `Ctrl+Shift+W` | Close pane/tab |
| `Ctrl+Shift+F` | Search terminal output |
| `Ctrl+Shift+P` | Open command palette |
| `Ctrl+,` | Open settings |
| `Ctrl+Tab` | Cycle through tabs |

---

## Configuration: settings.json

Windows Terminal's entire configuration lives in a single JSON file (also editable through a GUI), covering profile definitions, keybindings, color schemes, and default startup behavior:

```json
{
  "profiles": {
    "list": [
      {
        "name": "Ubuntu (WSL)",
        "commandline": "wsl.exe -d Ubuntu",
        "colorScheme": "One Half Dark",
        "startingDirectory": "//wsl$/Ubuntu/home/user"
      }
    ]
  },
  "defaultProfile": "{guid-of-preferred-profile}"
}
```

This file-based approach makes settings easy to version-control, share between machines, or template across a team.

---

## WSL Integration: The Real Reason for Its Popularity

Windows Terminal's single biggest practical impact has been making **WSL (Windows Subsystem for Linux)** genuinely comfortable to use day-to-day — before Windows Terminal, running Linux distributions on Windows meant a clunky, separate console window. Now, a developer can have a real Ubuntu, Debian, or Fedora shell running alongside PowerShell and Command Prompt tabs in the very same window, with full terminal capabilities, letting Windows serve as a legitimate development platform for Linux-targeted software without a separate VM or dual-boot.

---

## Beyond a Plain Terminal Window

- **Azure Cloud Shell integration** — a profile can connect directly to Azure's browser-based cloud shell environment.
- **SSH profiles** — dedicated profiles that immediately connect to a remote server, complete with their own icon and color scheme for quick visual identification.
- **Fragments extensions** — third-party installers (like WSL distro installers or dev tool installers) can automatically register new profiles into Windows Terminal via a "fragments" mechanism, without the user manually editing JSON.
- **Command-line launching**: `wt.exe` itself is scriptable — `wt -p "Ubuntu" ; split-pane -p "PowerShell"` can launch a fully arranged multi-pane window from a script or shortcut.

---

## Related Tools

- **Command Prompt** and **PowerShell** — the classic shells Windows Terminal most commonly hosts.
- **WSL** — the Linux compatibility layer Windows Terminal made practical to use.
- **iTerm2** — the closest macOS equivalent in spirit (advanced, highly configurable terminal emulator).
- **Konsole / GNOME Terminal** — the equivalent desktop-integrated terminal emulators on Linux.

---

## Example Walkthrough

Opening Windows Terminal, a developer might use `Ctrl+Shift+T` on a "WSL Ubuntu" profile to get a Linux shell for running a project's build tooling, split the pane with `Alt+Shift+-` to open a PowerShell pane for controlling a local Windows service the project depends on, and switch between them with `Alt+Arrow` — running a genuinely mixed Windows/Linux workflow inside one window.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)