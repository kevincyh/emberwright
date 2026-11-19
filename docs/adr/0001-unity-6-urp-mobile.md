# ADR 0001: Unity 6 LTS with URP for Android and iOS

- Status: accepted
- Date: 2026-11-19

## Context
Emberwright targets Android and iOS from one codebase, with 3D low-poly art and online multiplayer.

## Decision
Use Unity 6 LTS with the Universal Render Pipeline's mobile renderer and the Input System package.

## Consequences
- One C# codebase for the client and the dedicated server.
- GameCI supports headless builds and tests in GitHub Actions.
- CI needs a Unity license secret.
