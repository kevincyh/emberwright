---
name: NGO server authority
description: Netcode for GameObjects rules for Emberwright - the server owns state, clients send intent, grants are idempotent. Load when touching Net/, Server/ or multiplayer behaviour.
---
# NGO server authority

Adapted from pstack `boundary-discipline`, `make-operations-idempotent` and `separate-before-serializing-shared-state` (MIT, Lauren Tan).

## The rule

The client says what it **wants**. The server decides what **happens**.

| Client may send | Client must never send |
|---|---|
| Move input, attack target ID, skill ID, "talk to NPC 7" | Damage, HP, position jumps, loot, XP, currency, cooldown done |

## Patterns

```csharp
public class PlayerCombat : NetworkBehaviour
{
    // Server-written by default. Never change the write permission for gameplay values.
    readonly NetworkVariable<int> _health = new NetworkVariable<int>(100);

    [Rpc(SendTo.Server)]
    void AttackRpc(ulong targetId, RpcParams p = default)
    {
        if (!_cooldowns.Ready(p.Receive.SenderClientId, _clock.Tick)) return; // validate at the boundary
        var result = _combat.Resolve(...);                                  // Core decides
        _enemies.ApplyDamage(targetId, result.Damage);                      // server applies
    }
}
```

- Validate every RPC argument on arrival: range, ownership, cooldown and state. That is the boundary. Inside Core, trust the types.
- Check `IsServer` before you mutate shared state. Clients only render.
- Spawn and despawn only on the server (`NetworkObject.Spawn()`).

## Grants are idempotent

Every XP, loot or currency grant takes a request ID (for example `$"{matchId}:{enemyNetId}:{killTick}"`).
- Running the grant twice gives the same result. Cloud Code stores the IDs it has processed.
- Ask yourself: what if this runs twice? What if it crashes halfway? Then design so the answer is "nothing bad happens".

## Shared state

- Give each actor its own state (its own NetworkVariable or its own key). Do not have several writers on one object.
- If one shared value is truly needed, let one writer (the server) own it.

## Test it

Every rule above needs a `NetcodeIntegrationTest` that attempts the cheat from a client (see the `unity-test-framework` skill).
