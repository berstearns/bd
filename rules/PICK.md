# rules/PICK.md

Move an item from `Ideas`/`Milestones`/`Features`/`Todos` into `Doing`.

## Inputs

- `<slug>` — project file name
- `<id>` — the 8-char hex id of the item to pick

## Procedure

1. Open `projects/<slug>.md`.
2. Locate the line whose id is `[<id>]`. Verify it is under one of the four source
   stacks (Ideas/Milestones/Features/Todos), not already under `Doing`/`Done`.
3. Remove that line from its stack.
4. Insert it at the top of `## Doing` (immediately after the heading).
5. Append ` (picked: YYYY-MM-DD)` to the line. Keep the original `(added: ...)`.

## Constraints

- Only `projects/<slug>.md` may change.
- The item text, note, and id must be preserved byte-for-byte apart from the added
  `(picked: ...)` suffix.
- No other items move. No stacks reorder.
- Only one item picked per PR.

## Commit, branch, PR

Verb: `pick`. Branch: `pick/<slug>/<id>`. Subject:

```
pick(<slug>): <title> [<id>]
```

## Minimal diff example

```diff
 ## Todos
-- [a3f2c1b9] Add reconnect backoff to client — tcp client should back off on ECONNREFUSED (added: 2026-04-22)
 ## Doing
+- [a3f2c1b9] Add reconnect backoff to client — tcp client should back off on ECONNREFUSED (added: 2026-04-22) (picked: 2026-04-23)
```
