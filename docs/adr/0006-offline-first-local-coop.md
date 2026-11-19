# ADR 0006: Offline single-player with host-based local co-op

- Status: accepted
- Date: 2026-11-20
- Supersedes: [0002](0002-ngo-dedicated-server-authority.md), [0003](0003-ugs-backend.md)

## Context
The first plan was an online game with a dedicated server and the full UGS backend. The owner now wants a game that is mainly single-player, where friends can play together by connecting their devices. Running servers would cost money and add work that this goal does not need.

## Decision
- **Single-player is a host session with zero clients.** NGO stays, so solo and co-op share one code path.
- **Co-op: one phone hosts, and 1–3 others join** over the local network with Unity Transport.
  - Joining players find the host by UDP broadcast discovery, by scanning a QR code on the host's screen, or by typing its IP address. Broadcast on iOS needs Apple's multicast entitlement, which must be requested; QR and manual IP need no entitlement.
  - With no Wi-Fi, the host's mobile hotspot provides the network.
- **The host is authoritative** for the shared world: damage, enemies, drops and XP. Clients send intent only.
- **Each device keeps its own local save.** Rewards carry a `rewardId` and are applied once.
- **Remote co-op later, optionally,** through Unity Relay join codes. Only the transport changes.

## Alternatives considered

| Option | Why not |
|---|---|
| Dedicated server plus UGS backend (ADR 0002/0003) | Server cost and work that offline-first play does not need |
| Bluetooth (Apple MultipeerConnectivity, Google Nearby Connections) | No reliable Android↔iOS support, and too little bandwidth and too much latency for real-time combat |
| Wi-Fi Direct | Android-only |
| Separate offline and online code paths | Two code paths to build and test |

## Consequences
- No backend to run and no hosting bills. The game works offline.
- Saves are local, so a player can edit their own save. This is acceptable for a co-op game; there is no PvP ranking or trading economy to protect.
- A premium currency cannot be validated without a server. Prefer one-off purchases or cosmetics with store receipt validation.
- A host that leaves ends the session for everyone. Host migration is out of scope.
- Hosting adds CPU load to the host phone. The performance budget applies while hosting.
