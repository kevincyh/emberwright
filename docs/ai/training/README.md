# Training pairs (local only)

This folder is git-ignored except for this README. Tutors append JSONL records here:

{"task": "...", "context_files": ["..."], "failed_attempt": "<diff>", "fix": "<diff>", "lesson": "NNN", "model": "openai/...", "date": "YYYY-MM-DD"}

Only consider LoRA fine-tuning after about 300 good pairs, and after you check the provider's terms of use (see AI_WORKFLOW.md).
