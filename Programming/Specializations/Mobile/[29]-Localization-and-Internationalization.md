[Previous](./[28]-Background-Tasks-and-Services.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[30]-Introduction-to-iOS-Development.md)

*Background & System Integration*

# Lesson 29 - Localization & Internationalization

## 29.1 Internationalization (i18n) vs Localization (l10n)

- **Internationalization** is designing your app so it *can* support multiple languages/regions — separating text out of code, avoiding hard-coded formats.
- **Localization** is the actual process of translating and adapting content for a specific language or region.

You internationalize once; you localize repeatedly for each new market.

---

## 29.2 Externalizing Strings

Instead of hard-coding text directly in UI code, all user-facing text is stored in language-specific resource files and referenced by a key:

```arb
// app_en.arb
{ "welcomeMessage": "Welcome back, {name}!" }
// app_es.arb
{ "welcomeMessage": "¡Bienvenido de nuevo, {name}!" }
```

```dart
Text(AppLocalizations.of(context)!.welcomeMessage(userName))
```

The framework automatically picks the correct file based on the device's language setting.

---

## 29.3 Formatting Dates, Numbers, and Currency

Formatting rules vary significantly by locale — the US uses `MM/DD/YYYY` while much of Europe uses `DD/MM/YYYY`; decimal separators differ (`1,000.50` vs `1.000,50`). Never format these manually with string concatenation — use locale-aware formatting APIs (`intl` package in Flutter, `NumberFormatter`/`DateFormatter` in Swift) so the correct convention is applied automatically.

---

## 29.4 Layout Considerations

- **Right-to-left (RTL) languages** like Arabic and Hebrew mirror the entire UI layout horizontally — frameworks handle this automatically if you use logical direction properties (`start`/`end`) instead of literal `left`/`right`.
- **Text expansion**: translated text is often longer than the original English (German text can run 30% longer) — layouts must accommodate this rather than assuming fixed text lengths, or labels will visibly clip or wrap awkwardly.

[Previous](./[28]-Background-Tasks-and-Services.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[30]-Introduction-to-iOS-Development.md)
