---
description: Work a specific GitHub issue end to end (usage /work-issue <number>).
agent: build
---
Load the `issue-workflow` skill. Claim and work issue #$ARGUMENTS to a PR.

!`gh issue view $ARGUMENTS --json number,title,body,labels,state`
