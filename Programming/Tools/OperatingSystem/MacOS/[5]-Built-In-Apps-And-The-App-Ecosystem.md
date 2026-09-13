[Previous](./[4]-System-Settings-And-Customization.md) | [Table of Contents](./[0]-Introduction-to-MacOS.md) | [Next](./[6]-The-Terminal-On-macOS.md)

*System Features*

# Lesson 5 - Built-In Apps And The App Ecosystem

## 5.1 Default Apps (Safari, Mail, Notes, Preview)

Every Mac ships with a set of first-party apps that cover the basics without installing anything extra:

| App | Purpose |
|---|---|
| **Safari** | Apple's web browser, tuned for battery efficiency and privacy on Mac hardware |
| **Mail** | A unified inbox for Gmail, iCloud, Outlook, and other email accounts |
| **Notes** | Quick note-taking with support for checklists, sketches, and folders, synced via iCloud |
| **Preview** | Opens images and PDFs, and can also annotate, sign, or lightly edit them |
| **Calendar** | Schedules and events, syncable with iCloud, Google, or Outlook calendars |
| **Photos** | Stores, organizes, and edits your photo library, with iCloud Photo Library sync |

These apps are intentionally simple compared to dedicated third-party alternatives, but they're deeply integrated with the rest of the system — for instance, a PDF can be signed in Preview with a signature you drew once using the trackpad, and that signature is remembered for future documents.

## 5.2 The Mac App Store

The **App Store** on Mac works much like its iPhone counterpart: a curated storefront where developers submit apps for review before they're listed. Apps distributed this way are automatically:

- Code-signed and reviewed by Apple before release
- Sandboxed, meaning they can only access the specific files and system resources they've requested
- Kept up to date automatically through the App Store's update mechanism

```
Developer → submits app → Apple App Review → Published on Mac App Store → User installs
```

Not every Mac app is available here, though — many well-known developer tools and utilities are distributed outside the App Store entirely, which is where Gatekeeper comes in.

## 5.3 Installing Apps Outside The App Store (Gatekeeper)

macOS allows installing apps downloaded directly from a developer's website, not just the App Store — but it protects users from unsafe software with **Gatekeeper**, a system that checks whether an app is signed with a valid Apple Developer certificate and, ideally, notarized (scanned by Apple for known malware signatures) before allowing it to run.

```
Download .dmg/.pkg → Gatekeeper checks signature/notarization
                          │
              ┌───────────┴───────────┐
        Signed & notarized      Unsigned / unknown
              │                       │
        Opens normally      "Cannot verify developer" warning
                                      │
                     User can still allow it via
                     System Settings → Privacy & Security
```

This is why you'll sometimes see a warning like *"macOS cannot verify that this app is free of malware"* — it doesn't necessarily mean the app is dangerous, just that Apple hasn't independently reviewed it the way it does App Store submissions. Developers and experienced users often install trusted tools this way, especially command-line utilities we'll cover in Lesson 6.

## 5.4 Continuity Features (Handoff, Universal Clipboard)

Because iPhone, iPad, and Mac all sign in with the same Apple ID, macOS offers several **Continuity** features that let work flow between devices with almost no setup:

- **Handoff** — start writing an email on your iPhone, then see a Handoff icon appear in your Mac's Dock to continue exactly where you left off.
- **Universal Clipboard** — copy text or an image on one device, paste it on another, within a short time window.
- **Continuity Camera** — use your iPhone as a high-quality webcam or scanner for your Mac.
- **AirDrop** — wirelessly send files, photos, or links directly between nearby Apple devices without email or cloud services.
- **Phone calls and texts on Mac** — answer an iPhone call or send an SMS directly from your Mac.

These features require both devices to be signed into the same Apple ID, have Wi-Fi and Bluetooth enabled, and (for most of them) be reasonably close to each other — they rely on a peer-to-peer connection, not just the internet.

---

[Previous](./[4]-System-Settings-And-Customization.md) | [Table of Contents](./[0]-Introduction-to-MacOS.md) | [Next](./[6]-The-Terminal-On-macOS.md)
