# rules/ADD.md

Push a new item onto one of a project's stacks.

## Inputs

- `<slug>` — the project's file name without `.md` (e.g. `simple-tcp-comm`)
- `<stack>` — one of: `Ideas`, `Milestones`, `Features`, `Todos`
- `<title>` — imperative, <80 chars
- `<note>` — optional one-line context

## Procedure

1. Generate an 8-char lowercase hex id. Confirm uniqueness:
   ```
   grep -r "\[<id>\]" . && echo "COLLISION — regenerate" || echo "ok"
   ```
2. Open `projects/<slug>.md`.
3. Find the `## <stack>` heading. Insert the new line **immediately after the heading**
   (before any existing items — stacks grow at the top).
4. The inserted line must match this format exactly:
   ```
   - [<id>] <title> — <note> (added: YYYY-MM-DD)
   ```
   Omit ` — <note>` if there is no note. Use today's UTC date.

## Constraints

- Only `projects/<slug>.md` may change.
- Only one line is added. No blank-line changes. No reordering of existing items.
- If the target `## <stack>` heading does not exist in the project file, stop — do not create it.
- Do not add the same title twice (grep the file first).

## Commit, branch, PR

See `rules/PR.md`. Use verb `add`. Branch: `add/<slug>/<id>`. PR title and commit subject:

```
add(<slug>): <title> [<id>]
```

## Minimal diff example

```diff
 ## Todos
+- [a3f2c1b9] Add reconnect backoff to client — tcp client should back off on ECONNREFUSED (added: 2026-04-22)
 - [older item...]
```
