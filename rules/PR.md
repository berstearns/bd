# rules/PR.md — author protocol

How an agent opens a valid pull request against this repo.

## Data-structure rules (strict — required)

You must satisfy **every** rule below before you push. The reviewer enforces
them and will reject on any violation. Re-read this list after editing and
before running `gh pr create`.

1. **One project = one file.** Your change touches exactly one
   `projects/<slug>.md` (plus `archive/done.md` iff the action is `done`).
   `<slug>` must match the frontmatter `slug:` of that file.
2. **Project frontmatter is authoritative.** Every project file has
   frontmatter pinning `slug`, `path`, and `objective`. Never edit frontmatter
   in an action PR.
3. **Six sections, fixed names, fixed order.** Each project has
   `## Ideas`, `## Milestones`, `## Features`, `## Todos`, `## Doing`, `## Done`.
   Do not rename, add, remove, or reorder these sections.
4. **Stacks grow at the top.** New or moved items are inserted immediately
   after the section heading, before any existing item.
5. **Item line format is exact.**
   ```
   - [<id>] <title> — <note> (added: YYYY-MM-DD)
   ```
   `<id>` is 8 lowercase hex chars, globally unique across the repo
   (`git grep "\[<id>\]"` must return nothing before `add`). `<title>` is
   imperative, non-empty, <80 chars, no trailing punctuation. The em-dash
   `<note>` segment is optional; omit it entirely if absent.
6. **Lifecycle adds suffixes only.** `pick` appends ` (picked: YYYY-MM-DD)`.
   `done` appends ` (done: YYYY-MM-DD)`. The `[<id>]`, title, and note
   preceding the suffixes must be byte-identical across transitions.
7. **Four actions, one per PR.** `add` | `pick` | `done` | `drop`. Never bundle.
8. **File-touch invariants** (the reviewer checks these exactly):
   | Action | Files touched                                      |
   |--------|----------------------------------------------------|
   | add    | exactly `projects/<slug>.md`                       |
   | pick   | exactly `projects/<slug>.md`                       |
   | done   | exactly `projects/<slug>.md` and `archive/done.md` |
   | drop   | exactly `projects/<slug>.md`                       |
9. **No edits to rules, README, AGENTS.md, templates, or other projects.**
   Those require a human PR and are out of scope for action PRs.

The PR body schema below includes a `Rules:` line where you attest that
all nine rules hold. Lying there is a rejection offence.

## Preconditions

- You are on a freshly-pulled `main`.
- You have decided one action: `add`, `pick`, `done`, or `drop`.
- You have read the matching rule file (`rules/ADD.md` | `PICK.md` | `DONE.md` | `DROP.md`).
- You have re-read the **Data-structure rules** section above.
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
   Rules: confirmed 1-9 per rules/PR.md

   <optional one-paragraph rationale, or "Reason: ..." for drop>
   EOF
   )"
   ```

6. **Stop**. Do not self-merge. Wait for the reviewer agent.

## PR body schema

The body must contain these five fields on their own lines, in this order:

```
Action: add|pick|done|drop
Project: <slug>
Id: <id>
Title: <title>
Rules: confirmed 1-9 per rules/PR.md
```

The reviewer parses these. Missing, misspelled, or out-of-order → **reject**.
The `Rules:` line must read exactly `confirmed 1-9 per rules/PR.md`. Its
presence is your attestation that every Data-structure rule (1 through 9) holds.
The reviewer then verifies each rule independently against the diff — if the
diff contradicts the attestation, the PR is rejected.

## If the reviewer requests changes

- Amend only the branch, re-push. Do not open a second PR for the same item.
- If the reviewer closes the PR, treat it as terminal. Do not reopen unless the human asks.
