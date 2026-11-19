---
name: Impressions
description: Manage the art impressions library (art/impressions) - add your own or licensed references, generate new images from approved impressions with OpenAI, review candidates. Load for /impression-* commands or art tickets.
---
# Impressions

The format and rules are in docs/ART_DIRECTION.md. The tool is `scripts/impressions/` (Python plus the OpenAI SDK). It gets built from a ticket. If it does not exist yet, say so and stop.

## Hard rules

- Only these sources are allowed: `user` (your own sketches, photos or notes), `licensed` (with the license and attribution recorded), and `generated` (from approved impressions).
- **Never** add or use art, screenshots or wiki images from other games, even as "inspiration".
- Only `approved` items feed new generations.
- The API key comes from the `OPENAI_API_KEY` environment variable. Never write it to a file.

## Commands the tool provides (spec for the ticket that builds it)

| Command | Does |
|---|---|
| `python -m scripts.impressions add <file-or-text> --source user|licensed --license <x> [--tags a,b]` | Copies the file to `items/`. Writes an `index.jsonl` record with status `approved` for user items, or `candidate` for licensed ones. Uses an OpenAI vision model to propose tags and a description. Refuses a missing source or license. |
| `python -m scripts.impressions generate "<brief>" [--k 5] [--n 2]` | Retrieves the top-k approved impressions (by tags, then embeddings). Builds the prompt from the brief, docs/ART_DIRECTION.md and the impression descriptions. Calls the OpenAI Images API with the approved images as references. Saves the outputs as `candidate`, with `parents` and `prompt`, and logs the cost to docs/ai/usage-log.md. |
| `python -m scripts.impressions review` | Lists candidates. `approve <id>` or `reject <id>` updates the status. |
| `python -m scripts.impressions search "<query>"` | Prints matching records. |

Implementation notes for the builder:
- Read the current image and embedding model IDs from the OpenAI docs at build time. Put them in one constant per model.
- `index.jsonl` writes must be idempotent: rewrite to a temporary file, then rename it into place.
- Tests use a fake OpenAI client. They never call the network.

## Feedback loop

add your impressions → generate → review → approved items join the pool → the next generate uses them. Update docs/ART_DIRECTION.md when approved items show a clear style choice.
