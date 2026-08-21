[Previous](./[27]-Cross-Platform-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[29]-Inter-Process-Communication.md)

*System Integration*

# Lesson 28 - Working with the Operating System (Notifications, Tray Icons)

## 28.1 System Notifications

Desktop apps can raise native OS notifications that appear outside the app window, even when it's minimized — Windows toast notifications, macOS Notification Center banners, Linux notifications via D-Bus. Use them for events the user should know about even when not actively looking at the app (a download finishing, a reminder firing), not for routine feedback that belongs in the UI itself.

```javascript
new Notification('Export complete', { body: 'Your file has been saved.' });
```

---

## 28.2 Tray/Menu Bar Icons

A tray icon (Windows/Linux system tray, macOS menu bar) lets an app stay accessible while its main window is hidden — common for utilities, chat apps, and background services. It usually exposes a small context menu (Open, Settings, Quit) and can update its icon to reflect state (e.g. new messages).

---

## 28.3 Clipboard Access

Reading and writing the system clipboard lets an app support copy/paste beyond default text controls — copying rich content (images, custom data formats) or programmatically populating the clipboard from a "Copy link" button. Clipboard APIs are asynchronous in some frameworks since access may require a permission check.

---

## 28.4 Other OS Integration Points

Depending on the platform, apps can also integrate with: global keyboard shortcuts (working even when the app isn't focused), the OS's share sheet, drag-and-drop from the file explorer/Finder into the app window, and "Open with" file-type associations that launch the app when a user double-clicks a matching file.

[Previous](./[27]-Cross-Platform-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[29]-Inter-Process-Communication.md)
