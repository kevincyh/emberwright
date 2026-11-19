---
description: Break a story issue into Gemma-sized task issues (Claude Sonnet delegator). Usage /slice <issue number>.
agent: delegator
subagent: true
---
Load the `task-slicing` skill. Slice story #$ARGUMENTS into tasks. Write them to docs/tasks/ as a draft for human approval.

!`gh issue view $ARGUMENTS --json number,title,body,labels`
