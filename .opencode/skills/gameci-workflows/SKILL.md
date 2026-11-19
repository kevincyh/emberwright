---
name: GameCI workflows
description: How Emberwright's GitHub Actions work and how to change them safely. Load when editing .github/workflows/.
---
# GameCI workflows

Reference: docs/CI_CD.md.

| File | Jobs |
|---|---|
| `ci.yml` | `core` (dotnet format + test), `detect`, `unity-tests` (EditMode and PlayMode matrix) |
| `build.yml` | `detect`, `build` (Android AAB and iOS matrix) |
| `release.yml` | Gated by `vars.RELEASE_ENABLED`. Store uploads through fastlane. |
| `e2e.yml` | Gated by `vars.E2E_ENABLED`. AltTester on an Android emulator, and performance tests. |

## Rules

1. Unity jobs only run after `detect` finds `ProjectSettings/ProjectVersion.txt` and the `UNITY_LICENSE` secret. Keep that gate.
2. Secrets are only referenced as `${{ secrets.NAME }}`. Never echo them. Never add new secrets to the repo.
3. Check out with `lfs: true` for any job that builds or tests Unity.
4. Cache `Library/` keyed on `Packages/packages-lock.json` and `ProjectSettings/ProjectVersion.txt`.
5. Use action versions with a major tag (`@v4`). Dependabot bumps them.
6. A new required secret goes into the docs/CI_CD.md secrets table in the same PR.

## Test a workflow change

- Push the branch and open a draft PR. CI runs on `pull_request`.
- For `build.yml`, use **Run workflow** (workflow_dispatch) on your branch.
- Read the logs with `gh run list` and `gh run view <id> --log-failed`.
