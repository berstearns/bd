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

Extract these five fields, in order:

```
Action: add|pick|done|drop|run
Project: <slug>
Id: <id>
Title: <title>
Rules: confirmed 1-9 per rules/PR.md
```

Any missing, misordered, or malformed → **reject**. The `Rules:` line must
match the literal string `confirmed 1-9 per rules/PR.md`.

For `run`, six additional fields follow the five above (in this order):
`Target:`, `Branch:`, `Commits:`, `TargetPR:`, `Status:`, `Clean:`.
The `Clean:` line must match `confirmed; target repo was clean at execution start`.
Missing, misordered, or malformed → **reject**. See `rules/RUN.md`.

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
| run     | exactly `projects/<slug>/doing.md`                |

Any additional file touched → **reject**. For `run`, also reject if
`projects/<slug>/doing.md` does not exist on `main` (subfolder not
adopted — a separate structural PR must scaffold it first).

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
- **run**: `+N / -0` in `projects/<slug>/doing.md` where `N >= 7` (blank line
  + `### Execution — YYYY-MM-DD` heading + 6 bullet lines). The added block
  appears immediately after an existing `## [<id>] <title>` expansion block
  (or at the end of a newly-created one). The six bullets MUST contain, in
  order: `**Target:**`, `**Branch:**`, `**Commits:**`, `**TargetPR:**`,
  `**Status:**`, `**Notes:**`. Overview file `projects/<slug>.md` must NOT
  appear in the diff. `Branch:` bullet MUST match `bd/<id>` (literal).
  `Target:` bullet MUST match the frontmatter `path:` or `latest_version_path:`
  of the project. `Status:` MUST be `success`, `partial`, or `aborted`;
  if `aborted`, `**Notes:**` must not be empty.

## Semantic checks

- Item line follows format: `- [<id>] <title>( — <note>)? \(added: ...\)( \(picked: ...\))?( \(done: ...\))?`.
- Date suffixes are in ISO `YYYY-MM-DD` and plausibly recent.
- Title is non-empty and <80 chars.

## Data-structure rules (must all hold)

Verify each of the 9 rules from `rules/PR.md` independently. The `Rules:`
attestation in the PR body is **not** sufficient — the diff must actually
satisfy each one.

| # | Rule                                   | How to check                                                                 |
|---|----------------------------------------|------------------------------------------------------------------------------|
| 1 | One project's scope (per rule 8 table) | File-set check above                                                         |
| 2 | Frontmatter untouched                  | Diff does not touch lines between the two `---` fences                       |
| 3 | Six sections, fixed names & order      | `grep -c '^## ' projects/<slug>.md` returns 6; names/order match template    |
| 4 | Stacks grow at the top                 | Added line appears immediately after the target `##` heading in the diff    |
| 5 | Item line format + unique id           | Regex match; `git grep "\[<id>\]" origin/main` returns nothing for `add`     |
| 6 | Lifecycle suffixes only                | Pre-suffix substring of moved line is byte-identical across the `-`/`+` pair |
| 7 | One action per PR                      | Title verb, branch verb, and `Action:` field all agree                       |
| 8 | File-touch invariants per action       | File-set check above                                                         |
| 9 | No edits to rules/README/AGENTS/templates/other projects | No paths outside the rule-8 file set for the current action appear in the diff |

Any violation → **reject** citing the rule number.

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
