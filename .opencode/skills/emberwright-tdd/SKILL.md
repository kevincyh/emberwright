---
name: Emberwright TDD
description: Test-first loop for every Emberwright ticket. Red, then green, then all tests. Small verifiable steps. Use at the start of any coding ticket.
---
# Emberwright TDD

Adapted from pstack `tdd`, `test-behavior-not-implementation`, `prove-it-works` and `sequence-verifiable-units` (MIT, Lauren Tan).

## Loop (one acceptance criterion at a time)

1. **Red.** Write one test for one acceptance criterion. Run `dotnet test Tests/Emberwright.Core.sln`. It must fail for the right reason: a wrong value, or a missing type. Paste the failure.
2. **Green.** Write the smallest production change that makes it pass. Run the tests again.
3. **All green.** Every test passes, not only the new one.
4. Commit: `feat(<area>): <criterion>`. Move to the next criterion.

Do not start the next unit while anything is red.

## A good test

- It drives the code through its public method with concrete input.
- The expected value comes from the ticket text, never from calling the code under test.
- It fails if the behaviour breaks, and still passes if only the internals change.
- It has no hidden randomness or time. Pass `new SeededRandom(42)` and explicit time values.
- For "this never happens", add a case where it does happen, and assert the result.

## A bad test (do not write)

- It asserts on private helpers or on call order.
- It asserts something the test built itself.
- It has loose assertions such as `Is.Not.Null` when an exact value is known.
- It changes the test to match wrong code. Never do this.

## Proof

"It compiles" is not proof. Paste the real test output: `Passed!  - Failed: 0, Passed: N`.

## Example

```csharp
[Test]
public void UpgradeCost_AtLevel3_IsBaseTimesGrowthCubed()
{
    var curve = new UpgradeCostCurve(baseCost: 100, growth: 1.5);
    Assert.That(curve.CostFor(level: 3), Is.EqualTo(337)); // 100 * 1.5^3 = 337.5, floored
}
```
