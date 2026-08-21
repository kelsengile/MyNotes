[Previous](./[42]-Building-and-Exporting-a-Game.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[44]-Game-Design-Principles.md)

*Polishing & Shipping*

# Lesson 43 - Publishing to App Stores & Platforms (Steam, App Store, Google Play)

## 43.1 Platform Requirements and Certification

Most major platforms require a game to pass a **certification/review process** before it can be released, checking things like stability, content ratings, and compliance with platform-specific technical requirements (e.g. proper handling of being backgrounded on mobile, or supporting a platform's achievement system). Certification requirements should be reviewed early in development, since failing certification close to a planned launch date can cause significant delays.

---

## 43.2 Store Pages and Metadata

A store listing is often a player's very first impression of the game, independent of the game itself:

- **Screenshots and trailers** — visual material showcasing gameplay, usually the biggest factor in a player's decision to click.
- **Description and tags/genres** — helps the storefront's search and recommendation systems surface the game to the right players.
- **Age/content rating** — required by most storefronts and legally required in many regions, based on the game's content (violence, language, etc.).

---

## 43.3 Platform-Specific Considerations

- **Steam** — a PC-focused storefront with its own SDK (Steamworks) for achievements, cloud saves, and multiplayer matchmaking; supports wishlists and a review system that heavily influences visibility.
- **Apple App Store** — requires following Apple's Human Interface Guidelines and review requirements; releases are managed through App Store Connect.
- **Google Play** — supports staged rollouts (releasing to a percentage of users first) and has its own review and policy requirements, managed through the Google Play Console.

Each platform has distinct submission tools, review timelines, and content policies, so cross-platform releases typically need to budget separate time for each store's process.

---

## 43.4 Post-Launch Updates and Patching

Launch is rarely the end of a game's development. Post-launch work commonly includes:

- **Patches** — fixes for bugs discovered after release, often urgently for anything crash- or progress-blocking.
- **Content updates** — new levels, features, or balance changes to keep a live game engaging over time.
- **Version management** — most platforms require every update to go through the same review process as the initial release, so patch cadence has to account for that turnaround time.

[Previous](./[42]-Building-and-Exporting-a-Game.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[44]-Game-Design-Principles.md)
