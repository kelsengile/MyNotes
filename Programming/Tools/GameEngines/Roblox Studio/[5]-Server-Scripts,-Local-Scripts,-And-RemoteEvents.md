[Previous](./[4]-Introduction-To-Luau-Scripting.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[6]-Building-Parts,-Models,-And-Terrain.md)

*Building And Scripting*

# Lesson 5 - Server Scripts, Local Scripts, And RemoteEvents

## 5.1 The Client-Server Model In Roblox

Every Roblox experience runs across two separate execution contexts at once:

- **The Server** — one authoritative simulation, running on Roblox's infrastructure, that every connected player's game state is synced to. There is exactly one server per running experience instance.
- **The Client** — a copy running on each individual player's device, responsible for rendering, input, camera, and sound for that specific player.

```
                 ┌─────────────┐
                 │   Server    │  ← one authoritative simulation
                 └──────┬──────┘
          ┌─────────────┼─────────────┐
          ▼              ▼              ▼
     ┌─────────┐    ┌─────────┐    ┌─────────┐
     │ Client A│    │ Client B│    │ Client C│  ← one per player
     └─────────┘    └─────────┘    └─────────┘
```

Most of what happens in `Workspace` **replicates** automatically from server to clients — if the server moves a Part, every client sees it move too. But scripts don't replicate their *execution* this way: code in a server Script only ever runs on the server, and code in a LocalScript only ever runs on the one client it belongs to. This split exists for a reason covered in 5.4: the server is trusted, clients are not.

---

## 5.2 Script vs LocalScript vs ModuleScript

Roblox has three distinct script types, and choosing the right one is a foundational decision for every piece of logic you write:

| Type | Runs On | Typical Location | Use For |
|---|---|---|---|
| **Script** | Server only | `ServerScriptService`, or parented under something in `Workspace` | Game logic, spawning enemies, validating actions, anything that must be trustworthy |
| **LocalScript** | The owning client only | `StarterPlayerScripts`, `StarterGui`, or inside a `ScreenGui` | Camera control, UI responses, input handling, purely visual/client-side effects |
| **ModuleScript** | Neither — it doesn't run on its own | `ReplicatedStorage` (shared) or `ServerScriptService` (server-only) | Reusable code/functions, `require()`-d by Scripts or LocalScripts |

A `ModuleScript` returns a table of functions/values via `return`, and other scripts pull it in with `require()`:

```lua
-- ModuleScript named "DamageUtils" in ReplicatedStorage
local DamageUtils = {}

function DamageUtils.calculate(baseDamage, multiplier)
    return baseDamage * multiplier
end

return DamageUtils
```

```lua
-- In a Script or LocalScript
local DamageUtils = require(game.ReplicatedStorage.DamageUtils)
local finalDamage = DamageUtils.calculate(10, 1.5)
```

Because a ModuleScript's code only actually runs the first time it's required (and is cached after that), it's the standard way to share logic between server and client without duplicating code — as long as the logic itself doesn't need to be secret (see 5.4).

---

## 5.3 RemoteEvents And RemoteFunctions

Since server code and client code never run "in the same place," they need an explicit bridge to communicate — that's what **RemoteEvent** and **RemoteFunction** Instances are for, typically placed in `ReplicatedStorage` so both sides can find them.

**RemoteEvent** — fire-and-forget, one direction at a time:

```lua
-- Server: listening for a client's request
local remote = game.ReplicatedStorage.UseItemEvent
remote.OnServerEvent:Connect(function(player, itemName)
    print(player.Name, "used", itemName)
end)
```

```lua
-- LocalScript: firing the request
local remote = game.ReplicatedStorage.UseItemEvent
remote:FireServer("HealthPotion")
```

**RemoteFunction** — like a RemoteEvent, but waits for and returns a response, used when the client needs an answer back:

```lua
-- Server
local remoteFunction = game.ReplicatedStorage.GetPriceFunction
remoteFunction.OnServerInvoke = function(player, itemName)
    return 100   -- price
end
```

```lua
-- LocalScript
local price = game.ReplicatedStorage.GetPriceFunction:InvokeServer("Sword")
print("Price is:", price)
```

The server can also fire events *to* clients (`remote:FireClient(player, ...)` or `:FireAllClients(...)`), the reverse direction — used for things like telling a specific client's UI to show a notification.

---

## 5.4 Security: Why You Never Trust The Client

This is the single most important security principle in Roblox development: **the client runs on the player's own machine, and a sufficiently motivated player can modify anything running there.** LocalScripts, client-side variables, and anything sent *from* the client to the server via a RemoteEvent should always be treated as potentially falsified.

Consider a naive (and exploitable) approach:

```lua
-- BAD: trusts a value the client sends directly
remote.OnServerEvent:Connect(function(player, damageAmount)
    enemy.Health -= damageAmount   -- a modified client could send any number here
end)
```

A modified client could fire this event with `damageAmount = 999999`, instantly killing anything. The fix is to have the **server calculate the outcome itself**, using only the client's *intent* (e.g. "I attacked") rather than trusting a value it supplies directly:

```lua
-- GOOD: server decides the actual damage
remote.OnServerEvent:Connect(function(player, targetEnemy)
    if isValidTarget(player, targetEnemy) then
        local damage = calculateDamage(player)  -- server-side logic, not client input
        targetEnemy.Health -= damage
    end
end)
```

The general rule, worth internalizing before writing any multiplayer logic: **anything that affects game state that matters (currency, health, item ownership) must be decided and enforced on the server.** LocalScripts and the client are for responsiveness and presentation — showing a health bar update immediately, playing an attack animation — never for deciding the actual outcome of an action.

[Previous](./[4]-Introduction-To-Luau-Scripting.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[6]-Building-Parts,-Models,-And-Terrain.md)
