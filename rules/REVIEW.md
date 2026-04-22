# rules/REVIEW.md — reviewer protocol

You are the reviewer agent. You **never author** PRs; you accept or reject them.
Apply the following checklist to every incoming PR. If any check fails, request
changes or close the PR with a short, specific reason.

## Gather

```
gh pr view <N> --json number,title,headRefName,body,files,additions,deletions
gh pr diff <N>
```

## Parse the PR body

Extract these four fields:

```
Action: add|pick|done|drop
Project: <slug>
Id: <id>
Title: <title>
```

Any missing → **reject**.

## Static checks

1. **Title matches pattern** `^<verb>\(<slug>\): .+ \[<id>\]$` where `<verb>`
   matches the `Action` field and `<id>` matches the `Id` field.
2. **Branch matches** `<verb>/<slug>/<id>`.
3. **Id** is 8 lowercase hex chars.
4. **Slug** corresponds to an existing `projects/<slug>.md`.

## File-set checks

| Action  | Files touched                                     |
|---------|---------------------------------------------------|
| add     | exactly `projects/<slug>.md`                      |
| pick    | exactly `projects/<slug>.md`                      |
| done    | exactly `projects/<slug>.md` and `archive/done.md`|
| drop    | exactly `projects/<slug>.md`                      |

Any additional file touched → **reject**.

## Diff-shape checks (per action)

- **add**: exactly `+1 / -0` line in the project file. The added line is immediately
  after a `## Ideas|Milestones|Features|Todos` heading and matches the item line
  format in `AGENTS.md`. The id does not appear anywhere else in the repo prior to
  this PR (`git grep "\[<id>\]" origin/main` returns nothing).
- **pick**: exactly `+1 / -1` in the project file. Removed line is under one of the
  four source stacks. Added line is immediately after `## Doing`. The text before
  the suffixes is byte-identical. Added line has a new `(picked: YYYY-MM-DD)` suffix.
- **done**: in the project file, `+1 / -1`; removed line was under `## Doing`, added
  line is under `## Done` with `(done: ...)` suffix. In `archive/done.md`, `+1 / -0`,
  appended to end of file, starts with `<slug> — `.
- **drop**: `+0 / -1` in the project file. Removed line was **not** under `## Done`.
  PR body contains a `Reason: ...` line.

## Semantic checks

- Item line follows format: `- [<id>] <title>( — <note>)? \(added: ...\)( \(picked: ...\))?( \(done: ...\))?`.
- Date suffixes are in ISO `YYYY-MM-DD` and plausibly recent.
- Title is non-empty and <80 chars.

## Decide

- All checks pass → `gh pr review <N> --approve` then `gh pr merge <N> --squash --delete-branch`.
- Any fail → `gh pr review <N> --request-changes --body "<specific reason>"` citing
  the failed check. Do not merge. Do not hand-fix the PR.

## Never

- Never merge without running all checks.
- Never merge your own PRs (you don't author any).
- Never rewrite history on `main`.
- Never approve a PR that touches rules, README, AGENTS.md, or templates.
  Those changes require a human.
