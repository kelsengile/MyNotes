[Previous](./[40]-Installers-and-Auto-Updates.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[42]-Desktop-Accessibility.md)

*Distribution*

# Lesson 41 - Distributing via App Stores & Direct Download

## 41.1 App Store Distribution

The Microsoft Store, Mac App Store, and Linux app stores (Snap Store, Flathub) offer built-in discovery, trusted installation, and automatic updates handled by the platform rather than your own updater. In exchange, they impose review processes, sandboxing/entitlement restrictions (Lesson 25), and sometimes a revenue cut on paid apps or in-app purchases.

---

## 41.2 Direct Download Distribution

Distributing an installer directly from your own website avoids store review delays and revenue cuts, and allows capabilities that sandboxed store apps can't have (broader filesystem/system access). The trade-off is that you're fully responsible for your own code signing (Lesson 39), auto-updates (Lesson 40), and building user trust without a store's implicit vetting.

---

## 41.3 Choosing a Distribution Strategy

Many apps use both: a store listing for discoverability and low-friction installs on casual-user platforms, plus a direct download for power users or platforms without a strong store presence (many Linux users prefer distro packages or Flatpak over any single store). Consider your app's need for elevated system access — sandboxed store distribution is a poor fit for apps that fundamentally need broad filesystem or hardware access.

---

## 41.4 Store Submission Basics

Regardless of store, expect to provide: a signed/notarized build meeting the store's packaging format (MSIX, `.pkg`, Snap/Flatpak manifest), a privacy policy if the app collects any data, screenshots and a description, and to pass an automated and/or manual review checking for policy compliance, crashes, and (for paid apps) correct pricing/entitlement setup.

[Previous](./[40]-Installers-and-Auto-Updates.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[42]-Desktop-Accessibility.md)
