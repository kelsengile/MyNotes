[Previous](./[25]-macOS-Development.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[27]-Cross-Platform-Frameworks.md)

*Platform-Specific & Cross-Platform Frameworks*

# Lesson 26 - Linux Desktop Development (GTK/Qt)

## 26.1 A Fragmented but Standardized Ecosystem

Unlike Windows and macOS, Linux has multiple desktop environments (GNOME, KDE, XFCE, and others), each with its own visual conventions. Two toolkits dominate native Linux GUI development, and both target multiple desktop environments through shared standards (icon themes, D-Bus, XDG paths):

- **GTK** — used by GNOME and many GNOME-based distributions; commonly paired with C, Python, or Rust.
- **Qt** — used by KDE; also the most popular choice for genuinely cross-platform native-feeling apps (see Lesson 27).

---

## 26.2 A Basic GTK Example

```python
import gi
gi.require_version("Gtk", "4.0")
from gi.repository import Gtk

def on_click(button):
    button.set_label("Clicked!")

app = Gtk.Application()
def on_activate(app):
    window = Gtk.ApplicationWindow(application=app, title="Hello")
    button = Gtk.Button(label="Click me")
    button.connect("clicked", on_click)
    window.set_child(button)
    window.present()

app.connect("activate", on_activate)
app.run(None)
```

---

## 26.3 Desktop Integration Standards

Linux desktop integration relies on freedesktop.org standards rather than a single vendor API: `.desktop` files register an app in application launchers, the XDG Base Directory spec defines where config/data/cache files belong, and D-Bus is the standard inter-process communication mechanism used for notifications, media controls, and cross-app messaging.

---

## 26.4 Packaging Challenges

Linux distribution is more fragmented than Windows or macOS: apps may ship as distro-specific packages (`.deb`, `.rpm`), or as universal formats that bundle their own dependencies — **Flatpak**, **Snap**, and **AppImage** — which trade a larger download size for working consistently across distributions without relying on system library versions matching.

[Previous](./[25]-macOS-Development.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[27]-Cross-Platform-Frameworks.md)
