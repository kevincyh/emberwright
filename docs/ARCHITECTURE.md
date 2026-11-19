# Architecture

This page explains how Emberwright is put together and why. Each decision has an ADR in [adr/](adr/).

## Goals

1. **Fair online play.** The server decides every outcome: damage, loot, XP and currency. Clients send input only.
2. **Fast feedback for a small local model.** Game rules live in plain C# that tests in seconds without Unity.
3. **Data-driven content.** A new sword, enemy, zone or quest is a data asset, not new code.
4. **Mobile first.** It runs at 60 fps on mid-range phones.

## Tech stack

| Concern | Choice |
|---|---|
| Engine | Unity 6 LTS, URP (mobile renderer), 3D low-poly |
| Input | Unity Input System, with an on-screen joystick and buttons |
| Networking | Netcode for GameObjects (NGO), server-authoritative dedicated server |
| Transport | Unity Transport; Relay in development only |
| Server hosting | Headless Linux server build in a container, on Edgegap or similar |
| Accounts | UGS Authentication: anonymous login, plus Sign in with Apple and Google |
| Persistence | UGS Cloud Save (profile, inventory) |
| Trusted logic outside a match | UGS Cloud Code (upgrades, purchases, quest rewards) |
| Currencies and items | UGS Economy |
| Matchmaking | UGS Lobby and Matchmaker (zone and boss instances) |
| Live tuning | UGS Remote Config |
| CI/CD | GitHub Actions with GameCI; fastlane for store upload |

## Assemblies (layers)

Dependencies point downward only. An assembly never references a layer above it.

```
Emberwright.Client   (views, UI, input, VFX)      ─┐
Emberwright.Server   (server bootstrap, spawners) ─┤
                                                   ▼
Emberwright.Net      (NetworkBehaviours, RPCs, NetworkVariables)
                                                   ▼
Emberwright.Data     (ScriptableObject definitions → Core models)
                                                   ▼
Emberwright.Core     (pure C#: rules, formulas, state machines)
```

| Assembly | Path | Rules |
|---|---|---|
| `Emberwright.Core` | `Assets/_Game/Scripts/Core` | `noEngineReferences: true`. It has no `UnityEngine` references, no statics with mutable state, and no I/O. Everything is unit-tested with `dotnet test`. |
| `Emberwright.Data` | `Assets/_Game/Scripts/Data` | ScriptableObjects that hold content. Each one converts to an immutable Core model. |
| `Emberwright.Net` | `Assets/_Game/Scripts/Net` | Thin `NetworkBehaviour`s that call Core on the server and replicate the results. |
| `Emberwright.Server` | `Assets/_Game/Scripts/Server` | Server-only startup, spawning, instance lifecycle and UGS server calls. |
| `Emberwright.Client` | `Assets/_Game/Scripts/Client` | Presentation only. It reads replicated state and sends input. |

`Tests/Emberwright.Core` compiles the same Core source files outside Unity (netstandard2.1, C# 9), so Core tests run in seconds.

## Server authority

```
Client                         Server (dedicated)                    UGS
  │ input RPC (move, attack)      │                                    │
  ├──────────────────────────────▶│ validate (cooldown, range, state)  │
  │                               │ Core.Combat.Resolve(...)           │
  │                               │ apply result to NetworkVariables   │
  │◀── replicated state ──────────┤                                    │
  │                               │ loot / XP granted ────────────────▶│ Cloud Code (idempotent)
  │◀── inventory update ──────────┤◀─────────────── Economy / Cloud Save
```

- Clients never send results such as "I dealt 50 damage". They send intents such as "attack target 12".
- Every value that changes the economy is written by the server or by Cloud Code, never by the client.
- Each Cloud Code operation takes a request ID, so a retry cannot grant the same reward twice.
- Movement uses owner-predicted motion with server correction. Combat outcomes are server-only.

## Content pipeline

1. Designers create ScriptableObjects in `Assets/_Game/Data/` (swords, enemies, bosses, zones, quests, drop tables).
2. `Emberwright.Data` converts them to Core models at load time.
3. An editor export writes the same definitions to JSON for Cloud Code. Upgrade costs and drop tables have one source of truth.

## Game systems (in Core)

| System | Core responsibility |
|---|---|
| Combat | Combo timing, hit resolution, damage formula, status effects |
| Progression | XP curve, levels, stat points and derived stats |
| Items | Sword definitions, rarity, upgrade levels and costs, inventory rules |
| World | Zone unlock rules, enemy spawn tables, boss phase state machines |
| Quests | Quest state machine and objectives (kill, collect, talk) |
| Economy | Prices and reward calculation. The persisted values live in UGS. |

## Scenes

`Boot` → `Hub` (town, NPCs, shop) → `Zone_NN` (shared zone instance) → `BossArena_NN` (party instance).

## Performance budget

| Item | Budget |
|---|---|
| Frame time | 16.6 ms on the reference mid-range Android phone |
| Draw calls | Under 100 in a zone |
| GC allocations per frame in gameplay | 0 B (use pooling) |
| Server tick | 30 Hz |
| Players per zone instance | 2 to 10 |

## Testing

See [TESTING.md](TESTING.md).
