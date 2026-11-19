---
name: Issue workflow
description: How Gemma picks, claims, works and hands off GitHub issues. Load for /next-issue, /work-issue, or any issue update.
---
# Issue workflow

## Pick (/next-issue)

```sh
gh issue list --state open --label "status:ready" --label "agent:gemma" \
  --json number,title,labels,body --limit 30
```

Choose the issue with:
1. all dependencies closed (check each "Depends on" number with `gh issue view <n> --json state`),
2. then the lowest difficulty (low before medium; leave high for last),
3. then the lowest number.

## Claim

```sh
gh issue edit <n> --add-label "status:in-progress" --remove-label "status:ready" --add-assignee @me
git switch main && git pull --ff-only
git switch -c feat/<n>-<short-slug>
```

## Work

1. Read the issue. Load the listed skills, plus `project-lessons`.
2. Follow `emberwright-tdd` for each acceptance criterion.
3. Commit after each green step.

## Hand off

1. Run `/ponytail-review` (through `reviewer-local`) and apply the accepted "Must fix" and "Should fix" findings.
2. Run `/verify`.
3. Push and open the PR:

```sh
git push -u origin HEAD
gh pr create --fill --body-file <tmp>   # the body includes "Closes #<n>" and the PR template sections
gh issue edit <n> --add-label "status:needs-review" --remove-label "status:in-progress"
```

## Blocked or escalated

- On an escalation, comment on the issue with the escalation packet and the outcome: `gh issue comment <n> --body-file <tmp>`.
- If a human or Claude must decide something, add `status:blocked` and `agent:claude`, and say exactly what decision is needed.

Never merge PRs. Never close issues by hand. The PR closes them.
