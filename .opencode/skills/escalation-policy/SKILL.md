---
name: Escalation policy
description: When and how Gemma asks for help - hard triggers, which agent to call, what context to pass, and how to log it. Load before /escalate or on difficulty:high tickets.
---
# Escalation policy

Usage of OpenAI and Claude is limited. Escalate on these triggers, never on a feeling.

## Triggers

| # | Trigger | Do this |
|---|---|---|
| 1 | The same test is red after 2 fix attempts | **Attack the premise** (below). If it is still red, call `tutor-openai`. |
| 2 | `tutor-openai`'s fix is red, or the ticket is `difficulty: high` | Call `tutor-openai-frontier` |
| 3 | The frontier tutor says the design is wrong, or the change crosses assemblies in a way the ticket did not plan | Stop. Comment on the issue and label it `agent:claude`. **The human** decides whether to call `delegator-pro`. |
| 4 | A feature has no task breakdown | **The human** calls `delegator` (`/stories` or `task-slicing`) |

You may call the `tutor-*` agents yourself. You must never call `delegator` or `delegator-pro` yourself.

## Attack the premise (pstack principle, MIT, Lauren Tan)

Before escalating:
1. Write the one sentence that both failed attempts assumed. For example: "The damage formula runs on the client."
2. Find one quick check that would come out differently if that sentence were false. Run it.
3. If the premise was false, fix the premise, not the symptom. If it was true and the test is still red, escalate.

## What to send (the /escalate packet)

```
Ticket: #<n> <title> (difficulty: <d>)
Goal: <one sentence>
Premise both attempts shared: <sentence>, check result: <what you saw>
Attempt 1: <what you changed> → <failure line>
Attempt 2: <what you changed> → <failure line>
Diff: <git diff, trimmed to the files in scope>
Failing output: <the first 40 lines of dotnet test output>
Files: <paths>
```

## After help arrives

1. Read the lesson the tutor wrote (in `docs/ai/lessons/`).
2. Continue the ticket yourself. Rerun all tests.
3. Make sure a row was added to `docs/ai/usage-log.md`.
4. Relabel the issue: remove `agent:gemma` and add `agent:openai` if the tutor wrote most of the fix.
