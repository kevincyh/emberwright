---
name: NGO host authority
description: Netcode for GameObjects rules for Emberwright's local co-op - the host phone owns the shared world, clients send intent, rewards apply once per device, LAN discovery and joining. Load when touching Net/, Host/ or multiplayer behaviour.
---
# NGO host authority

Adapted from pstack `boundary-discipline`, `make-operations-idempotent` and `separate-before-serializing-shared-state` (MIT, Lauren Tan). Model: docs/adr/0006-offline-first-local-coop.md.

## The rule

One phone is the **host**: in NGO terms, it is the server and a player at the same time. Solo play is a host with zero clients.

The client says what it **wants**. The host decides what **happens** in the shared world.

| Client may send | Client must never send |
|---|---|
| Move input, attack target ID, skill ID, "talk to NPC 7" | Damage, HP, position jumps, drops, XP, cooldown done |

```csharp
public class PlayerCombat : NetworkBehaviour
{
    // Host-written (the default write permission). Never change it for gameplay values.
    readonly NetworkVariable<int> _health = new NetworkVariable<int>(100);

    [Rpc(SendTo.Server)] // "Server" = the host in Emberwright
    void AttackRpc(ulong targetId, RpcParams p = default)
    {
        if (!_cooldowns.Ready(p.Receive.SenderClientId, _clock.Tick)) return; // validate at the boundary
        var result = _combat.Resolve(...);                                  // Core decides
        _enemies.ApplyDamage(targetId, result.Damage);                      // host applies
    }
}
```

- Validate every RPC argument when it arrives: range, ownership, cooldown and state. Inside Core, trust the types.
- Check `IsServer` (true on the host) before you mutate shared state.
- Spawn, despawn and load scenes only on the host. NGO scene management moves the clients along.

## Rewards: per device, applied once

- The host decides drops and XP, then sends each player `RewardRpc(rewardId, payload)` to that player's client.
- `rewardId` = `$"{sessionId}:{enemyNetId}:{killTick}:{clientId}"`.
- Each device applies the reward to **its own save** only if `rewardId` is not already in the save's applied set. The host does the same for itself. Load the `local-save` skill.
- Ask yourself: what if the message arrives twice? What if the app closes halfway? The answer must be "nothing bad happens".

## Shared state

- Give each player their own state (their own NetworkVariable or their own save). Do not have several writers on one object.
- If one shared value is needed, the host is its single writer.

## Joining (Emberwright.Host)

| Path | How | Notes |
|---|---|---|
| LAN discovery | The host broadcasts a small UDP beacon (game ID, version, host name, port). The join screen lists the hosts it hears. | On iOS, sending or receiving broadcast needs Apple's multicast entitlement (`com.apple.developer.networking.multicast`), which must be requested from Apple. Until it is granted, use the paths below. |
| QR code | The host screen shows a QR code with `ip:port:version`. The joiner scans it. | No entitlement needed, and it works on a hotspot |
| Manual IP | The host screen shows `192.168.x.y:7777`. The joiner types it. | Always-available fallback |

- iOS needs `NSLocalNetworkUsageDescription` in Info.plist (the local network permission prompt).
- Refuse a join when the game version does not match, and show a clear message.
- If the host leaves, the session ends for everyone. Return the clients to the menu, keeping their saves.

## Test it

Every authority rule needs a `NetcodeIntegrationTest` (1 host + 1–3 clients) that attempts the cheat from a client. See the `unity-test-framework` skill.
