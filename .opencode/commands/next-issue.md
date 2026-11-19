---
description: Pick the next ready GitHub issue for Gemma, claim it, and start the TDD loop.
agent: build
---
Load the `issue-workflow` skill. Pick the next issue using its "Pick" rules, claim it, then work it to a PR.

Ready issues right now:
!`gh issue list --state open --label "status:ready" --label "agent:gemma" --json number,title,labels --limit 30`
