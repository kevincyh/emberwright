---
description: Package the current failure and ask the right tutor for help, following the escalation policy.
agent: build
---
Load the `escalation-policy` skill.
1. Confirm that a trigger is met (2 red attempts on the same test, or difficulty:high). If none is met, keep working instead.
2. Write the premise check.
3. Build the escalation packet.
4. Call `tutor-openai`. Call `tutor-openai-frontier` only if trigger 2 applies.
5. Afterwards, read the lesson the tutor wrote, then continue.

Extra context from the user: $ARGUMENTS

Current diff summary:
!`git diff --stat`
