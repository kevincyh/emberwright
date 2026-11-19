# Game design: Emberwright

> **Owner: you.** This is the source for every user story. The `/stories` command reads this file. Fill in each `TODO`, then run `/stories`.
>
> Write mechanics in your own words. Do not copy names, items, bosses, maps, lore or art from other games.

## Pitch

TODO: Write one or two sentences about the fantasy. Example: "You are an emberwright, a smith-warrior who forges living blades from the embers of a dying world."

## Pillars

1. TODO: for example, "Every sword feels different."
2. TODO: for example, "Short sessions on a phone, deep goals over weeks."
3. TODO: for example, "Bosses are better with friends."

## Core loop

```
Enter zone → fight enemies → earn XP, gold and materials → find or forge swords
     ▲                                                           │
     └──── stronger build ◀── upgrade swords, spend stat points ◀┘
                     │
                     └──▶ unlock the next zone and boss
```

## Systems

### Sword collection and upgrades
- Rarity tiers: TODO (for example Common, Rare, Epic, Legendary)
- Each sword has: base damage, attack speed, combo pattern, one passive, element (TODO)
- Upgrade path: TODO (for example levels +1 to +10, cost grows per level, a chance to fail at high levels: yes or no?)
- How players get swords: drops, crafting, shop, quests (TODO: choose)

### Leveling and stats
- Level cap at launch: TODO
- Stats: TODO (for example Strength, Agility, Vitality, Focus), plus what each one changes
- Points per level: TODO
- Respec: TODO

### Combat
- Controls: virtual joystick, attack button, dodge, one or two skills (TODO)
- Combos: TODO
- Death and respawn rules: TODO

### Zones and bosses
| # | Zone (your name) | Level range | Theme | Boss (your name) | Boss mechanic |
|---|---|---|---|---|---|
| 1 | TODO | 1–10 | TODO | TODO | TODO |
| 2 | TODO | 10–20 | TODO | TODO | TODO |
| 3 | TODO | 20–30 | TODO | TODO | TODO |

### Quests and NPCs
- Quest types: kill, collect, talk, escort (TODO: choose)
- Hub NPCs: TODO (for example smith, quest giver, merchant)
- Daily or repeatable quests: TODO

### Multiplayer (local co-op; see docs/adr/0006-offline-first-local-coop.md)
- The game is single-player first. Friends can join by connecting devices: same Wi-Fi, or the host's hotspot.
- Players per session: 2–4 (TODO: confirm the maximum)
- Whose world: the host's world and story progress. Each player keeps their own XP and loot on their own device. (TODO: confirm)
- Drop-in and drop-out in the middle of a zone: TODO (yes, or only in the hub?)
- Do enemies scale with the player count? TODO
- How is loot shared? TODO (everyone gets their own drops, or the first to pick it up?)
- Trading between players: TODO. This is risky without a server, so the recommendation is no.
- PvP: TODO
- Remote co-op with a join code over the internet: after launch, optional

### Economy
- Currencies: TODO (for example gold, which is earned in the game)
- Sinks: upgrades, repairs, shop (TODO)
- Monetization: TODO. Without a server, a premium currency can't be protected. Options: a paid game, a one-off "full game" unlock, cosmetic purchases with store receipt validation, or none.

## Out of scope for launch

- TODO

## Open questions

- TODO
