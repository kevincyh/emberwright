---
name: Emberwright verify
description: Pre-PR verification - run every check, prove the change works, and find what else it could break. Load before opening a PR or when asked to verify.
---
# Emberwright verify

Adapted from pstack `prove-it-works` and `blast-radius` (MIT, Lauren Tan).

## 1. Run the checks (paste the real output)

```sh
dotnet format Tests/Emberwright.Core.sln --verify-no-changes
dotnet test Tests/Emberwright.Core.sln
git diff --stat main...HEAD
```

If Unity is installed and `Net`, `Server`, `Client` or `Data` changed, also run the Unity tests (see the `unity-test-framework` skill).

## 2. Checklist

- [ ] Each acceptance criterion in the issue has a named test, and the test passes.
- [ ] Only files in the ticket's scope changed, or the PR explains why.
- [ ] Game rules are in Core. Behaviours only call Core.
- [ ] No client-trusted values. Grants take a request ID.
- [ ] No `UnityEngine`, `DateTime.Now` or unseeded random in Core.
- [ ] No new interface with one implementation, no dead code, no duplicate helper.
- [ ] Docs, the lesson index and the usage log are updated if needed.

## 3. Blast radius (for any change to a public method or type)

1. List what changed: signatures, return values and behaviour.
2. Find every caller: `grep -rn "<MethodName>" Assets Tests`.
3. Name the **one fact** the change is safe because of. Example: "CostFor is only called by UpgradeService, and that caller already passes level ≥ 1."
4. Prove that fact with a test or a command, not with words. If you cannot prove it, write "unproven" in the PR.

## 4. Reply

```
Checks: format ✅/❌, tests N passed / M failed
Checklist: all ✅, or the failing items
Blast radius: <the safety fact>, proven by <test or command>, or "unproven"
Verdict: ready for PR / fix first: <items>
```
