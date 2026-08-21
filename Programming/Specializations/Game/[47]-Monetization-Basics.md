[Previous](./[46]-Localization-for-Games.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[48]-Analytics-and-Player-Telemetry.md)

*Best Practices*

# Lesson 47 - Monetization Basics (Ads, IAP, Premium)

## 47.1 Common Monetization Models

- **Premium (paid upfront)** — a single, one-time purchase price to play the full game, common on PC/console.
- **Free-to-play with in-app purchases (IAP)** — the game is free to download and play, with optional purchases inside it; the dominant model on mobile.
- **Subscription** — recurring payment for continued access, often to a library of games or ongoing content in a single live game.
- **Ad-supported** — revenue comes from showing advertisements rather than charging players directly, often combined with IAP as a way to remove ads.

The right model depends heavily on platform, genre, and audience — what works for a mobile puzzle game rarely works for a premium narrative PC game, and vice versa.

---

## 47.2 In-App Purchases

Common categories of IAP include:

- **Cosmetics** — purely visual items (skins, outfits) that don't affect gameplay balance.
- **Consumables** — items used up over time (currency, boosts, extra lives).
- **Convenience** — purchases that save time rather than unlock anything new (skipping a wait timer, extra inventory slots).
- **Gacha/loot boxes** — randomized rewards from a purchase; note that several regions have specific legal disclosure or age-rating requirements around these mechanics, which is worth checking during the platform certification process (Lesson 43).

---

## 47.3 Advertising

- **Interstitial ads** — full-screen ads shown at natural breaks (between levels, after a match), most effective when they don't interrupt active gameplay.
- **Rewarded ads** — optional ads a player chooses to watch in exchange for an in-game reward, generally considered more player-friendly since watching is the player's choice.
- **Banner ads** — small, persistent ads shown during gameplay; effective for revenue but can hurt the perceived quality and immersion of a game if overused.

---

## 47.4 Ethical Considerations

Monetization design has a direct effect on player trust and experience, and poorly designed systems (sometimes called **dark patterns**) — like manipulative pressure to spend, or progression deliberately slowed to push purchases — can generate short-term revenue while damaging a game's reputation and long-term player retention. Designing monetization that feels fair and optional, rather than coercive, is generally better for a game's long-term health, not just its players' goodwill.

[Previous](./[46]-Localization-for-Games.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[48]-Analytics-and-Player-Telemetry.md)
