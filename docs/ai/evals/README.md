# Evals

A fixed set of small tasks with known-good tests. Use it to measure whether Gemma, a new prompt, a new skill or a LoRA adapter actually improves results.

Each eval is a folder `NNN-<name>/` containing:
- `task.md`: the prompt, written exactly as a ticket would be
- `tests/`: NUnit tests that must pass
- `notes.md`: past results (date, model or adapter, pass or fail, attempts)

Add an eval whenever a lesson is written for a mistake that keeps coming back.
