# Roadmap

Each phase is a GitHub milestone. A phase is done only when all of its acceptance criteria pass.

| Phase | Milestone | Goal | Acceptance criteria |
|---|---|---|---|
| 0 | `P0 Foundation` | The repo, docs, CI and agent setup | CI is green on `main`. The opencode agents load. The board holds the Phase 1 stories. |
| 1 | `P1 Prototype` | Offline playable slice | Move with the joystick. Do a 3-hit combo. One enemy type with AI. Health and death. One test zone. Core tests ≥80% coverage. |
| 2 | `P2 Networking` | 2–10 players in one zone | Dedicated server build runs headless. Combat is resolved on the server. An NGO integration test proves that a client cannot set damage. |
| 3 | `P3 Progression` | XP, stats, swords, persistence | Level up, spend stat points, get swords as drops, upgrade a sword. Data survives a reinstall through Cloud Save. Rewards are granted idempotently through Cloud Code. |
| 4 | `P4 World` | Hub, 3 zones and 3 bosses | Level-gated portals. Each boss has a phase state machine. Party boss instances run through Lobby or Matchmaker. |
| 5 | `P5 Quests` | NPCs and quests | Quest givers, kill/collect/talk objectives, a quest log UI, rewards. |
| 6 | `P6 Economy & polish` | Shop, balance, juice | Shop and currencies. Remote Config tuning. VFX and SFX pass. 60 fps on the reference phone. |
| 7 | `P7 Release` | Store launch | Play internal track and TestFlight builds from CI. Privacy policy, age rating and account deletion are in place. Soft launch. |

## Rules

- Stories come from [GAME_DESIGN.md](GAME_DESIGN.md) through `/stories`. A human approves them before they become issues.
- No phase starts until the previous phase meets its criteria, except for spikes that are labelled `spike`.
