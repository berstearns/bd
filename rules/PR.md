# rules/PR.md — author protocol

How an agent opens a valid pull request against this repo.

## Preconditions

- You are on a freshly-pulled `main`.
- You have decided one action: `add`, `pick`, `done`, or `drop`.
- You have read the matching rule file.
- `gh` is authenticated on the host.

## Steps

1. **Branch**
   ```
   git switch -c <verb>/<slug>/<id>
   ```

2. **Edit** — exactly the change the rule prescribes. Nothing else.
   Run `git diff` and verify the diff matches the "Minimal diff example" in the rule.

3. **Commit** — subject line exactly:
   ```
   <verb>(<slug>): <title> [<id>]
   ```
   For `drop`, include `Reason: <reason>` in the body.
   Never use `--amend` after pushing. Never use `--no-verify`.

4. **Push**
   ```
   git push -u origin <verb>/<slug>/<id>
   ```

5. **Open PR** with `gh`:
   ```
   gh pr create \
     --title "<verb>(<slug>): <title> [<id>]" \
     --body "$(cat <<'EOF'
   Action: <verb>
   Project: <slug>
   Id: <id>
   Title: <title>

   <optional one-paragraph rationale, or "Reason: ..." for drop>
   EOF
   )"
   ```

6. **Stop**. Do not self-merge. Wait for the reviewer agent.

## PR body schema

The body must contain these four fields on their own lines, in this order:

```
Action: add|pick|done|drop
Project: <slug>
Id: <id>
Title: <title>
```

The reviewer parses these. If any is missing or wrong, the PR is rejected.

## If the reviewer requests changes

- Amend only the branch, re-push. Do not open a second PR for the same item.
- If the reviewer closes the PR, treat it as terminal. Do not reopen unless the human asks.
