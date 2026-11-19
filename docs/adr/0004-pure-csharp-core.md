# ADR 0004: Game rules in a Unity-free Core assembly

- Status: accepted
- Date: 2026-11-19

## Context
Most code is written by a small local model (Gemma 4). It needs fast and reliable feedback. Unity tests take minutes, while plain .NET tests take seconds.

## Decision
- `Emberwright.Core` has `noEngineReferences: true`.
- `Tests/Emberwright.Core/Emberwright.Core.csproj` compiles the same source files for netstandard2.1 with C# 9, and NUnit 3 tests run with `dotnet test`.
- Randomness and time are injected.

## Consequences
- Most bugs are caught in seconds.
- Behaviours must stay thin. Logic in Behaviours is a review finding.
- Core cannot use Unity math types. Use `System.Numerics` or small Core structs.
