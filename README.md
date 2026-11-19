# Emberwright

Emberwright is an original online multiplayer sword action RPG for Android and iOS, built with Unity 6.

Players collect and upgrade swords, level up, fight through zones and bosses with friends, and take on quests from NPCs. The world, names and art are original to Emberwright.

> Status: **pre-production**. The repo holds the architecture, docs, CI/CD and the AI-agent setup. Feature work starts from the GitHub Issues board.

## How this repo is built

Most code is written by AI agents and reviewed by people:

| Role | Who | Docs |
|---|---|---|
| Builder (writes code, runs tests) | Gemma 4, running locally in [opencode](https://opencode.ai) | [AGENTS.md](AGENTS.md) |
| Delegator (splits features into tasks) | Claude Sonnet, with Opus as the fallback | [docs/AI_WORKFLOW.md](docs/AI_WORKFLOW.md) |
| Tutors (unblock Gemma, write lessons) | OpenAI models | [docs/AI_WORKFLOW.md](docs/AI_WORKFLOW.md) |
| Verifier (reviews PRs) | Claude Code | [CLAUDE.md](CLAUDE.md) |
| Owner (approves stories, merges PRs) | Human | [CONTRIBUTING.md](CONTRIBUTING.md) |

## Quick start

Prerequisites:
- macOS with Xcode
- Unity Hub, plus Unity 6 LTS with the Android and iOS Build Support modules
- .NET 8 SDK
- Git LFS
- GitHub CLI (`gh`)
- opencode

```sh
git lfs install
git clone <repo-url> && cd emberwright

# Fast Core tests (no Unity needed)
dotnet test Tests/Emberwright.Core.sln

# Start the builder agent (Gemma 4 must be serving on 127.0.0.1:8080)
opencode
```

To work the next ticket inside opencode, type `/next-issue`.

## Docs

- [Architecture](docs/ARCHITECTURE.md)
- [Game design](docs/GAME_DESIGN.md)
- [Roadmap](docs/ROADMAP.md)
- [Coding standards](docs/CODING_STANDARDS.md)
- [Testing](docs/TESTING.md)
- [CI/CD](docs/CI_CD.md)
- [AI workflow](docs/AI_WORKFLOW.md)
- [Art direction](docs/ART_DIRECTION.md)
- [Architecture decisions](docs/adr/)
- [Contributing](CONTRIBUTING.md)
- [Security](SECURITY.md)
