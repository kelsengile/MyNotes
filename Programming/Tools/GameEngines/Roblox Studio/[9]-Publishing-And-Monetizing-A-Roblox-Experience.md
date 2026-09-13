[Previous](./[8]-GUI-With-Roblox-UI-Elements.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md)

*Publishing*

# Lesson 9 - Publishing And Monetizing A Roblox Experience

## 9.1 Publishing A Place To Roblox

Unlike Godot's export-to-standalone-build workflow, publishing on Roblox doesn't produce a downloadable file — it uploads your place directly to Roblox's servers, where it becomes immediately joinable through the Roblox client.

From **File > Publish to Roblox** (or the Publish button in the toolbar), you'll either:

- Publish to an **existing** experience you've created before, updating it in place, or
- Create a **new experience**, which prompts you for a name, description, and thumbnail image before the first publish.

```
File > Publish to Roblox
┌────────────────────────────────┐
│  Publish As:                     │
│   ○ Update Existing Experience   │
│   ○ Create New Experience        │
│                                   │
│  Name: [My First Game        ]   │
│  [Publish]                       │
└────────────────────────────────┘
```

Because publishing updates a live experience immediately, it's good practice to test thoroughly in Studio's Play/Run modes (Lesson 2.4) first — an update goes live for any currently-joining players right away, with no separate staging/review step required for most changes.

---

## 9.2 Configuring Experience Settings And Access

Beyond the place itself, an experience has project-wide settings managed from the Roblox website's **Creator Dashboard**, or via Studio's **Game Settings** window (Home tab > Game Settings):

- **Basic Info** — name, description, genre, and the thumbnail/icon shown in search and on the experience's page.
- **Permissions** — who can access the experience: Public (anyone), Friends of players, or Private (only you and collaborators) — useful for keeping a work-in-progress hidden while testing with others.
- **Access & Age Rating** — Roblox requires experiences to be accurately rated for their content, affecting which age groups can discover and join.
- **Localization** — optional translated strings for text shown in your experience, letting Roblox automatically serve the right language per player.

Access settings matter a lot during development specifically: keeping an experience **Private** while iterating (only inviting specific collaborators) avoids exposing an unfinished project publicly before you're ready.

---

## 9.3 Monetization (Game Passes, Developer Products)

Roblox provides two primary built-in monetization tools, and picking the right one depends on whether what you're selling is a one-time unlock or something repeatable:

| Type | Purchased | Use For |
|---|---|---|
| **Game Pass** | Once per player, permanently | Permanent unlocks: a VIP area, a cosmetic skin, a persistent gameplay perk |
| **Developer Product** | Any number of times | Consumables: in-game currency, extra lives, temporary boosts — anything meant to be bought repeatedly |

Both are created from the Creator Dashboard, then checked for and granted from **server-side** code, following the exact same "never trust the client" principle from Lesson 5.4:

```lua
local MarketplaceService = game:GetService("MarketplaceService")
local GAME_PASS_ID = 123456789

local function playerHasVIP(player)
    local success, hasPass = pcall(function()
        return MarketplaceService:UserOwnsGamePassAsync(player.UserId, GAME_PASS_ID)
    end)
    return success and hasPass
end
```

```lua
-- Developer Products are granted via a ProcessReceipt callback,
-- which Roblox calls once a purchase is confirmed server-side
MarketplaceService.ProcessReceipt = function(receiptInfo)
    local player = game.Players:GetPlayerByUserId(receiptInfo.PlayerId)
    if player and receiptInfo.ProductId == COIN_PACK_ID then
        grantCoins(player, 100)
    end
    return Enum.ProductPurchaseDecision.PurchaseGranted
end
```

Robux earned from sales convert to real payouts through the **Developer Exchange (DevEx)** program once a creator meets Roblox's eligibility thresholds, covered in full on the Roblox Creator Documentation site.

---

## 9.4 Community Guidelines And Moderation

Every published experience is subject to Roblox's **Community Standards**, which cover both the experience's content (no prohibited content: graphic violence, hate speech, inappropriate content given Roblox's large population of younger players) and its behavior (no exploits/cheating tools, no deceptive monetization practices).

A few practical implications for creators:

- Roblox uses a mix of automated and human moderation; experiences and user-generated content (including chat) can be flagged and actioned, including removal, for violations.
- Because a large share of Roblox's audience is under 13, extra restrictions apply around chat filtering and content appropriate for that age group — Studio's default `TextService`-filtered chat handles most of this automatically, and bypassing it is against platform rules.
- Exploits — modified clients that violate the "never trust the client" boundary from Lesson 5.4 — are an active, ongoing concern on the platform; following the server-authoritative patterns taught throughout this Topic is your primary defense as a developer, not just a best practice.

Reviewing Roblox's official Community Standards and Terms of Service (linked from the Creator Dashboard) before publishing anything intended for a real audience is worth the time — violations can result in anything from content removal to account termination, regardless of whether they were intentional.

---

This concludes the **Introduction to Roblox Studio** Topic. From here, the natural next step is building a small complete experience — an obstacle course or a simple tycoon — applying Lessons 3 through 8 together: Instances and Services for structure, Luau and the client-server model for logic, Parts/Terrain/Constraints for the world, and UI for player feedback, before publishing it with Lesson 9.

[Previous](./[8]-GUI-With-Roblox-UI-Elements.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md)
