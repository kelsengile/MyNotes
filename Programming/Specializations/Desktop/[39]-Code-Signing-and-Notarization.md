[Previous](./[38]-Building-and-Packaging-for-Release.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[40]-Installers-and-Auto-Updates.md)

*Distribution*

# Lesson 39 - Code Signing & Notarization

## 39.1 Why Sign an App

Code signing cryptographically attaches a certificate-backed signature to your app binary, letting the OS and the user verify who published it and that it hasn't been tampered with since signing. Unsigned apps trigger scary warnings ("Unknown Publisher", Gatekeeper blocking launch) that erode user trust and, on some platforms, prevent the app from running at all by default.

---

## 39.2 Windows Code Signing

Windows signing uses an Authenticode certificate from a trusted Certificate Authority, applied to the executable/installer with a tool like `signtool`:

```bash
signtool sign /f certificate.pfx /p <password> /t http://timestamp.digicert.com MyApp.exe
```

Timestamping the signature means it stays valid even after the certificate itself expires, since the signature can be proven to have existed while the certificate was still valid.

---

## 39.3 macOS Signing and Notarization

macOS requires both signing (with a Developer ID certificate from Apple) and **notarization** — submitting the signed app to Apple's automated malware-scanning service, which returns a ticket that gets stapled to the app. Without notarization, Gatekeeper blocks the app from launching on modern macOS with an unremovable-feeling warning.

```bash
codesign --sign "Developer ID Application: Your Name" MyApp.app
xcrun notarytool submit MyApp.zip --keychain-profile "AC_PASSWORD" --wait
xcrun stapler staple MyApp.app
```

---

## 39.4 Linux Signing

Linux has no single OS-enforced signing gate like Gatekeeper, but package formats have their own trust mechanisms: `.deb`/`.rpm` repositories commonly use GPG-signed packages, and Flatpak/Snap stores verify submissions through their own review and signing pipelines.

[Previous](./[38]-Building-and-Packaging-for-Release.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[40]-Installers-and-Auto-Updates.md)
