# rules/DONE.md

Close an item currently in `## Doing`.

## Inputs

- `<slug>` — project file name
- `<id>` — 8-char hex id of the item in `Doing`

## Procedure

1. Open `projects/<slug>.md`.
2. Find the `[<id>]` line under `## Doing`. If not there, stop.
3. Remove the line from `Doing`.
4. Append it at the top of `## Done` with ` (done: YYYY-MM-DD)` added.
5. Append the same resulting line to `archive/done.md`, prefixed by the slug:
   ```
   <slug> — - [<id>] <title> — <note> (added: ...) (picked: ...) (done: YYYY-MM-DD)
   ```
   (If there was no note, the em-dash/note segment is absent — preserve whatever the line actually was.)

## Constraints

- Exactly two files change: `projects/<slug>.md` and `archive/done.md`.
- Exactly one line moves in the project file. Exactly one line is appended to `archive/done.md`.
- Do not alter previously-closed items.

## Commit, branch, PR

Verb: `done`. Branch: `done/<slug>/<id>`. Subject:

```
done(<slug>): <title> [<id>]
```

## Minimal diff example

```diff
 ## Doing
-- [a3f2c1b9] Add reconnect backoff to client — ... (added: 2026-04-22) (picked: 2026-04-23)
 ## Done
+- [a3f2c1b9] Add reconnect backoff to client — ... (added: 2026-04-22) (picked: 2026-04-23) (done: 2026-04-24)
```

```diff
--- a/archive/done.md
+++ b/archive/done.md
@@
+simple-tcp-comm — - [a3f2c1b9] Add reconnect backoff to client — ... (added: 2026-04-22) (picked: 2026-04-23) (done: 2026-04-24)
```
