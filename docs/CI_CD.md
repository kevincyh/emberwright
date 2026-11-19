# CI/CD

All pipelines run on GitHub Actions. Unity jobs use [GameCI](https://game.ci).

## Workflows

| Workflow | Trigger | What it does | Active when |
|---|---|---|---|
| `ci.yml` | Every PR, push to `main` | `dotnet format` check and Core tests with coverage. Unity EditMode and PlayMode tests with coverage. | Core: always. Unity: once `ProjectSettings/ProjectVersion.txt` exists and `UNITY_LICENSE` is set. |
| `build.yml` | Push to `main`, manual | Builds the Android AAB, the iOS Xcode project and the Linux dedicated server. Uploads them as artifacts for 14 days. | Same as the Unity jobs in CI |
| `release.yml` | Tag `v*`, manual | Android → Play internal track. iOS → TestFlight (macOS runner). Server image → GHCR. | Repository variable `RELEASE_ENABLED=true` |
| `e2e.yml` | Nightly, manual | AltTester suite on an Android emulator, plus the performance tests | Repository variable `E2E_ENABLED=true` |

Dependabot updates the Actions and NuGet versions weekly.

## Required checks on `main`

- `Core tests (dotnet)`
- `Unity editmode tests` and `Unity playmode tests`, once the Unity jobs are active

## Secrets

Add these in **Settings → Secrets and variables → Actions**. Never commit them. See [SECURITY.md](../SECURITY.md).

| Secret | Needed for | How to get it |
|---|---|---|
| `UNITY_LICENSE` | All Unity jobs | Contents of the `.ulf` file from Unity activation (Personal) or a serial (Pro). See the GameCI activation guide. |
| `UNITY_EMAIL`, `UNITY_PASSWORD` | All Unity jobs | Your Unity ID |
| `ANDROID_KEYSTORE_BASE64` | Android build and release | `base64 -i emberwright.keystore` |
| `ANDROID_KEYSTORE_PASS`, `ANDROID_KEYALIAS_NAME`, `ANDROID_KEYALIAS_PASS` | Android build and release | Chosen when you create the keystore |
| `GOOGLE_PLAY_SERVICE_ACCOUNT_JSON` | Android release | A Google Play Console service account with release rights |
| `APP_STORE_CONNECT_API_KEY_ID`, `APP_STORE_CONNECT_API_ISSUER_ID`, `APP_STORE_CONNECT_API_KEY_P8` | iOS release | App Store Connect → Users and Access → Keys |
| `MATCH_GIT_URL`, `MATCH_PASSWORD` | iOS signing | A private repo for fastlane match certificates |

## Release flow (Phase 7)

1. Merge to `main`. `build.yml` produces artifacts that you test by hand.
2. Bump the version, then tag: `git tag v0.1.0 && git push origin v0.1.0`.
3. `release.yml` uploads the builds to the internal and TestFlight tracks.
4. Promote to production from the store consoles by hand.

## Still to add when each phase arrives

| Item | Phase |
|---|---|
| `fastlane/Fastfile` with the `android internal` and `ios beta` lanes, plus a `Gemfile` | 7 |
| `server/Dockerfile` for the headless server | 2 |
| `Emberwright.Editor.E2EBuild.BuildAndroidWithAltTester` and `Tests/E2E/run-android.sh` | 2 |

Each item is a ticket on the board.
