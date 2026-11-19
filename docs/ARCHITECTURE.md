# Architecture

This page explains how Emberwright is put together and why. Each decision has an ADR in [adr/](adr/). The current model is [ADR 0006](adr/0006-offline-first-local-coop.md).

## Goals

1. **Offline first.** Single-player works with no internet and no account.
2. **Co-op by connecting devices.** 2–4 phones play together on the same Wi-Fi or on the host's hotspot. Android and iOS can play together.
3. **One code path.** Solo play is a host session with zero clients. Co-op adds clients to the same session.
4. **Fast feedback for a small local model.** Game rules live in plain C# that tests in seconds without Unity.
5. **Data-driven content.** A new sword, enemy, zone or quest is a data asset, not new code.
6. **Mobile first.** It runs at 60 fps on mid-range phones.

## Tech stack

| Concern | Choice |
|---|---|
| Engine | Unity 6 LTS, URP (mobile renderer), 3D low-poly |
| Input | Unity Input System, with an on-screen joystick and buttons |
| Networking | Netcode for GameObjects (NGO), **host-authoritative**. One phone is the host, and it runs the simulation. |
| Transport | Unity Transport (UDP) over the local network |
| Finding a host | LAN discovery by UDP broadcast, a QR code on the host's screen, or manual IP:port entry. iOS broadcast needs Apple's multicast entitlement; QR and manual IP work without it. |
| No Wi-Fi | The host turns on a mobile hotspot; the other players join it |
| Persistence | Local save file on each device. It is versioned JSON, written atomically, with a checksum. |
| Tuning | ScriptableObjects in `Assets/_Game/Data/` |
| Purchases (if any) | Unity IAP with store receipt validation (open question in [GAME_DESIGN.md](GAME_DESIGN.md)) |
| CI/CD | GitHub Actions with GameCI; fastlane for store upload |
| Later, optional | Unity Relay join codes for remote co-op; UGS Authentication with Cloud Save as an opt-in save backup |

There are no game servers and no backend to run.

## Play modes

| Mode | How it works |
|---|---|
| Single-player | `NetworkManager.StartHost()` with no other clients. Same code as co-op. |
| Local co-op (2–4) | The host taps **Host**, which starts the host and broadcasts its presence. Other players tap **Join**, see nearby hosts and connect. If discovery fails (some routers block broadcast, and iOS needs Apple's multicast entitlement), they scan the QR code or type the IP address shown on the host's screen. |
| Remote co-op (later) | The host gets a Unity Relay join code and shares it. Only the transport changes. |

## Assemblies (layers)

Dependencies point downward only. An assembly never references a layer above it.

```
Emberwright.Client   (views, UI, input, VFX)              ─┐
Emberwright.Host     (session start, discovery, spawning,  ─┤
                      save hooks; runs on the host only)    │
                                                            ▼
Emberwright.Net      (NetworkBehaviours, RPCs, NetworkVariables)
                                                            ▼
Emberwright.Data     (ScriptableObject definitions → Core models)
                                                            ▼
Emberwright.Core     (pure C#: rules, formulas, state machines, save format)
```

| Assembly | Path | Rules |
|---|---|---|
| `Emberwright.Core` | `Assets/_Game/Scripts/Core` | `noEngineReferences: true`. It has no `UnityEngine` references, no statics with mutable state, and no I/O. It holds the save model and migrations. Everything is unit-tested with `dotnet test`. |
| `Emberwright.Data` | `Assets/_Game/Scripts/Data` | ScriptableObjects that hold content. Each one converts to an immutable Core model. |
| `Emberwright.Net` | `Assets/_Game/Scripts/Net` | Thin `NetworkBehaviour`s that call Core on the host and replicate the results. |
| `Emberwright.Host` | `Assets/_Game/Scripts/Host` | Starting and stopping sessions, LAN discovery, spawning, and the save file I/O. |
| `Emberwright.Client` | `Assets/_Game/Scripts/Client` | Presentation only. It reads replicated state and sends input. |

`Tests/Emberwright.Core` compiles the same Core source files outside Unity (netstandard2.1, C# 9), so Core tests run in seconds.

## Host authority

```
Client phone                      Host phone (also a player)
  │ input RPC (move, attack)         │
  ├─────────────────────────────────▶│ validate (cooldown, range, state)
  │                                  │ Core.Combat.Resolve(...)
  │                                  │ apply result to NetworkVariables
  │◀── replicated state ─────────────┤
  │◀── reward RPC (rewardId, items) ─┤ host decides drops and XP
  │ apply to OWN save if rewardId    │ host applies to its own save the same way
  │ not seen before                  │
```

- Clients send intents such as "attack target 12". They never send results such as "I dealt 50 damage".
- The host decides what happens in the shared world: damage, enemy state, drops and XP.
- **Each player keeps their own progress on their own device.** After a co-op session, everyone keeps the XP and loot that the host gave them.
- Each reward has a `rewardId`. A device applies a reward only once, even if the message arrives twice.
- Friends play co-op together, so the design prevents accidents and casual cheating, not determined hackers. A modified client can edit its own save; that only affects that player.

## Saves

- One file per profile: `Application.persistentDataPath/save_<slot>.json`.
- `{ "version": N, "data": {...}, "checksum": "..." }`. Core owns the model and the step-by-step migrations (`v1 → v2 → …`).
- Writes go to `save_<slot>.json.tmp`, then the file is renamed into place, so a crash never leaves a half-written save.
- A checksum mismatch means the file was edited or corrupted. Load the `.bak` copy and warn the player.
- See the `local-save` skill.

## Content pipeline

1. Designers create ScriptableObjects in `Assets/_Game/Data/` (swords, enemies, bosses, zones, quests, drop tables).
2. `Emberwright.Data` converts them to Core models at load time.

## Game systems (in Core)

| System | Core responsibility |
|---|---|
| Combat | Combo timing, hit resolution, damage formula, status effects |
| Progression | XP curve, levels, stat points and derived stats |
| Items | Sword definitions, rarity, upgrade levels and costs, inventory rules |
| World | Zone unlock rules, enemy spawn tables, boss phase state machines |
| Quests | Quest state machine and objectives (kill, collect, talk) |
| Economy | Prices, rewards, and reward IDs that are applied once |
| Save | Save model, versioning, migrations, checksum |

## Scenes

`Boot` → `Menu` (Play solo / Host / Join) → `Hub` (town, NPCs, shop) → `Zone_NN` → `BossArena_NN`.

In co-op, the host's scene choice drives everyone, and NGO scene management loads the same scene on every client.

## Performance budget

| Item | Budget |
|---|---|
| Frame time | 16.6 ms on the reference mid-range Android phone, including when it is hosting |
| Draw calls | Under 100 in a zone |
| GC allocations per frame in gameplay | 0 B (use pooling) |
| Network tick | 30 Hz |
| Players per session | 1–4 |

## Testing

See [TESTING.md](TESTING.md). Netcode tests run one host plus N clients in a single PlayMode test.
