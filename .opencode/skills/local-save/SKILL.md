---
name: Local save
description: Emberwright's on-device save system - versioned JSON model and migrations in Core, atomic file writes in Host, checksum and backup, rewards applied once by rewardId. Load for any save, load, migration or reward ticket.
---
# Local save

Each device stores its own progress. There is no server. Model: docs/ARCHITECTURE.md#saves.

## Where things live

| Part | Assembly | Tested with |
|---|---|---|
| `SaveData` model, `SaveMigrator`, checksum, `RewardLedger` | `Emberwright.Core` (`Core/Save/`) | `dotnet test` |
| File read and write (`SaveFileStore`) | `Emberwright.Host` | EditMode test with a temp folder |

## File format

```json
{ "version": 2, "checksum": "<sha256 of data>", "data": { "profile": {...}, "inventory": [...], "appliedRewards": ["..."] } }
```

- `version` goes up whenever the shape of `data` changes.
- `SaveMigrator` has one pure step per version: `V1ToV2(JObject-like model) → model`. It never skips a step. Test every step with a fixture of the old shape.
- Core does not do I/O. It turns `SaveData` into a string and back.

## Writing safely (atomic)

1. Serialise and compute the checksum.
2. Write to `save_<slot>.json.tmp`, then flush it.
3. Copy the current `save_<slot>.json` to `save_<slot>.json.bak` if it exists.
4. Move the `.tmp` file over `save_<slot>.json` (`File.Replace`, or delete then move).

A crash at any step leaves either the old save or the new one, never half of one.

## Loading

1. Read `save_<slot>.json`. If the checksum is OK, migrate it to the current version.
2. If the checksum is bad or the file is unreadable, try `.bak`. Tell the player that the backup was used.
3. If both are bad, start a new profile only after asking the player. Never overwrite silently.

## Rewards are applied once

```csharp
// Core
public bool TryApply(Reward reward)   // false if reward.Id is already in AppliedRewards
```

- `AppliedRewards` keeps the last N IDs (for example 512). IDs include the session ID, so old ones can be dropped safely.
- Save soon after applying rewards (for example at the end of a fight or on a scene change), not every frame.

## Must-have tests

- `Roundtrip_SaveThenLoad_ReturnsEqualData`
- `Migrate_V1Fixture_ProducesCurrentVersion`
- `Load_BadChecksum_UsesBackup`
- `TryApply_SameRewardTwice_SecondReturnsFalseAndNoChange`

## Do not

- Do not use `PlayerPrefs` for progress.
- Do not save from clients for the host's world. Each device saves only its own player.
- Do not put Unity types (`Vector3`) in `SaveData`. Use Core types.
