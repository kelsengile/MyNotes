[Previous](./[6]-Testing-In-Xcode.md) | [Table of Contents](./[0]-Introduction-to-XCode.md)

*Shipping*

# Lesson 7 - Archiving And Distributing An App

## 7.1 Signing And Provisioning Profiles

Every app run on a physical device or submitted to the App Store must be **code signed** — cryptographically proving it comes from a known, registered developer, and hasn't been tampered with since signing.

```
              Signing Identity (Certificate)
                        │
                        ▼
   ┌───────────────────────────────────────┐
   │        Provisioning Profile             │
   │  - Which App ID this covers              │
   │  - Which devices can install it (dev)    │
   │  - Which capabilities are enabled         │
   │  - Which certificate(s) are authorized    │
   └───────────────────────────────────────┘
```

- **Certificate** — proves your identity as a developer (or your organization's), issued by Apple and tied to your Apple Developer account.
- **App ID** — a unique identifier for your app (usually matching the Bundle Identifier from Lesson 3.3), registered in the Apple Developer portal.
- **Provisioning Profile** — ties a certificate, an App ID, and (for development profiles) a list of allowed test devices together, and is what actually gets embedded in the built app.
- **Automatic signing** — under **Signing & Capabilities** in project settings, Xcode can manage all of the above automatically once you select your Team, which is the recommended default for most projects, especially while learning.

**Capabilities** (also configured in Signing & Capabilities) — features like Push Notifications, HealthKit, or iCloud must be explicitly enabled per-app in the developer portal and mirrored here before Xcode will let you use the corresponding APIs.

---

## 7.2 Archiving A Build

An **Archive** is a specially packaged, Release-configuration build intended for distribution, created via **Product → Archive** (only enabled when a real device or "Any iOS Device" is selected as the destination, not a Simulator).

```
Product → Archive
        │
        ▼
┌─────────────────────────┐
│   Organizer Window         │
│  ┌─────────────────────┐  │
│  │ MyApp 1.2.0 (14)       │  │
│  │ Archived: Today, 3:15pm│  │
│  └─────────────────────┘  │
│  [Distribute App]  [Validate App]│
└─────────────────────────┘
```

Once archiving finishes, the **Organizer** window opens automatically, listing every archive you've created for the project. From here:

- **Validate App** — checks the archive against App Store requirements (correct icons present, valid entitlements, etc.) without actually uploading it, catching common mistakes early.
- **Distribute App** — the actual export/upload step, offering different destinations depending on your goal (App Store Connect, Ad Hoc for a fixed device list, Enterprise, or a plain `.ipa` export).

---

## 7.3 TestFlight For Beta Testing

**TestFlight** is Apple's official beta-distribution platform, reached by choosing "App Store Connect" as the distribution destination in the Organizer (7.2) and uploading the build.

```
Xcode Archive → Upload → App Store Connect → TestFlight
                                                  │
                                    ┌─────────────┴─────────────┐
                                    ▼                             ▼
                          Internal Testers                External Testers
                          (up to 100, your team,           (up to 10,000, via
                           no review needed)                 public link, needs
                                                              Beta App Review)
```

- **Internal testing** — anyone added to your App Store Connect team can install a new build almost immediately after upload, no additional Apple review step required.
- **External testing** — for testers outside your organization, requiring a lightweight "Beta App Review" from Apple (typically faster than a full App Store review) before the build becomes available to them.
- **Build expiration** — TestFlight builds expire automatically after 90 days, after which testers can no longer launch that version and a new build must be uploaded.
- **Feedback and crash reports** — testers can submit screenshots and comments directly from the TestFlight app, which surface back in App Store Connect for the developer to review.

---

## 7.4 Submitting To The App Store

The final step reuses the same App Store Connect upload from TestFlight (7.3) — a build already in TestFlight can be selected for release rather than uploading a separate one.

```
App Store Connect
 └── My App
      ├── App Information (name, category, age rating)
      ├── Pricing and Availability
      ├── App Privacy (data collection disclosure)
      ├── Version 1.2.0
      │    ├── Screenshots (per device size)
      │    ├── Description, Keywords, What's New
      │    └── Build: [Select uploaded build]
      └── [Submit for Review]
```

Before submitting, App Store Connect requires:

- **Screenshots** for each supported device size class (though Apple auto-generates some sizes from others in recent versions).
- **App Privacy details** — a declared list of what data types the app collects and how they're used, shown to users on the App Store product page.
- **Metadata** — name, subtitle, description, keywords, support URL, and an age rating questionnaire.

After submission, Apple's **App Review** team evaluates the app against the App Store Review Guidelines — typically completing within a day or two, though it can take longer, and can result in rejection with specific, correctable feedback rather than a permanent block. Once approved, the app can be released immediately, on a scheduled date, or manually at a time you choose, depending on the release option selected during submission.

[Previous](./[6]-Testing-In-Xcode.md) | [Table of Contents](./[0]-Introduction-to-XCode.md)
