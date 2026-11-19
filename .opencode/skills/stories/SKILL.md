---
name: Stories
description: For the delegator. Turn docs/GAME_DESIGN.md into epics and user stories, draft them for human approval, then push the approved ones to GitHub Issues without duplicates. Use for /stories.
---
# Stories

## Inputs

Read `docs/GAME_DESIGN.md` (the only source of mechanics), `docs/ROADMAP.md` (phases become milestones) and `docs/ARCHITECTURE.md`.

If a GAME_DESIGN section still says `TODO`, do not invent the design. List it under "Open questions" in the draft.

## 1. Draft (always first)

Write `docs/backlog/draft-YYYY-MM-DD.md`:

```markdown
# Backlog draft YYYY-MM-DD (phase: P1 Prototype)

## Epic: Combat basics (EW-COMBAT)

### EW-COMBAT-001: Three-hit combo
- Story: As a player, I want to chain three attacks with the attack button, so that combat feels fluid.
- Acceptance:
  - [ ] Given an idle player, when attack is pressed 3 times within 0.6 s windows, then hits 1, 2 and 3 play in order.
  - [ ] Given hit 2, when no press arrives within 0.6 s, then the combo resets to hit 1.
- Difficulty: low · Area: combat · Milestone: P1 Prototype
- Depends on: none
- Skills: emberwright-tdd, core-dotnet-tests
- Tasks: (optional, task-slicing format)

## Open questions
- ...
```

Rules:
- IDs are `EW-<AREA>-NNN` and never change. The sync step uses them.
- Each acceptance criterion is testable, with concrete numbers.
- Stories are thin vertical slices. Split anything that would be `high` and more than 6 files.
- Use only names and mechanics from GAME_DESIGN.md.

Stop here. Ask the human to review and approve the draft.

## 2. Push the approved stories (only after explicit approval)

For each approved story, first check whether its ID already exists:

```sh
gh issue list --state all --search "EW-COMBAT-001 in:body" --json number --jq 'length'
```

- If the result is `0`, create the issue:
  `gh issue create --title "[Story] Three-hit combo" --body-file <tmp> --label "type:story,status:backlog,difficulty:low,area:combat,agent:gemma" --milestone "P1 Prototype"`
- If the result is `1` or more, skip it, or update it with `gh issue edit` only if the human asked.
- The body uses the `.github/ISSUE_TEMPLATE/user-story.yml` field headings, including `### Story ID`.
- Add the issue to the project board: `gh project item-add <project-number> --owner @me --url <issue-url>`.

When the human says which stories are ready, relabel them from `status:backlog` to `status:ready`.

Reply with a table: ID | issue number | created or skipped.
