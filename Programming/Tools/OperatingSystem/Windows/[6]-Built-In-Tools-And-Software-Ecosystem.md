[Previous](./[5]-The-Command-Line-On-Windows.md) | [Table of Contents](./[0]-Introduction-to-Windows.md) | [Next](./[7]-Using-Windows-For-Development.md)

*System Features*

# Lesson 6 - Built-In Tools And Software Ecosystem

## 6.1 Default Apps (Edge, Notepad, Task Manager)

Windows ships with a set of built-in apps covering everyday needs out of the box:

| App | Purpose |
|---|---|
| **Microsoft Edge** | Default web browser, built on the same Chromium engine as Google Chrome |
| **Notepad** | A minimal plain-text editor, recently updated with tabs and basic autosave |
| **Task Manager** | Shows running apps/processes, resource usage (CPU, memory, disk, GPU), and lets you force-quit unresponsive programs |
| **Paint** | Simple image editing and drawing |
| **Photos** | Views and lightly edits images |
| **Mail & Calendar** | Basic email and scheduling, syncable with Outlook.com, Gmail, and other accounts |

**Task Manager** deserves special mention — opened with **Ctrl + Shift + Esc**, it's one of the most-used troubleshooting tools in Windows, letting you identify which process is consuming excessive CPU, memory, or disk activity, and end it directly without restarting the whole PC.

## 6.2 The Microsoft Store

The **Microsoft Store** is Windows' curated app marketplace, similar in concept to the Mac App Store: apps are submitted, reviewed, and distributed through one central, sandboxed system with automatic updates.

```
Developer → submits app → Microsoft review → Published on Microsoft Store → User installs
```

The Microsoft Store has historically had a smaller catalog than Windows' broader software ecosystem, since Windows has long allowed installing software from outside the store (unlike more locked-down platforms). In recent years, Microsoft has expanded what the Store supports, including some traditional `.exe`/`.msi` installers and even certain Android apps, narrowing that gap somewhat.

## 6.3 Installing Software Outside The Store (.exe/.msi)

The majority of Windows software is still installed the traditional way: downloading an installer file, typically ending in **`.exe`** or **`.msi`**, directly from a developer's website.

```
Download installer.exe → SmartScreen checks reputation/signature
                                    │
                    ┌───────────────┴───────────────┐
              Recognized/trusted              Unrecognized publisher
                    │                                 │
              Runs normally                "Windows protected your PC"
                                                       │
                                     User can click "More info" → "Run anyway"
```

**Windows Defender SmartScreen** performs a similar role to Gatekeeper on macOS — checking a file's reputation and digital signature before running it, and warning you if it's from an unrecognized publisher or hasn't been seen widely before. This doesn't mean the software is necessarily unsafe, just that Windows can't yet vouch for it. `.msi` (Microsoft Installer) files are the more structured, standardized format often used by enterprise software, since they support features like unattended/scripted installs across many machines at once.

## 6.4 Windows Security (Defender, Firewall)

Windows includes built-in security tools active by default, without requiring third-party antivirus software for baseline protection:

- **Windows Defender Antivirus** — real-time scanning for malware, viruses, and other threats, updated automatically through Windows Update.
- **Windows Defender Firewall** — filters incoming and outgoing network traffic, blocking unsolicited connections by default while allowing traffic you or an app has explicitly approved.
- **Windows Security app** — the central dashboard combining antivirus, firewall, device performance, and account protection status into one place, reachable from Settings or the Taskbar's shield icon.
- **BitLocker** (Pro editions and above, from Lesson 1) — encrypts an entire drive, so its contents are unreadable without the correct key if the physical drive is lost or stolen.

Third-party antivirus and firewall software can still be installed and will typically take over from Windows Defender automatically, but modern Windows Defender is considered reasonably capable protection on its own for most everyday users — a significant change from the platform's older reputation for needing separate antivirus software.

---

[Previous](./[5]-The-Command-Line-On-Windows.md) | [Table of Contents](./[0]-Introduction-to-Windows.md) | [Next](./[7]-Using-Windows-For-Development.md)
