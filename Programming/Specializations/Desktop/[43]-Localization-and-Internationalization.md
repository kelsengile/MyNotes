[Previous](./[42]-Desktop-Accessibility.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[44]-Performance-Optimization.md)

*Best Practices*

# Lesson 43 - Localization & Internationalization

## 43.1 Internationalization vs Localization

**Internationalization (i18n)** is designing your app so it *can* support multiple languages/regions — externalizing strings, avoiding hardcoded formats. **Localization (l10n)** is the actual work of adapting the app to a specific language/region — translating strings, adjusting layouts, adapting formats. i18n is a one-time architectural investment; l10n is ongoing, per-language work.

---

## 43.2 Externalizing Strings

Never hardcode user-facing text inline; store it in resource files keyed by identifier, loaded based on the active locale:

```csharp
string message = Resources.GetString("SaveSuccessMessage");
```

```json
// en.json
{ "saveSuccess": "File saved successfully." }
// ja.json
{ "saveSuccess": "ファイルが正常に保存されました。" }
```

---

## 43.3 Locale-Aware Formatting

Dates, numbers, currency, and even sort order vary by locale — never format these manually with string concatenation. Use the platform's locale-aware formatting APIs (`CultureInfo` in .NET, `Intl` in JavaScript) so `1,234.56` correctly becomes `1.234,56` in locales that swap the decimal and thousands separators, and dates render in the order (day/month/year vs month/day/year) users expect.

---

## 43.4 Layout for Different Languages

Translated text can be significantly longer or shorter than the source language (German text often runs 30% longer than English), so layouts must accommodate text-length variation rather than fixed-width labels that truncate. Right-to-left languages (Arabic, Hebrew) require mirroring the entire UI layout, not just the text direction — most modern frameworks support this via a layout-direction flag rather than manual per-control mirroring.

[Previous](./[42]-Desktop-Accessibility.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[44]-Performance-Optimization.md)
