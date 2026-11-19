---
name: Ponytail review
description: Quality review of a change - correct, safe, holds under load, tested, fast, lean. Lists findings in plain English and changes no code. Use at the review step only (/ponytail-review), never while building.
---
# Ponytail review

Ported from Ponytail's `ponytail-review` skill (MIT, Dietrich Gebert, v5.3.3). Changes for Emberwright: a precedence rule, the expected load, and a C# example.

**Precedence:** tests, host authority and the docs/ARCHITECTURE.md rules always win over "lean". Never propose deleting a test, a host-side validation or a `rewardId` check to save lines.

**Expected load:** local co-op. 1–3 clients send RPCs to a host phone at 30 ticks per second, and the host is also rendering the game, so its CPU and allocations matter. Treat clients as untrusted.

Review like the senior developer who will be paged when it breaks. Order of importance: correct, safe, holds under load, tested, fast, lean.

## 1. Understand first

- Review what was named. If nothing was named, review `git diff main...HEAD`, or the last commit if there is no diff.
- Read the diff, then the code it touches: the callers of every changed method, the methods it calls, and the tests.
- When a signature or behaviour changes, grep every caller.

## 2. Look for (in this order)

1. **Bug:** wrong result, crash, missed edge case (empty, zero, last item, rounding), a caller broken by the change.
2. **Risk:** client-trusted values, missing RPC validation, a reward without a `rewardId`, a save write that is not atomic, swallowed errors.
3. **Scale:** a check-then-write race, per-tick allocations, lists that only grow, O(n²) over all players or entities.
4. **Missing test:** risky new logic (a branch, money, loot, authority, a bug fix) with no test that fails when it breaks.
5. **Speed:** big slowdowns are problems. Small wins in hot paths (`Update`, the tick loop) are suggestions.
6. **Lean:** dead code, a helper the repo already has (name the path), a hand-rolled version of something the BCL or Unity already does, an abstraction with one implementation, near-copies to merge, or one method doing unrelated jobs.

## 3. Check before you report

- Every finding needs a concrete case: "this input leads to this wrong result". No case, no finding.
- Re-read the lines to confirm. The caller must exist. The value must really be able to be empty.
- A `// shortcut:` comment that names its limit is a decision, not a finding, unless the expected load crosses that limit.
- Propose the smallest fix. Prefer fixes that delete code. Never add layers.
- No style taste, no "consider", no vague worries.

## 4. Output

Use simple English. Start with `What this change does:` in 2 or 3 sentences.

Then number the findings in up to three groups, skipping empty groups:
- **Must fix:** a bug, a security or authority hole, or something that breaks at the expected load.
- **Should fix:** risky code without a test, real slowness, duplication or a method that mixes jobs.
- **Nice to have:** small speed-ups.

Each finding is a bold title with `file:lines`, then four bullets, each 1 or 2 sentences: **What this is**, **Problem**, **Fix**, **If we skip it**.

1. **Damage trusted from client** (`Assets/_Game/Scripts/Net/PlayerCombat.cs:L22-30`)
   - **What this is:** The attack RPC applies the damage value that the client sends.
   - **Problem:** A modified client can send 99999 and kill a boss in one hit.
   - **Fix:** Send only `targetId`, and compute the damage on the host with `CombatRules.Resolve`.
   - **If we skip it:** Anyone can cheat, and loot loses its value.

End with:
- `Verdict: Ship.` or `Verdict: fix 1 and 3 first.`
- `Lean: -<N> lines possible.` when lean findings exist.
- `Not checked:` one line, if something mattered and you could not check it.

If nothing is found: `What this change does:`, then `Looks good. Ship.`, then one line on what you checked.

This skill lists findings. It never changes code.
