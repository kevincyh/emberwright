# Testing

## Test layers

| Layer | Tool | Location | Runs | Speed |
|---|---|---|---|---|
| Core unit tests | NUnit 3 + `dotnet test` | `Tests/Emberwright.Core.Tests/` | Every change, every PR | Seconds |
| Unity EditMode | Unity Test Framework | `Assets/Tests/EditMode/` | Every PR (CI) | About a minute |
| Unity PlayMode, including netcode | Unity Test Framework + NGO `NetcodeIntegrationTest` | `Assets/Tests/PlayMode/` | Every PR (CI) | Minutes |
| Performance | `com.unity.test-framework.performance` | `Assets/Tests/PlayMode/Performance/` | Nightly | Minutes |
| End to end on a device | AltTester Unity SDK | `Tests/E2E/` | Nightly, before release | Long |
| Manual multiplayer | Multiplayer Play Mode | Editor | When needed | Not applicable |

Most tests are Core tests. Keep game rules in Core so they can be tested there.

## Run locally

```sh
# Core: the main loop for agents
dotnet test Tests/Emberwright.Core.sln

# Core with coverage
dotnet test Tests/Emberwright.Core.sln --collect:"XPlat Code Coverage"

# Unity EditMode and PlayMode (headless)
/Applications/Unity/Hub/Editor/<version>/Unity.app/Contents/MacOS/Unity \
  -batchmode -projectPath . -runTests -testPlatform EditMode -testResults TestResults/editmode.xml
```

## Rules for writing tests

1. **Test behaviour, not implementation.** Assert what a caller can see: return values, state that can be read back, and events raised. A test must fail if the behaviour breaks and keep passing if only the internals change.
2. **Derive expected values from the requirement,** not by calling the code under test.
3. **No hidden randomness or time.** Pass a seeded `IRandom` and explicit time values.
4. **One behaviour per test.** Name tests `Method_Condition_Result`, for example `Upgrade_AtMaxLevel_ReturnsFailure`.
5. **Write the failing test first** for every bug fix and every acceptance criterion.
6. **Use NUnit 3 syntax** (`Assert.That(x, Is.EqualTo(y))`) so tests can move into Unity unchanged.

## Netcode tests

Use `NetcodeIntegrationTest` from `Unity.Netcode.TestHelpers.Runtime`. Spin up one server and N clients in a PlayMode test. Every authority rule needs a test that tries the cheat from a client and asserts that the server ignores it.

## Coverage targets

| Area | Target |
|---|---|
| `Emberwright.Core` | ≥ 80% lines |
| Net, Client, Server | Covered by integration and E2E tests; no number |

## End-to-end tests (AltTester)

- The AltTester Unity SDK is added to the Unity project in a development build only.
- Tests in `Tests/E2E/` find GameObjects by name or path, tap them, and assert UI text and state.
- In CI, `e2e.yml` runs them on an Android emulator. Firebase Test Lab or AWS Device Farm can run them on real devices later.
