---
name: Debug playbook
description: Find and fix the root cause of a bug or unexpected test failure with evidence, not guesses. Load for bug tickets or any surprising red test.
---
# Debug playbook

Adapted from pstack `bug-fix`, `investigation` and `fix-root-causes` (MIT, Lauren Tan).

Every line you ship must trace back to evidence. A change that "might help" is a guess, and guesses do not ship.

1. **Reproduce.** Write the smallest failing test or command that shows the bug. Paste its output.
2. **Narrow down.** List 2 or 3 possible causes. Rule them out one by one with a test, a log line or by reading the actual value. Do not guess.
3. **Find the root.** The root cause is the first place where the code did something wrong. Ask:
   - Is the bad state legitimate? If yes, handle it where the code broke.
   - Should the bad state never exist? Then find what produced it. A check where the error shows up hides the bug.
4. **Fix the pattern.** Search for the same fault elsewhere: `grep -rn "<pattern>" Assets`. Fix every instance, or list them in the PR.
5. **Prove it.** The reproducing test fails before the fix and passes after it. Paste both outputs.
6. **Revert guesses.** If evidence disproves an idea, undo the code you added for it.

Never hide bugs with:
- try/catch that swallows errors
- extra null checks or clamps
- retries
- changing the test to match the wrong behaviour

After 2 failed fixes, load `escalation-policy`.
