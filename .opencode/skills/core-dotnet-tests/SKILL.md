---
name: Core dotnet tests
description: How to add and run fast NUnit 3 tests for Emberwright.Core with dotnet test (no Unity). Load when writing Core code or tests.
---
# Core dotnet tests

- Source: `Assets/_Game/Scripts/Core/**/*.cs`. `Tests/Emberwright.Core/Emberwright.Core.csproj` compiles it outside Unity.
- Tests: `Tests/Emberwright.Core.Tests/<Area>/<Type>Tests.cs`
- Run: `dotnet test Tests/Emberwright.Core.sln`
- One test: `dotnet test Tests/Emberwright.Core.sln --filter "FullyQualifiedName~UpgradeCostCurveTests"`
- Coverage: `dotnet test Tests/Emberwright.Core.sln --collect:"XPlat Code Coverage"`

## File template

```csharp
using NUnit.Framework;
using Emberwright.Core.Items;

namespace Emberwright.Core.Tests.Items
{
    public class UpgradeCostCurveTests
    {
        [Test]
        public void CostFor_Level1_ReturnsBaseCost()
        {
            var curve = new UpgradeCostCurve(baseCost: 100, growth: 1.5);
            Assert.That(curve.CostFor(1), Is.EqualTo(150));
        }

        [TestCase(0)]
        [TestCase(-1)]
        public void CostFor_LevelBelowOne_Throws(int level)
        {
            var curve = new UpgradeCostCurve(100, 1.5);
            Assert.That(() => curve.CostFor(level), Throws.TypeOf<System.ArgumentOutOfRangeException>());
        }
    }
}
```

## Rules

- Use NUnit **3** syntax only (`Assert.That`). Unity ships NUnit 3, so these tests can move into Unity.
- New Core files are picked up automatically by the glob. Do not edit the csproj to add files.
- If you need randomness, use the Core `IRandom` interface with a seeded implementation in the tests.
- `TreatWarningsAsErrors` is on. Fix warnings; do not suppress them.
