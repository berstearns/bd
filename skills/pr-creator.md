# skills/pr-creator.md

Role playbook for an agent whose job is to open a single valid PR against this repo.
This skill is authoritative for sequencing; the `rules/` files are authoritative for
format. If they disagree, the rule file wins.

## When to use

The user (human or another agent) asks you to:

- add an idea/milestone/feature/todo to a project
- pick an existing item to work on
- mark a `Doing` item as done
- drop an item without doing it

If the request implies two actions (e.g. "add this and start it"), refuse and split
into two sequential PRs.

## Step 0 — Preflight

1. Confirm you are inside a clone of this repo (`ls AGENTS.md rules/PR.md` succeeds).
2. `git status` — working tree clean. If not, stop and ask the user.
3. `git checkout main && git pull`.
4. `gh auth status` — must be authenticated.

## Step 1 — Resolve intent

Extract and fix, from the conversation:

| Field          | Required for      | How to obtain                                                |
|----------------|-------------------|--------------------------------------------------------------|
| action         | all               | Map user intent → `add` \| `pick` \| `done` \| `drop`        |
| slug           | all               | Must match an existing `projects/<slug>.md`                  |
| stack          | add               | One of `Ideas`, `Milestones`, `Features`, `Todos`            |
| title          | add               | Imperative, <80 chars, no trailing punctuation               |
| note           | add (optional)    | One line. Omit the em-dash segment entirely if absent.       |
| id             | pick, done, drop  | Read from `projects/<slug>.md`; must exist in expected stack |
| reason         | drop              | One short line, goes into commit body and PR body            |

Anything missing → ask the user. **Do not guess.** Do not fabricate an id.

## Step 2 — Read the rules

Read in this order:
1. `rules/PR.md` — the 9 data-structure rules and the PR body schema.
2. The matching action rule: `rules/ADD.md` | `rules/PICK.md` | `rules/DONE.md` | `rules/DROP.md`.

## Step 3 — Prepare the id (add only)

Generate an 8-char lowercase hex string. Verify global uniqueness:

```
git grep "\[<id>\]"
```

Empty output → proceed. Any hit → regenerate.

For `pick`/`done`/`drop`, **verify** the id and its current stack:

```
grep -n "\[<id>\]" projects/<slug>.md
```

The match must be under the stack the action expects (`Doing` for `done`, any of the
four sources for `pick`, any non-`Done` stack for `drop`).

## Step 4 — Edit

Make the **minimal** diff prescribed by the action rule file. Specifically:

- Do not touch frontmatter.
- Do not rename, add, remove, or reorder section headings.
- Do not modify any line other than the one the action targets.
- Do not touch any file other than `projects/<slug>.md`, except for `done`
  which also appends **one** line to `archive/done.md`.
- Do not add or remove blank lines around the edit.

After editing, run `git diff` and compare against the "Minimal diff example" in the
matching action rule. If it doesn't match, fix until it does.

## Step 5 — Branch, commit, push

```bash
git switch -c <verb>/<slug>/<id>
git add -A
git commit -m "<verb>(<slug>): <title> [<id>]"   # for drop, add body: Reason: <reason>
git push -u origin <verb>/<slug>/<id>
```

Never `--amend` after pushing. Never `--no-verify`.

## Step 6 — Open the PR

```bash
gh pr create \
  --title "<verb>(<slug>): <title> [<id>]" \
  --body "$(cat <<'EOF'
Action: <verb>
Project: <slug>
Id: <id>
Title: <title>
Rules: confirmed 1-9 per rules/PR.md

<one-sentence rationale, or "Reason: <reason>" for drop>
EOF
)"
```

The `Rules:` line is a literal attestation that all 9 data-structure rules from
`rules/PR.md` hold. Re-verify each rule against your diff before submitting:

1. One project file (+ archive iff done)
2. Frontmatter untouched
3. Six sections present, names and order unchanged
4. Inserted line immediately after its section heading
5. Item line format matches; id is 8-hex and globally unique
6. Only suffix text was added on lifecycle transitions; pre-suffix bytes identical
7. Title verb, branch verb, and `Action:` agree; only one action
8. File-touch invariants hold
9. No edits to `rules/`, `skills/`, `README.md`, `AGENTS.md`, templates, or other projects

If any rule fails, go back to Step 4.

## Step 7 — Report & stop

Output to the user:

- PR URL (from `gh pr create` stdout)
- One-sentence summary: `<verb> <title> [<id>] in <slug>`

**Stop.** Do not self-merge. Do not approve. The reviewer agent handles merging
(`skills/pr-reviewer.md` when it exists; otherwise a human).

## Hard stops

Stop and ask the user rather than proceed if any of these are true:

- Two or more actions requested in one breath.
- Slug does not match an existing `projects/<slug>.md`.
- `add` without a title, or with a title ≥80 chars, or with trailing punctuation.
- `pick`/`done`/`drop` without an id, or id not found in the expected stack.
- Working tree dirty, or already on a non-`main` branch.
- Id collision on `add` after 3 regeneration attempts (something is wrong).
- The final diff touches any file outside the action's invariant.
