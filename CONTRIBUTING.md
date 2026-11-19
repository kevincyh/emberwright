# Contributing

## Flow

1. Work comes from a GitHub issue labelled `status:ready`. No ticket, no code.
2. Branch from `main` as `feat/<issue>-<slug>` or `fix/<issue>-<slug>`.
3. Write the failing test first. See [docs/TESTING.md](docs/TESTING.md).
4. Make it pass with the smallest change. Follow [docs/CODING_STANDARDS.md](docs/CODING_STANDARDS.md).
5. Run `/ponytail-review` and `/verify` in opencode, or the same checks by hand.
6. Open a PR that says `Closes #<issue>`, and fill in the template.
7. Claude Code reviews it, then a human merges. Agents never merge.

## Labels

| Group | Labels |
|---|---|
| Type | `type:story`, `type:task`, `type:bug`, `spike` |
| Status | `status:backlog`, `status:ready`, `status:in-progress`, `status:blocked`, `status:needs-review` |
| Difficulty | `difficulty:low`, `difficulty:medium`, `difficulty:high` |
| Area | `area:combat`, `area:progression`, `area:items`, `area:world`, `area:quests`, `area:net`, `area:ui`, `area:ci`, `area:docs`, `area:art`, `area:tools` |
| Agent | `agent:gemma`, `agent:openai`, `agent:claude`, `agent:human` |

## Commits

Use Conventional Commits, for example `feat(items): add sword upgrade cost curve`. Each commit builds and passes the tests.

## Docs

Write docs with the `docs-writing` skill: short sentences, real file names, one idea per sentence.
