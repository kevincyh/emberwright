---
name: Docs writing
description: How to write Emberwright docs, ADRs, lessons, PR descriptions and commit messages - short, concrete, no filler. Load when writing any prose.
---
# Docs writing

Adapted from pstack `technical-writing` and `unslop` (MIT, Lauren Tan).

- Write so a tired engineer understands on the first read.
- One idea per sentence. Split sentences longer than about 25 words.
- Use the short, everyday word: "use", not "utilize".
- Write the real name of the file, type, command or flag, not a description of it.
- Be specific. Not "this may cause issues" but "a level of 0 throws ArgumentOutOfRangeException".
- Write instructions as commands. Put the condition before the step.
- Use numbered lists for steps and bullets for everything else.
- Use code font for code, paths and commands.
- No filler: no "simply", "just", "easy", "robust", "seamless", "leverage", "delve", and no "In conclusion".
- No hedging unless you are actually unsure. If you are, say what you did not check.

## One document, one job

| Kind | Purpose | Example |
|---|---|---|
| How-to | Steps to a goal | docs/CI_CD.md "Release flow" |
| Reference | Facts to look up, kept dry | Tables of labels, secrets and commands |
| Explanation | Why it is built this way | ADRs, docs/ARCHITECTURE.md |

## PR description

- Start with what changed and why, in 2 or 3 sentences, with `Closes #n`.
- Say how it was verified: the test names and a pasted summary line.
- A reviewer reads it in under a minute. Link to logs; do not paste them.

## Commit message

Use `type(area): what changed`, in the imperative, under 72 characters. Example: `fix(combat): stop combo reset when attack lands on tick boundary`
