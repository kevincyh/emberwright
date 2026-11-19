# Emberwright: rules for every agent

Emberwright is an original sword action RPG for Android and iOS, built with Unity 6. It is single-player first, with local co-op where one phone hosts and others join over Wi-Fi or a hotspot (NGO, host-authoritative).

## Your job

1. Work only on the GitHub issue or task file you were given. If you have neither, run `/next-issue`.
2. Read the issue's "Files in scope" and "Skills and lessons to load". Load those skills before you write code.
3. Follow the `emberwright-tdd` skill: failing test → smallest fix → all tests green.
4. Run `/ponytail-review`, then `/verify`. Then open a PR that says `Closes #<n>`.
5. Never merge a PR. Never push to `main`. Never force-push.

## Hard rules

1. Game rules live in `Assets/_Game/Scripts/Core/` (`Emberwright.Core`). Core code never uses `UnityEngine`.
2. Behaviours (`MonoBehaviour`, `NetworkBehaviour`) only read input, call Core and apply the result.
3. Clients send intent only, with `[Rpc(SendTo.Server)]` (in NGO, the host is the server). The host computes damage, loot and XP. Each device applies a reward to its own save once, by `rewardId`.
4. Inject randomness (`IRandom`) and time into Core. Never call `UnityEngine.Random`, `Time.time` or `DateTime.Now` in Core.
5. Use C# 9 and netstandard2.1 APIs only.
6. Tests use NUnit 3 syntax: `Assert.That(actual, Is.EqualTo(expected))`.
7. Do not edit files outside the ticket's scope. If you must, say why in the PR.
8. Do not read or print secrets: `.env`, `*.keystore`, `*.p8`, `*.p12`.

## Simplicity rules (from pstack and Ponytail)

1. Make the smallest change that meets the acceptance criteria.
2. Remove before you add. Delete dead code in the same change.
3. Do not add an interface with one implementation, a factory with one product, or unused options.
4. Search for an existing helper before you write one (`grep -r "<Name>" Assets Tests`).
5. Fix the root cause, not the place where the error shows. Do not hide bugs with try/catch, null checks or clamps.
6. Readers should find where a value comes from in under 30 seconds. Prefer return values over mutation, and locals over fields.

## Commands

| Goal | Command |
|---|---|
| Core tests (fast, run after every change) | `dotnet test Tests/Emberwright.Core.sln` |
| Format check | `dotnet format Tests/Emberwright.Core.sln --verify-no-changes` |
| Read an issue | `gh issue view <n> --json title,body,labels` |
| Comment on an issue | `gh issue comment <n> --body "<text>"` |

## When you are stuck (hard triggers, do not skip)

1. **Same test red after 2 fix attempts:** stop. Write the one sentence that both attempts assumed. Check whether it is true. If the test is still red, run `/escalate`.
2. **The ticket says `difficulty: high`:** load `escalation-policy` before you start.
3. **Never** call `delegator` or `delegator-pro` (Claude) on your own. The human must approve it.
4. After a tutor helps, read the lesson it wrote before you continue.

## Skills (load on demand)

| Skill | Load when |
|---|---|
| `emberwright-tdd` | Every ticket |
| `unity-csharp-conventions` | Writing any C# |
| `core-dotnet-tests` | Writing Core tests |
| `ngo-host-authority` | Touching `Net/`, `Host/` or anything multiplayer |
| `local-save` | Save format, loading, migrations, rewards |
| `unity-test-framework` | Writing EditMode or PlayMode tests |
| `debug-playbook` | A bug or an unexpected test failure |
| `escalation-policy` | Before `/escalate`, or on `difficulty: high` |
| `project-lessons` | Start of a ticket: find lessons for your area |
| `issue-workflow` | Picking or updating GitHub issues |
| `emberwright-verify` | Before a PR |
| `ponytail-review` | Review step only |
| `docs-writing` | Writing docs, PR text or commits |
| `gameci-workflows` | Editing `.github/workflows/` |
| `impressions` | Art reference and image generation tickets |

## Map

| Path | What |
|---|---|
| `Assets/_Game/Scripts/{Core,Data,Net,Host,Client}` | Assemblies, from lowest to highest layer |
| `Tests/Emberwright.Core.Tests` | Fast NUnit tests for Core |
| `Assets/Tests/{EditMode,PlayMode}` | Unity tests |
| `docs/ARCHITECTURE.md` | How it fits together |
| `docs/ai/lessons/` | Mistakes to avoid |
