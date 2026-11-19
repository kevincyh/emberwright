---
description: Draft user stories from docs/GAME_DESIGN.md for human approval (Claude Sonnet delegator). Usage /stories [phase or epic].
agent: delegator
subagent: true
---
Load the `stories` skill. Produce the draft for: $ARGUMENTS (if empty, the next roadmap phase without stories).
Write only the draft and stop. Do not create issues until the human approves.
