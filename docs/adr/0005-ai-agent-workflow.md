# ADR 0005: Local Gemma builds; OpenAI tutors; Claude delegates and verifies

- Status: accepted
- Date: 2026-11-19

## Context
Claude and OpenAI usage is limited. A local Gemma 4 (26B MoE on an M4 Pro) costs nothing per token, but it is weaker.

## Decision
- Gemma is the primary builder in opencode.
- Claude Sonnet breaks features into tasks, with Opus as the fallback, and needs human approval for each call.
- OpenAI models tutor Gemma when hard triggers fire, and record lessons.
- Claude Code verifies PRs.
- See [AI_WORKFLOW.md](../AI_WORKFLOW.md).

## Consequences
- Tickets must be small and precise, with named files, tests and skills.
- Lessons accumulate in `docs/ai/lessons/`. They need pruning and promotion into structure.
- Escalations are logged, so cost and Gemma's progress are visible.
