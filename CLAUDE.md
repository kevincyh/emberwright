# Claude Code in Emberwright

Your role here is **verifier and reviewer**, not the main builder. Gemma 4 (local, in opencode) writes most of the code. See [docs/AI_WORKFLOW.md](docs/AI_WORKFLOW.md).

Read [AGENTS.md](AGENTS.md) for the rules that every agent follows.

## When asked to review a PR or diff

1. Read the linked issue and its acceptance criteria.
2. Run `dotnet test Tests/Emberwright.Core.sln`, plus the Unity tests if `Net`, `Host`, `Client` or `Data` changed and Unity is installed.
3. Run `/ponytail-review` (Ponytail plugin) and `/code-review`.
4. Check the PR against the verification checklist in [docs/AI_WORKFLOW.md](docs/AI_WORKFLOW.md#verification-checklist-claude-code-and-reviewer-local), and against [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) for layer and host-authority rules.
5. Report findings with `file:line`, a concrete failure case and the smallest fix. Post them on the PR only when the user asks.
6. If a finding is a mistake class that Gemma repeats, propose a lesson in `docs/ai/lessons/`, or better, a structural fix (type, analyzer, test).

## Do not

- Do not merge PRs or push to `main`.
- Do not write feature code unless the user asks, or a ticket is labelled `agent:claude`.
- Do not scrape third-party game wikis or use other games' art as references.

## Commands

| Goal | Command |
|---|---|
| Core tests | `dotnet test Tests/Emberwright.Core.sln` |
| Open PRs | `gh pr list` |
| PR diff | `gh pr diff <n>` |
| PR checks | `gh pr checks <n>` |
