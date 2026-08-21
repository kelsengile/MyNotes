[Previous](./[45]-Accessibility-in-Games.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[47]-Monetization-Basics.md)

*Best Practices*

# Lesson 46 - Localization for Games

## 46.1 What Localization Involves

**Localization** is adapting a game for a different language and region — not just translating text, but also adjusting things like date/number formats, currency, voice-over, and even content that may be culturally sensitive or restricted in a given region. It's distinct from, but closely related to, **internationalization** (below), and both are usually necessary to release a game successfully in multiple markets.

---

## 46.2 Internationalization vs. Localization

- **Internationalization (i18n)** — preparing the game's *code and systems* to support multiple languages/regions in the first place — for example, never hardcoding player-facing text directly in code, and instead pulling it from a language file keyed by an identifier (e.g. `"button_start"` → "Start" / "Empezar" / "開始").
- **Localization (l10n)** — the actual process of translating and adapting content into a specific target language/region, built on top of the internationalization work.

Doing internationalization properly from the start of a project makes adding new languages later dramatically cheaper — retrofitting it into a game with hardcoded text usually means touching every screen in the game.

---

## 46.3 Text Expansion and UI Considerations

Translated text is very rarely the same length as the source text — German and Finnish text, for example, commonly run 20–35% longer than English for the same meaning, while some languages are shorter. UI layouts need to accommodate this **text expansion**, either through flexible, auto-resizing UI elements or by designing with extra space as a buffer from the start, so translated text doesn't get cut off or overlap other elements.

---

## 46.4 Culturalization

Beyond direct translation, **culturalization** adapts content to be appropriate or resonant for a specific culture:

- Adjusting content that may be restricted or considered offensive in certain regions (imagery, symbols, references).
- Adapting humor, idioms, and references that don't translate directly and would otherwise read as confusing or meaningless.
- Region-specific content ratings and legal requirements (see also Lesson 43), which can vary significantly by country.

[Previous](./[45]-Accessibility-in-Games.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[47]-Monetization-Basics.md)
