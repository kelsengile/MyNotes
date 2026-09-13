[Previous](./[0]-Introduction-to-Windows.md) | [Table of Contents](./[0]-Introduction-to-Windows.md) | [Next](./[2]-The-Windows-Interface.md)

*Getting Started*

# Lesson 1 - What Is Windows

## 1.1 Windows As An Operating System

Windows is Microsoft's operating system, and unlike macOS, it isn't tied to hardware built by the same company that makes the OS. Instead, Microsoft licenses Windows to dozens of hardware manufacturers — Dell, HP, Lenovo, ASUS, and many more — who each build their own machines around it.

```
        Microsoft (Windows OS)
                 │
   ┌─────────────┼─────────────┬─────────────┐
   │             │              │             │
  Dell           HP           Lenovo         ASUS
 (laptops,    (laptops,      (laptops,     (laptops,
 desktops)    desktops)      desktops)     desktops)
```

This "one OS, many manufacturers" model is the biggest structural difference from macOS. It means an enormous range of hardware configurations, price points, and form factors — from ultra-budget laptops to gaming rigs with custom-built components — but it also means Windows has to work with far more hardware variety than an OS designed for a small, fixed set of machines.

## 1.2 Windows Version History (7, 10, 11...)

Windows has a long release history, and unlike macOS's yearly cadence, major versions have historically been spaced further apart:

| Version | Released | Notable For |
|---|---|---|
| Windows XP | 2001 | Long-lived, widely adopted in homes and business |
| Windows 7 | 2009 | Refined UI after the mixed reception of Vista |
| Windows 8 / 8.1 | 2012/2013 | Touch-first redesign, controversial removal of Start menu |
| Windows 10 | 2015 | Brought the Start menu back; shifted to "Windows as a service" with rolling updates |
| Windows 11 | 2021 | Redesigned interface (centered Taskbar), stricter hardware requirements |

Since Windows 10, Microsoft has moved toward continuous update cycles rather than distinct, separately purchased new versions every few years — most Windows 10 and 11 users receive ongoing feature and security updates through Windows Update rather than buying a new OS outright.

## 1.3 Windows Editions (Home, Pro, Enterprise)

Beyond version numbers, each release of Windows comes in multiple **editions**, which unlock different feature sets for different audiences:

| Edition | Typical User | Key Extras |
|---|---|---|
| **Home** | Everyday consumers | Core features, no domain join |
| **Pro** | Power users, small business | BitLocker encryption, Remote Desktop (host), joining a Windows domain, Hyper-V virtualization |
| **Enterprise** | Large organizations | Advanced security/management tools, volume licensing, deployed via IT departments |
| **Education** | Schools | Similar to Enterprise, discounted licensing for institutions |

Developers in particular often need **Pro** or higher, since features like Hyper-V (for virtual machines) and Group Policy management aren't available on Home editions.

## 1.4 Hardware And Compatibility

Because Windows supports such a wide range of hardware, it relies heavily on **drivers** — small pieces of software that let the OS communicate with a specific piece of hardware (a graphics card, printer, Wi-Fi adapter, etc.). Windows Update automatically distributes many drivers, and manufacturers also provide their own.

Windows 11 introduced stricter minimum requirements compared to Windows 10, most notably requiring a **TPM (Trusted Platform Module) 2.0** chip — a small security chip that handles encryption keys — along with a supported 64-bit processor and UEFI Secure Boot. This was a shift toward baseline hardware security standards, and it's why some older PCs capable of running Windows 10 smoothly cannot officially upgrade to Windows 11.

---

[Previous](./[0]-Introduction-to-Windows.md) | [Table of Contents](./[0]-Introduction-to-Windows.md) | [Next](./[2]-The-Windows-Interface.md)
