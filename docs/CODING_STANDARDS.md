# Coding standards

These rules apply to every contributor, human or agent. Where a tool can enforce a rule, the tool is named.

## C# and Unity

| Rule | Enforced by |
|---|---|
| C# 9 and netstandard2.1 APIs only. Unity 6 compiles this level. | `Tests/Emberwright.Core/Emberwright.Core.csproj` (`LangVersion 9.0`) |
| `Emberwright.Core` has no `UnityEngine` usage. | `noEngineReferences: true` in the asmdef, plus the dotnet build |
| Namespaces match the assembly: `Emberwright.Core.Combat`, `Emberwright.Net.Combat`, and so on. | Review |
| PascalCase for types and methods. `_camelCase` for private fields. camelCase for locals. | `.editorconfig`, `dotnet format` |
| One public type per file, and the file name matches the type. | Review |
| Warnings are errors in Core. | `TreatWarningsAsErrors` |

## Where logic goes

- **Rules and math go in Core** as pure functions or small immutable types. Examples: damage, XP, upgrade cost, drop roll, quest transitions.
- **MonoBehaviours and NetworkBehaviours stay thin.** They read input, call Core and apply the result. If a method in `Net` or `Client` has an `if` about game rules, move it to Core.
- **Randomness is injected.** Core takes an `IRandom` (or a seed), never `UnityEngine.Random` or `System.Random` created inside. This keeps tests deterministic.
- **Time is injected.** Core takes elapsed time or a tick number as a parameter, never `Time.time`.
- **Model the domain.** Use a state machine instead of several booleans that must stay in sync. Use a table or registry instead of a growing `if`/`switch` over item types.

## Networking

- The host is authoritative for the shared world. In NGO, the host *is* the server, so the attribute is still `[Rpc(SendTo.Server)]`.
- Clients send intent RPCs only. The host validates and computes.
- `NetworkVariable`s are written only by the host, which is the default write permission. Never change that default for gameplay values.
- Never trust a client value for damage, position jumps or cooldowns.
- Rewards (XP, loot, currency) carry a `rewardId`. Each device applies a reward to its own save once, so a repeated message cannot grant twice.
- Save file I/O lives in `Emberwright.Host`. The save model and migrations live in Core.

## Simplicity

- Make the smallest change that meets the acceptance criteria.
- Do not add an interface with one implementation, a factory with one product, or config that nobody sets.
- Reuse an existing helper before you write a new one. Search first.
- Delete dead code in the same PR that makes it dead.

## Comments

- Comment *why*, not *what*. Do not leave commented-out code.
- A deliberate shortcut gets a `// shortcut: <limit>` comment that names the limit, for example `// shortcut: O(n) scan, fine below 200 items`.

## Commits and branches

- Branch names: `feat/<issue>-<slug>`, `fix/<issue>-<slug>`, `chore/<slug>`.
- Commits follow Conventional Commits: `feat(combat): add 3-hit combo timing`.
- Each commit builds and passes the tests.
