---
name: Unity Test Framework
description: Writing EditMode and PlayMode tests in Unity, including NGO multiplayer integration tests. Load for tests that need UnityEngine or Netcode.
---
# Unity Test Framework

Use Unity tests only when Core tests cannot cover the case: MonoBehaviours, scenes, physics and netcode.

| Kind | Folder | asmdef |
|---|---|---|
| EditMode | `Assets/Tests/EditMode/` | `Emberwright.Tests.EditMode` (Editor only) |
| PlayMode | `Assets/Tests/PlayMode/` | `Emberwright.Tests.PlayMode` |

## PlayMode test

```csharp
using System.Collections;
using NUnit.Framework;
using UnityEngine;
using UnityEngine.TestTools;

public class PlayerHealthViewTests
{
    [UnityTest]
    public IEnumerator HealthBar_AfterDamage_ShowsNewValue()
    {
        var go = new GameObject("Player");
        // arrange components, act, then wait one frame
        yield return null;
        Assert.That(/* observed value */ 0, Is.EqualTo(0));
        Object.Destroy(go);
    }
}
```

## Netcode integration test (server authority)

The base class is `NetcodeIntegrationTest` in `Unity.Netcode.TestHelpers.Runtime`. It needs `"testables": ["com.unity.netcode.gameobjects"]` in `Packages/manifest.json`.

```csharp
public class AttackAuthorityTests : NetcodeIntegrationTest
{
    protected override int NumberOfClients => 2;

    [UnityTest]
    public IEnumerator ClientCannotWriteEnemyHealth()
    {
        // spawn an enemy on the host, try to change its health from a client,
        // then assert that the host value is unchanged
        yield return WaitForConditionOrTimeOut(() => /* host observed the RPC */ true);
        Assert.That(/* host-side health */ 100, Is.EqualTo(100));
    }
}
```

Every authority rule needs one test that attempts the cheat from a client.

## Run headless

```sh
<Unity> -batchmode -projectPath . -runTests -testPlatform PlayMode -testResults TestResults/playmode.xml
```

CI runs both modes with GameCI (`.github/workflows/ci.yml`).
