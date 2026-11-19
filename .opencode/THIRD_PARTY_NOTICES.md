# Third-party notices

Parts of the skills in `.opencode/skills/` are adapted from the projects below. They were reviewed in full before adaptation. The adapted text was condensed and rewritten for Emberwright (Unity, C#, a small local model).

## pstack

- Source: https://github.com/backnotprop/pstack (a mirror of Cursor's `pstack` plugin by Lauren Tan, also known as poteto)
- Commit reviewed: `0d2dc00df47d7686c94324cfe9c3444710d9f16d`
- Used in: `emberwright-tdd`, `unity-csharp-conventions`, `ngo-server-authority`, `escalation-policy`, `teach-gemma`, `task-slicing`, `emberwright-verify`, `debug-playbook`, `docs-writing`, and the simplicity rules in `AGENTS.md`
- Not used: the autonomy rules that proceed without asking, `autopilot-*`, `shipping`, `setup-pstack`, `make-bot-ui`, `automate-me`, and the TypeScript-specific skills

```
MIT License

Copyright (c) 2026 Lauren Tan

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

## Ponytail

- Source: https://github.com/DietrichGebert/ponytail (Claude Code marketplace `ponytail`, plugin `ponytail@ponytail`)
- Version pinned: tag `v5.3.3` (commit `7801d061243d7b753bad0e0b3c057cd250b11755`). Reviewed at `96608e0`, whose `skills/ponytail-review`, `hooks/` and `.claude-plugin/` are identical to the tag.
- Used in: `.opencode/skills/ponytail-review/SKILL.md` (a port of `skills/ponytail-review/SKILL.md`). Its hooks and always-on rules are **not** used in opencode.
- Claude Code loads the full plugin through `.claude/settings.json`.

```
MIT License

Copyright (c) 2026 DietrichGebert

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
