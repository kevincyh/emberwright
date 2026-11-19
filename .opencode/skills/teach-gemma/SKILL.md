---
name: Teach Gemma
description: For tutor agents. Turn a fixed mistake into a lesson, a training pair, a usage-log row, and (on repeats) a structural guard. Load after helping Gemma.
---
# Teach Gemma

Adapted from pstack `correct`, `reflect` and `encode-lessons-in-structure` (MIT, Lauren Tan).

Gemma cannot remember. It reads. Every fix you make must leave something behind that Gemma will read next time, or better, something that makes the mistake impossible.

## 1. Lesson (always)

Check whether `docs/ai/lessons/` already has a lesson for this mistake class.
- If it does, increase `seen` by 1 and improve its example.
- If not, create `NNN-<topic>.md` with the next free number:

```markdown
---
id: NNN
topic: netcode
applies_to: ["Assets/_Game/Scripts/Net/**"]
seen: 1
---
## Symptom
What Gemma saw (error text or the wrong behaviour), in one or two lines.
## Wrong
The pattern Gemma used: 3 to 10 lines of code.
## Right
The correct pattern: 3 to 10 lines of code.
## Example
A minimal, complete example that compiles in C# 9.
```

Add a row to the index table in `docs/ai/lessons/README.md`. Keep the lesson under 40 lines. It must be useful to someone who sees only this file.

## 2. Usage log (always)

Append to `docs/ai/usage-log.md`: date | #issue | agent | model | trigger | outcome | lesson ID.

## 3. Training pair (frontier tutor only)

Append one JSON line to `docs/ai/training/pairs.jsonl` (git-ignored):
`{"task":..., "context_files":[...], "failed_attempt":"<diff>", "fix":"<diff>", "lesson":"NNN", "model":"...", "date":"YYYY-MM-DD"}`

## 4. Promote on repeat (`seen` ≥ 2)

Prose did not work, so fix the mistake at the highest level that does:
1. **Architecture.** Give the state one owner and the task one supported way to do it. Make the wrong import fail.
2. **Types.** Make the bad state impossible to write.
3. **A test or analyzer** whose failure message names the fix.
4. **A rule in AGENTS.md**, only for judgment calls.

Propose the guard in your reply. If it is larger than the current ticket, open an issue for it with labels `type:task` and `area:tools`.
