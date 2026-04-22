# AGENTS.md — entrypoint for LLM agents

You are an agent interacting with a brain-dump repo. Read this file first.
Never invent rules. If a rule is unclear, stop and ask the human.

## Before anything

1. `git pull` (or clone fresh).
2. List `projects/*.md` to know what exists.
3. Decide the single action you will perform. See actions below.
4. Read the matching rule file **in full** before editing.
5. Follow `rules/PR.md` to open a pull request.

## Skills

`skills/` contains role playbooks — sequencing guides for specific agent jobs.
If a skill matches your role, read it in full before step 3 above. It tells you
*how* to perform the work; `rules/` tells you *what format* the result must take.

| Skill                     | Use when                                                 |
|---------------------------|----------------------------------------------------------|
| `skills/pr-creator.md`    | You are opening a PR to add/pick/done/drop an item       |

## The five actions

| Action  | Rule             | What it does                                              |
|---------|------------------|-----------------------------------------------------------|
| `add`   | `rules/ADD.md`   | Push an item onto a stack (Ideas/Milestones/Features/Todos) |
| `pick`  | `rules/PICK.md`  | Move an item from a stack to `Doing`                      |
| `done`  | `rules/DONE.md`  | Close a `Doing` item; append to `archive/done.md`         |
| `drop`  | `rules/DROP.md`  | Delete an item without doing it                           |
| `run`   | `rules/RUN.md`   | Record one execution of a `Doing` item against a target git repo |

**One action per pull request. No exceptions.**

## Hard invariants

These hold for every PR. The reviewer will reject anything violating them.

1. **Exactly one project's scope is touched.** The file-touch invariant per
   action is defined in `rules/PR.md` rule 8. In short: `add`/`pick`/`drop`
   touch only `projects/<slug>.md`; `done` touches `projects/<slug>.md` +
   `archive/done.md`; `run` touches only `projects/<slug>/doing.md`.
   No other files change.
2. **The change matches exactly one action pattern** (see rules).
3. **Every item has a non-empty title and an 8-char lowercase hex id** (e.g. `a3f2c1b9`).
4. **Ids are unique across the whole repo**. Before adding, `grep -r '\[<id>\]' .` must return nothing.
5. **Stacks grow at the top**. Newest item is the first line after the section heading.
6. **No silent edits**. Don't rename, rephrase, or reformat existing items. Don't touch unrelated sections.
7. **No new files outside what the rule prescribes**. No README edits, no templates, no reflows.
8. **Commit subject, branch name, and PR title follow `rules/PR.md` exactly.**
9. **If the project file has no `<slug>.md`, the action is invalid** — ask the human to create the project first.
10. **Never fabricate project context**. If you don't know what the project is, read its frontmatter `path:` and inspect the codebase; if still unclear, stop.

## Item line format

```
- [<id>] <title> — <one-line note> (added: YYYY-MM-DD)
```

- `<id>`: 8 lowercase hex chars, square brackets. Generate with a PRNG.
- `<title>`: imperative, <80 chars, no trailing punctuation.
- `<one-line note>`: optional context. If omitted, drop the em dash.
- `(added: YYYY-MM-DD)`: mandatory for new items. UTC date.

When moved to Doing, append ` (picked: YYYY-MM-DD)`.
When moved to Done, append ` (done: YYYY-MM-DD)`.

## Picking what to work on

If the human says "pick something from project X":

1. Read `projects/<slug>.md`.
2. Consider the `objective:` in the frontmatter.
3. Prefer stacks in this priority order: **Todos > Features > Milestones > Ideas**.
4. Within a stack, prefer the top (newest/freshest) unless the human says otherwise.
5. Open a `pick` PR (see `rules/PICK.md`).

## What you must never do

- Open a PR that bundles `add` + `pick`, or any other combination.
- Edit `archive/done.md` except by appending one line as part of a `done` action.
- Rewrite the rules, README, or AGENTS.md. Those are human-owned.
- Squash-merge your own PRs. Only the reviewer agent merges.
- Push to `main` directly.
