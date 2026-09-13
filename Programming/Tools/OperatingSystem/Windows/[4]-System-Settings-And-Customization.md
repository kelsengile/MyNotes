[Previous](./[3]-Files,-Folders,-And-Storage.md) | [Table of Contents](./[0]-Introduction-to-Windows.md) | [Next](./[5]-The-Command-Line-On-Windows.md)

*The Interface And File System*

# Lesson 4 - System Settings And Customization

## 4.1 The Settings App vs Control Panel

Windows currently has **two** overlapping places to change system configuration — a legacy holdover from a long, gradual redesign:

| | Settings App | Control Panel |
|---|---|---|
| Introduced | Windows 8 | Original, since Windows 1.0 |
| Design | Modern, touch-friendly | Classic, dialog-box based |
| Coverage | Growing every release | Shrinking every release |
| Status | Actively developed | Being phased out |

```
     Settings App                    Control Panel
┌───────────────────┐          ┌───────────────────┐
│ System             │          │ Programs           │
│ Bluetooth & devices │   ⇄     │ Network and         │
│ Network & internet │  (some   │   Sharing Center    │
│ Personalization    │  overlap)│ Region              │
│ Apps                │          │ (legacy dialogs)    │
└───────────────────┘          └───────────────────┘
```

Microsoft has been migrating features from Control Panel into Settings for years, but some advanced options — certain networking configurations and legacy hardware dialogs, for example — still only exist in Control Panel. Opening either is as simple as searching "Settings" or "Control Panel" from the Taskbar search box.

## 4.2 User Accounts And Permissions

Windows supports both **local accounts** (username and password stored only on that PC) and **Microsoft accounts** (an online account also used for OneDrive, the Microsoft Store, and Xbox). Each account is also assigned a permission level:

| Account Type | Can Install Software / Change System Settings |
|---|---|
| Administrator | Yes |
| Standard User | No (prompted for admin credentials) |

When a Standard user (or even an Administrator, by default) tries to perform an action requiring elevated permissions, Windows shows a **User Account Control (UAC)** prompt — the dimmed-screen dialog asking "Do you want to allow this app to make changes to your device?" This is Windows' equivalent of macOS's admin password prompts, designed to stop malicious software from silently making system-level changes.

## 4.3 Personalization (Themes, Taskbar, Start Menu)

The **Personalization** section of Settings covers most of what makes a Windows install feel like "yours":

- **Themes** — bundle a desktop background, accent color, sounds, and mouse cursor into one saved combination.
- **Background** — a static image, solid color, or slideshow of rotating images.
- **Colors** — choose an accent color and whether the Taskbar/Start menu use Light or Dark mode.
- **Taskbar** — control which icons appear, whether it's centered or left-aligned, and which system tray icons show.
- **Start** — choose which folders appear on the Start menu, and manage pinned apps and "Recommended" file suggestions.

Because Windows runs on such varied hardware, personalization also extends to **display settings** — scaling, resolution, and refresh rate — which matter far more here than on more hardware-uniform systems, since screen sizes and pixel densities vary enormously across manufacturers.

## 4.4 Windows Update

**Windows Update**, found in Settings, is how Microsoft delivers security patches, driver updates, and new features. It runs largely automatically:

```
Microsoft servers → Windows Update service → Downloads in background
                                                    │
                                     Installs at next restart/scheduled time
```

Home edition users have limited control over deferring updates, while Pro and Enterprise editions (Lesson 1) allow more granular control — pausing updates for a set period, or (in managed enterprise environments) having an IT department control rollout timing entirely through tools like Windows Server Update Services (WSUS). Keeping Windows Update current matters even more than on many systems, given how large and frequently targeted the Windows install base is for security threats.

---

[Previous](./[3]-Files,-Folders,-And-Storage.md) | [Table of Contents](./[0]-Introduction-to-Windows.md) | [Next](./[5]-The-Command-Line-On-Windows.md)
