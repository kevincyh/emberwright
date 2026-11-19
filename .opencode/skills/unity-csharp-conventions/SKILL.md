---
name: Unity C# conventions
description: How to write C# in Emberwright - assemblies, naming, where logic goes, domain modelling. Load before writing any C#.
---
# Unity C# conventions

Full rules: docs/CODING_STANDARDS.md. This is the short version.

## Assemblies (lowest to highest; reference downward only)

| Assembly | Path | May use |
|---|---|---|
| Emberwright.Core | Assets/_Game/Scripts/Core | `System.*` only. No `UnityEngine`. |
| Emberwright.Data | Assets/_Game/Scripts/Data | Core, `UnityEngine` (ScriptableObject) |
| Emberwright.Net | Assets/_Game/Scripts/Net | Core, Data, `Unity.Netcode` |
| Emberwright.Server | Assets/_Game/Scripts/Server | Core, Data, Net |
| Emberwright.Client | Assets/_Game/Scripts/Client | Core, Data, Net, Input System |

## Language

- C# 9 and netstandard2.1. Allowed: records, `init`, switch expressions and pattern matching. Not allowed: file-scoped namespaces, `required` and raw string literals.
- Block-scoped namespaces: `namespace Emberwright.Core.Items { ... }`.
- `_camelCase` private fields, PascalCase for public members, one public type per file.

## Model the domain (pstack: model-the-domain, foundational-thinking, type-system-discipline)

- Define the data shape first: types, the owner of each piece of state, and access patterns.
- Use a state machine (an enum plus a transition table) instead of booleans that must stay in sync.
- Use a table or dictionary keyed by ID instead of a growing `switch` over item kinds.
- Give IDs their own types when two ints mean different things: `readonly struct SwordId { public readonly int Value; ... }`.
- Parse raw data (JSON, ScriptableObject fields) into Core types once, at the boundary. Inside Core, trust the types and do not re-validate.

## Thin Behaviours

```csharp
// Net layer: no game rules here, only call Core and apply the result.
[Rpc(SendTo.Server)]
void AttackRpc(ulong targetId, RpcParams p = default)
{
    var result = _combat.Resolve(_attacker.State, _targets.Get(targetId), _clock.Tick, _random);
    _targets.Apply(targetId, result);
}
```

## Unity performance

- Do not allocate in `Update`. Cache components in `Awake`, and use pooling.
- No `FindObjectOfType` or `GameObject.Find` at runtime.
