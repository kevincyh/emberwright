# Art direction

> A living style guide. Update it as impressions are approved in `art/impressions/`.

## Style

- 3D low-poly with readable silhouettes. Players must read it on a 6-inch screen.
- TODO: palette (for example warm ember oranges against cool ash greys)
- TODO: shape language (for example angular blades, rounded friendly NPCs)
- TODO: lighting mood per zone

## Rules

- Every asset is original, made by us or generated from our own approved impressions, or it is licensed. Record the license in `art/impressions/index.jsonl`.
- Do not use art, screenshots or characters from other games as references or inputs.
- UI must stay readable at 360×640 dp.

## Impressions library

`art/impressions/` holds the references that guide image generation:

| Field in `index.jsonl` | Meaning |
|---|---|
| `id` | `imp-0001`, … |
| `type` | `image` or `text` |
| `source` | `user`, `generated` or `licensed` |
| `license`, `attribution` | Required for `licensed` items. Use `owned` for your own work. |
| `tags`, `description` | Used for search and retrieval |
| `prompt`, `model`, `parents` | Lineage for `generated` items |
| `status` | `candidate`, `approved` or `rejected`. Only `approved` items feed new generations. |
| `created` | ISO date |

The loop: add your impressions → `/impression-generate "<brief>"` → review the candidates → approved images join the pool. See `.opencode/skills/impressions/SKILL.md`.
