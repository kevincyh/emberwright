# ADR 0003: Unity Gaming Services as the backend

- Status: superseded by [0006](0006-offline-first-local-coop.md) on 2026-11-20
- Date: 2026-11-19

## Context
The game needs accounts, persistent inventories, currencies, matchmaking and live tuning, without running a custom backend.

## Decision
Use UGS for all of these:
- Authentication
- Cloud Save
- Cloud Code (trusted, idempotent grants and purchases)
- Economy
- Lobby and Matchmaker
- Remote Config

## Consequences
- Vendor lock-in to Unity. Core logic stays engine-free so it can move.
- Cloud Code (JavaScript or C# modules) mirrors the Core rules through JSON exported from the ScriptableObjects. This keeps one source of truth.
