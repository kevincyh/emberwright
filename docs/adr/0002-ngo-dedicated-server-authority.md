# ADR 0002: Netcode for GameObjects with a server-authoritative dedicated server

- Status: superseded by [0006](0006-offline-first-local-coop.md) on 2026-11-20
- Date: 2026-11-19

## Context
Players share zones and fight bosses together. Loot and progression have value, so clients must not be able to cheat.

## Decision
- Use Netcode for GameObjects.
- The server is a headless Linux build of the same project.
- Clients send intent RPCs. The server validates them, resolves the outcome with Core and replicates the result.
- `NetworkVariable` writes are server-only.

## Consequences
- Server hosting costs money (Edgegap or similar). Relay is used only in development.
- Combat feels slightly delayed. Movement is owner-predicted to hide this.
- Every authority rule needs an NGO integration test that attempts the cheat.
