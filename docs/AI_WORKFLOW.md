# AI workflow

This page explains how the AI agents and the human owner build Emberwright together. It covers who does what, when work moves to a stronger (more expensive) model, and how Gemma improves over time.

## Roles

| Agent (opencode ID) | Model | Job | When it runs |
|---|---|---|---|
| `build` (primary) | `mlx/gemma4` (local) | Implements tickets, runs tests, opens PRs | Every ticket |
| `test-writer` | `mlx/gemma4` | Writes failing NUnit tests from the acceptance criteria | Start of every ticket |
| `reviewer-local` | `mlx/gemma4` | Runs `ponytail-review` and the verify checklist on the diff | Before every PR |
| `delegator` | `claude-agent/sonnet` (Sonnet 5.5) | Turns the GDD into stories, and features into Gemma-sized tasks | Once per feature |
| `delegator-pro` | `claude-agent/opus` (Opus 5.5) | Same job for architecture-level or `difficulty:high` features | Rarely |
| `tutor-openai` | `openai/gpt-6.2-sol-fast` | Unblocks Gemma with a hint or a minimal fix, then writes a lesson | After 2 red test runs |
| `tutor-openai-frontier` | `openai/gpt-6.2-sol` | Solves hard problems, explains the pattern, writes a lesson and a training pair | After `tutor-openai` fails, or for `difficulty:high` |
| Claude Code | Claude (outside opencode) | Verifies PRs against the architecture and the tests | Every PR |
| Human | You | Approves stories, approves escalations to Claude, merges PRs | Gates |

> **Verify the model IDs** with `opencode models` and the providers' docs, then edit the `model:` lines in `.opencode/agents/*.md`:
> - The OpenAI tier picks above are placeholders: the newest `sol` model for the frontier tier, and its `-fast` variant for the mid tier.
> - `claude-agent/sonnet` and `claude-agent/opus` are aliases. Confirm that they resolve to the 5.5 models.

## Escalation ladder (hard rules)

Gemma does not decide on its own when it is stuck. These triggers decide:

| Trigger | Action |
|---|---|
| The same test fails after 2 fix attempts | Write down the premise that both attempts shared. Test the premise. If it still fails, call `tutor-openai` with `/escalate`. |
| `tutor-openai` fix fails, or the ticket is `difficulty:high` | Call `tutor-openai-frontier` |
| The frontier tutor fails, or a design question crosses assemblies | **Ask the human**, then call `delegator-pro` (Opus) |
| A feature needs breaking down | **Ask the human**, then call `delegator` (Sonnet) |

The Claude agents are set to `ask` in `opencode.json`, so opencode always asks before it spends Claude usage.

### Routing by ticket difficulty

| Difficulty | Planned by | Built by | Escalation path |
|---|---|---|---|
| low | Sonnet (batched per feature) | Gemma | Gemma retry → OpenAI mid |
| medium | Sonnet | Gemma | OpenAI mid → OpenAI frontier |
| high | Opus | Gemma, paired with OpenAI frontier from the start | OpenAI frontier → Opus |

Every escalation adds one row to [ai/usage-log.md](ai/usage-log.md).

## Ticket flow

```
docs/GAME_DESIGN.md ──/stories (Sonnet)──▶ docs/backlog/draft-*.md ──human approves──▶ GitHub Issues
                                                                                          │
   ┌──────────────────────────── /next-issue (Gemma) ◀───────────────────────────────────┘
   ▼
 test-writer: failing tests ─▶ build: code until green ─▶ reviewer-local: /ponytail-review + /verify
   │                                   │ 2 red runs                         │
   │                                   ▼                                    ▼
   │                         /escalate → tutor → lesson              PR "Closes #n"
   │                                                                        │
   └────────────────────────────────────────────── Claude Code review ◀──────┘ ──▶ human merges
```

## The learning loop: how Gemma gets better

A model's weights do not change while it works. Gemma improves through what it reads:

1. **Lessons.** Every tutor intervention writes `docs/ai/lessons/NNN-<topic>.md`, using the `teach-gemma` skill. A lesson holds the symptom, the wrong pattern, the right pattern and a minimal example.
2. **Targeted loading.** The `project-lessons` skill indexes lessons by topic. The delegator names the relevant lessons in each ticket, so Gemma loads only what applies.
3. **Promotion.** When the same lesson is needed twice, fix it at the highest level that works:
   1. architecture or types that make the mistake impossible
   2. a test or analyzer that fails on it
   3. a rule in `AGENTS.md` or a skill
4. **Training pairs (optional, later).** Tutors also save `{task, failed attempt, fix}` pairs as JSONL in `docs/ai/training/`. That folder is git-ignored and stays on your machine.
   - After about 300 good pairs, you can train a LoRA adapter with `mlx_lm.lora` and serve it with `mlx_lm.server --adapter-path`.
   - Keep the adapter only if Gemma's first-try pass rate improves on the fixed eval set in [ai/evals/](ai/evals/).
   - Check OpenAI's terms of use about training on its outputs before you do this.
5. **Metrics.** `usage-log.md` tracks Gemma's first-try pass rate and escalations per week.

## Gemma 4 tuning

| Setting | Value | Where |
|---|---|---|
| Model | `mlx-community/gemma-4-26B-A4B-it-qat-q4_0-mlx-aligned` | `mlx_lm.server` |
| Context limit | 65536 | `opencode.json` → `providers.mlx.models.gemma4.limit` |
| Output limit | 8192 | same |
| Temperature | 0.2 | same, `body.temperature` |
| Always-loaded instructions | `AGENTS.md` only (≤150 lines) | opencode loads it automatically |
| Everything else | Skills, loaded on demand | `.opencode/skills/` |

Keep the context small:
- Tickets name the exact files and skills to load.
- Ponytail's rules are not injected on every turn. Only the review skill is used, at review time.
- Long docs are linked from skills, not inlined.
- Only the project skills are allowed (`opencode.json` → `permissions`, the `skill` rules). This hides global skills such as browser or computer-use from Gemma. **When you add a skill, add an `allow` rule for its ID.**

If 65536 tokens of context makes the Mac swap, lower the limit to 32768. To test it, run a long session and watch memory pressure in Activity Monitor.

Suggested server command (you run it):

```sh
mlx_lm.server --model mlx-community/gemma-4-26B-A4B-it-qat-q4_0-mlx-aligned \
  --host 127.0.0.1 --port 8080
```

## Third-party skills

- Selected [pstack](https://github.com/backnotprop/pstack) principles and playbooks (MIT, by Lauren Tan) are adapted into the project skills.
- Ponytail's review skill (MIT, by Dietrich Gebert) is ported as `ponytail-review`.
- Source commits and licenses are in [.opencode/THIRD_PARTY_NOTICES.md](../.opencode/THIRD_PARTY_NOTICES.md).

## Verification checklist (Claude Code and reviewer-local)

- [ ] The PR closes exactly one issue, and every acceptance criterion has a test.
- [ ] Tests fail without the change: the test came first, or reverting the change shows it.
- [ ] `dotnet test Tests/Emberwright.Core.sln` is green, and so are the Unity tests if they are affected.
- [ ] Game rules are in `Emberwright.Core`, and the Behaviours are thin.
- [ ] No client-trusted values: damage, currency, inventory, cooldowns.
- [ ] Grants are idempotent: each takes a request ID.
- [ ] No unneeded abstractions, dead code or duplicate helpers (`/ponytail-review`).
- [ ] Docs and lessons are updated if behaviour or a pattern changed.
