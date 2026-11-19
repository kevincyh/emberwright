# Roadmap

Each phase is a GitHub milestone. A phase is done only when all of its acceptance criteria pass.

| Phase | Milestone | Goal | Acceptance criteria |
|---|---|---|---|
| 0 | `P0 Foundation` | The repo, docs, CI and agent setup | CI is green on `main`. The opencode agents load. The board holds the Phase 1 stories. |
| 1 | `P1 Prototype` | Offline playable slice (solo) | Move with the joystick. Do a 3-hit combo. One enemy type with AI. Health and death. One test zone. It runs as a host session with no clients. Core tests ≥80% coverage. |
| 2 | `P2 Local co-op` | 2–4 phones play together | Host and Join screens. LAN discovery finds the host on the same Wi-Fi. QR code and manual IP entry are the fallbacks. Request Apple's multicast entitlement early. Joining works on the host's hotspot. Android↔iOS works. An NGO test proves that a client cannot set damage. |
| 3 | `P3 Progression` | XP, stats, swords, local saves | Level up, spend stat points, get swords as drops, upgrade a sword. The save survives an app restart. Saves are written atomically and migrated by version. A co-op reward is applied once per device. |
| 4 | `P4 World` | Hub, 3 zones and 3 bosses | Level-gated portals. Each boss has a phase state machine. The co-op party follows the host through scenes. |
| 5 | `P5 Quests` | NPCs and quests | Quest givers, kill/collect/talk objectives, a quest log UI, rewards. |
| 6 | `P6 Economy & polish` | Shop, balance, juice | Shop and currencies. Tuning in ScriptableObjects. VFX and SFX pass. 60 fps on the reference phone, including while hosting. |
| 7 | `P7 Release` | Store launch | Play internal track and TestFlight builds from CI. Privacy policy, age rating and the local network permission text are in place. Soft launch. |

## After launch (optional)

- **Remote co-op:** a Unity Relay join code, so friends can play from different places. Only the transport changes.
- **Save backup:** UGS Authentication plus Cloud Save, opt-in.

## Rules

- Stories come from [GAME_DESIGN.md](GAME_DESIGN.md) through `/stories`. A human approves them before they become issues.
- No phase starts until the previous phase meets its criteria, except for spikes that are labelled `spike`.
