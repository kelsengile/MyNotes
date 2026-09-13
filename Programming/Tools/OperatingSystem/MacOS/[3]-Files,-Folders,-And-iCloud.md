[Previous](./[2]-The-macOS-Interface.md) | [Table of Contents](./[0]-Introduction-to-MacOS.md) | [Next](./[4]-System-Settings-And-Customization.md)

*The Interface And Finder*

# Lesson 3 - Files, Folders, And iCloud

## 3.1 The Home Folder Structure

Every macOS user account gets a **Home folder**, named after the account's short username (e.g., `/Users/jane`). It's the personal space where your files, preferences, and app data live, and it contains a standard set of subfolders that most apps expect to find:

```
/Users/jane/
├── Desktop/
├── Documents/
├── Downloads/
├── Movies/
├── Music/
├── Pictures/
├── Public/
└── Library/   (hidden — app support files, caches, preferences)
```

- **Desktop** — anything you save to the Desktop shows up as icons on your actual screen background.
- **Documents, Movies, Music, Pictures** — the conventional homes for those file types; many apps default to saving here.
- **Downloads** — where Safari and other apps place downloaded files by default.
- **Public** — a folder other users on the same network can access, if file sharing is enabled.
- **Library** — hidden by default because it stores technical data (settings, caches) that most users never need to touch directly. You can reach it by holding **Option** while clicking the Finder's **Go** menu.

You can jump to your Home folder anytime with **⌘ + Shift + H**, or reach any hidden folder using **⌘ + Shift + G** ("Go to Folder") and typing a path.

## 3.2 Tags, Smart Folders, And Quick Look

Beyond plain folders, Finder offers a few tools for organizing and previewing files without moving them around:

- **Tags** — colored labels (Red, Orange, Yellow, etc., or custom names) you can attach to any file or folder. A single file can carry multiple tags, and you can then find everything with a given tag from the sidebar, regardless of where the files physically live.
- **Smart Folders** — a saved search that behaves like a folder. For example, a Smart Folder could show "every PDF modified in the last 7 days" and will update automatically as new files match, without you ever placing anything inside it.
- **Quick Look** — select any file and press the **Space bar** to preview it instantly (images, PDFs, videos, even some code files) without opening its full application.

| Tool | Purpose | How To Use |
|---|---|---|
| Tags | Cross-folder categorization | Right-click file → Tags |
| Smart Folder | Auto-updating saved search | File → New Smart Folder |
| Quick Look | Instant preview | Select file → Space bar |

## 3.3 iCloud Drive And Syncing

**iCloud Drive** is Apple's cloud storage service, and it integrates directly into the Home folder structure rather than living in a separate app. When enabled, your Desktop and Documents folders can be set to sync automatically:

```
   MacBook                iCloud                 iPhone / iPad
┌───────────┐          ┌──────────┐          ┌───────────────┐
│ Desktop/  │  <───>   │  iCloud  │  <───>   │  Files App     │
│ Documents/│          │  Drive   │          │  (iCloud Drive)│
└───────────┘          └──────────┘          └───────────────┘
```

This means a file saved on your Mac's Desktop can appear moments later in the Files app on your iPhone, and vice versa. It also means files can show a small cloud icon if they haven't finished downloading locally yet — useful to know so you don't panic thinking a file has "disappeared." iCloud also syncs Photos, Notes, Mail, Safari bookmarks, and more, all tied to the same Apple ID.

## 3.4 Time Machine Backups

**Time Machine** is macOS's built-in backup system. Once you connect an external drive and turn Time Machine on, it automatically keeps:

- Hourly backups for the past 24 hours
- Daily backups for the past month
- Weekly backups for all previous months (as space allows)

This lets you restore not just "the latest version" of a file, but a version from a specific point in the past — useful if a file gets corrupted or you realize you deleted something important weeks ago. Restoring is done through the **Time Machine** interface, which lets you visually "travel back" through a stack of past Finder windows to find the version you need.

> **Note:** Time Machine backs up to a local or network drive you provide — it is separate from iCloud, which syncs specific folders online. Many users rely on both for redundancy.

---

[Previous](./[2]-The-macOS-Interface.md) | [Table of Contents](./[0]-Introduction-to-MacOS.md) | [Next](./[4]-System-Settings-And-Customization.md)
