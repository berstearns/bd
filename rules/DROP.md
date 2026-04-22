# rules/DROP.md

Discard an item without doing it. Use when an idea is stale, duplicate, or out of scope.

## Inputs

- `<slug>` — project file name
- `<id>` — 8-char hex id to drop
- `<reason>` — one short line explaining why (goes into the commit body, not the file)

## Procedure

1. Open `projects/<slug>.md`.
2. Find the `[<id>]` line. It may be in any stack **except** `## Done`
   (completed items are not dropped — they're history).
3. Delete the line.

## Constraints

- Only `projects/<slug>.md` changes.
- Only one line is removed.
- Do not move the line to Done. Do not append to `archive/done.md`.
- The `<reason>` must appear in the PR body and commit body.

## Commit, branch, PR

Verb: `drop`. Branch: `drop/<slug>/<id>`. Subject:

```
drop(<slug>): <title> [<id>]
```

Commit body and PR body must contain a single line:

```
Reason: <reason>
```
