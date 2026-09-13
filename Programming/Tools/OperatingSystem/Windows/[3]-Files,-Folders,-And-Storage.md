[Previous](./[2]-The-Windows-Interface.md) | [Table of Contents](./[0]-Introduction-to-Windows.md) | [Next](./[4]-System-Settings-And-Customization.md)

*The Interface And File System*

# Lesson 3 - Files, Folders, And Storage

## 3.1 The Drive Letter System

Windows organizes storage using **drive letters** rather than the single unified folder tree macOS and Linux use. Each physical drive, partition, or removable device gets its own letter, shown in File Explorer under "This PC":

```
This PC
├── 🖴 Local Disk (C:)     ← main system drive
├── 🖴 Data (D:)           ← secondary internal/external drive
├── 💿 DVD Drive (E:)
└── 🔌 USB Drive (F:)
```

`C:` is almost always the primary drive, holding Windows itself, installed programs, and (by default) your personal files — a holdover from early PC-DOS conventions where `A:` and `B:` were reserved for floppy disk drives. A full file path looks like `C:\Users\Jane\Documents\report.docx`, using backslashes (`\`) rather than the forward slashes (`/`) macOS and Linux use.

## 3.2 Common Folders (Program Files, Users, AppData)

The `C:` drive follows a fairly standardized top-level layout:

```
C:\
├── Program Files\        ← installed 64-bit applications
├── Program Files (x86)\  ← installed 32-bit applications
├── Users\
│   └── Jane\
│       ├── Desktop\
│       ├── Documents\
│       ├── Downloads\
│       ├── Pictures\
│       └── AppData\      ← hidden — app settings, caches
├── Windows\               ← the OS itself
```

- **Program Files / Program Files (x86)** — where most installed applications live; the split exists for backward compatibility with older 32-bit software.
- **Users\[YourName]** — the Windows equivalent of a Home folder, containing your personal Desktop, Documents, Downloads, and other standard folders.
- **AppData** (hidden by default) — stores per-app settings, caches, and local data, split into `Local`, `LocalLow`, and `Roaming` subfolders depending on how that data should sync or persist.

You can reveal hidden folders like AppData from File Explorer's **View → Show → Hidden items**.

## 3.3 OneDrive Integration

**OneDrive** is Microsoft's cloud storage service, and — much like iCloud Drive on macOS — it integrates directly into File Explorer rather than requiring a separate app. When set up, your Desktop, Documents, and Pictures folders can be redirected ("Known Folder Move") to sync automatically:

```
     Windows PC                OneDrive              Phone / Other PC
┌───────────────────┐       ┌──────────┐       ┌───────────────────┐
│ Desktop\           │ <──> │ OneDrive │ <──>  │ OneDrive app /     │
│ Documents\         │       │  Cloud   │       │ office.com         │
└───────────────────┘       └──────────┘       └───────────────────┘
```

Files can be set to **Files On-Demand**, appearing in File Explorer with a small cloud icon even before they're downloaded locally, saving disk space until a file is actually opened. OneDrive comes built into Windows 10 and 11 and is tied to a Microsoft account, the same account used for the Microsoft Store and (optionally) signing into Windows itself.

## 3.4 File Permissions (NTFS Basics)

Windows' primary file system, **NTFS** (New Technology File System), supports detailed permissions on files and folders — controlling which users or groups can read, write, modify, or execute a given item.

| Permission | Allows |
|---|---|
| Read | View file/folder contents |
| Write | Create new files/folders |
| Modify | Change or delete existing content |
| Full Control | All of the above, plus changing permissions |

These permissions can be viewed and edited by right-clicking a file or folder, choosing **Properties → Security**. In practice, most home users never need to touch this directly — permissions matter more in shared or business environments, where an IT administrator restricts which employees can access which folders on a shared network drive. NTFS also supports other features beyond basic Unix-style permissions, such as file compression, encryption (via BitLocker on Pro editions and above, mentioned in Lesson 1), and detailed audit logging of file access.

---

[Previous](./[2]-The-Windows-Interface.md) | [Table of Contents](./[0]-Introduction-to-Windows.md) | [Next](./[4]-System-Settings-And-Customization.md)
