[Previous](./[27]-Game-State-Management.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[29]-Dialogue-and-Quest-Systems.md)

*Game Systems & Logic*

# Lesson 28 - Inventory & Item Systems

## 28.1 Modeling Items

Before building an inventory, you need a clear data model for what an "item" actually is. A common approach defines an item's **static data** (shared by every copy of that item type) separately from any **instance data** (specific to one held copy):

- **Static data**: name, description, icon, item type (weapon, consumable, key item), base stats.
- **Instance data**: current stack count, durability remaining, enchantment level.

Many engines support data-driven item definitions (e.g. Unity's ScriptableObjects) that let designers create and tweak new items without touching code.

---

## 28.2 Inventory Data Structures

An inventory is fundamentally a collection (Lesson 9) of item references, plus rules governing it:

- **List/array of slots** — a fixed or dynamic number of inventory slots, each holding zero or one item stack.
- **Dictionary keyed by item ID** — useful for inventories without slot limits, where you only care about total quantities (e.g. crafting materials).

Design decisions here (slot-based vs. weight-based vs. unlimited) significantly affect gameplay feel and should be decided early, since they ripple into UI design, itemization, and balancing.

---

## 28.3 Stacking, Equipping, and Using Items

- **Stacking** — combining multiple copies of the same item into one slot with a quantity counter (e.g. "Potion x5"), rather than taking up five separate slots.
- **Equipping** — moving an item from the general inventory into a dedicated "equipped" slot (weapon, armor), which typically applies its stats to the character while equipped.
- **Using/consuming** — triggering an item's effect (healing, buffs) and reducing its stack count or removing it entirely.

Each of these actions should go through a small set of central functions (`AddItem`, `RemoveItem`, `UseItem`) rather than having UI code directly manipulate inventory data — this keeps validation (e.g. "is the inventory full?") in one place.

---

## 28.4 UI Considerations

The inventory system's data and its visual UI (Lesson 33) should be kept as separate as possible:

- The UI **reads** inventory data to display it, and **calls** inventory functions in response to player actions (drag-and-drop, clicking "use").
- The underlying inventory data should work correctly even with no UI at all (useful for testing, and for systems like crafting that operate on inventory data programmatically).

This separation — keeping gameplay data independent from its presentation — is a pattern worth applying broadly, and comes up again in Lesson 33.

[Previous](./[27]-Game-State-Management.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[29]-Dialogue-and-Quest-Systems.md)
