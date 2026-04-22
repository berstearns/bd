# rules/RUN.md — execute an item against a target git repo

The `run` action records one execution attempt against a picked item.
Unlike the four metadata actions (`add|pick|done|drop`), `run` produces
artifacts **outside** the bd repo — branches and commits in a target
git repo. The bd PR is the audit trail of what happened; the actual
code change lives in the target repo.

## Preconditions (reject if any is false)

1. `projects/<slug>.md` exists.
2. The project has adopted the subfolder layout per `rules/TEMPLATE.md`:
   `projects/<slug>/doing.md` MUST exist. If it does not, a separate
   structural PR must scaffold the subfolder first (`cp -r projects/_template projects/<slug>`).
3. `<id>` appears on exactly one line in `projects/<slug>.md` under
   `## Doing`. The item MUST have been picked first (`rules/PICK.md`).
4. The project frontmatter has a `path:` field resolving to an
   existing directory that is a git repository (contains `.git`).
   If the frontmatter has `latest_version_path:`, that path takes
   precedence.
5. The target git repo was clean (`git status --porcelain` empty) at
   the start of execution. The PR body attests this via the `Clean:`
   line.

## Inputs

- `<slug>` — the bd project slug.
- `<id>` — 8-char hex id of the item in `## Doing`.
- `<target-repo>` — absolute path to the target git repo. MUST match
  the resolved `path:` / `latest_version_path:` from frontmatter.
- `<status>` — one of `success`, `partial`, `aborted`.

## Procedure

### Part A — in the target git repo

1. Verify `git status --porcelain` is empty.
2. `git switch -c bd/<id>` — the branch name is literally `bd/` + the
   8-char id. No other prefix or suffix permitted. This is what lets
   the bd record trace to the target repo deterministically.
3. Implement the work described by the overview item line in
   `projects/<slug>.md` plus any expansion block for `[<id>]` in
   `projects/<slug>/doing.md` (or, if the expansion still lives in the
   source section because T5 migration has not yet been wired into
   action rules, wherever it currently is).
4. Commit on `bd/<id>` with subject:

   ```
   bd(<slug>/<id>): <short description>
   ```

   Body optional. Multiple commits on the same branch are allowed.
   Never `--amend` after pushing. Never `--no-verify`.
5. Optionally `git push` and open a PR in the target repo. Record its
   URL for the bd PR body.

### Part B — in the bd repo

1. `git switch main && git pull --ff-only`.
2. `git switch -c run/<slug>/<id>`.
3. Open `projects/<slug>/doing.md`. Locate the expansion block
   `## [<id>] <title>`. If no expansion exists yet, create it first
   (heading + any minimal context), then proceed.
4. Append an **Execution subsection** at the end of the block,
   preceded by a single blank line:

   ```markdown
   ### Execution — YYYY-MM-DD

   - **Target:** <absolute path>
   - **Branch:** bd/<id>
   - **Commits:** <sha1>[, <sha2>, ...]
   - **TargetPR:** <url-or-"none">
   - **Status:** success | partial | aborted
   - **Notes:** <one paragraph summary of what was done and what remains>
   ```

5. Commit, push, open the PR (see `rules/PR.md`).

## Constraints

- **Only `projects/<slug>/doing.md` changes in bd.** Exactly one file.
- **No changes to `projects/<slug>.md`** (the overview). `run` does
  not move the item. A separate `done` or `drop` PR closes it.
- **Exactly one Execution subsection is appended per `run` PR.** Never
  edit a previously-committed Execution subsection. Re-running appends
  a new subsection below the old one.
- **Branch name in the target repo MUST be `bd/<id>`** exactly.
- **If `<status>` is `aborted`, the `Notes:` field MUST explain why.**

## PR body schema

The body MUST contain these fields, on their own lines, in this order:

```
Action: run
Project: <slug>
Id: <id>
Title: <title>
Rules: confirmed 1-9 per rules/PR.md

Target: <absolute path>
Branch: bd/<id>
Commits: <sha1>[, <sha2>, ...]
TargetPR: <url-or-"none">
Status: success | partial | aborted
Clean: confirmed; target repo was clean at execution start
```

Missing, misordered, or malformed fields → **reject**. The `Rules:`
and `Clean:` lines MUST match their literal strings exactly.

## Commit, branch, PR (bd side)

- Branch: `run/<slug>/<id>`.
- Commit subject:

  ```
  run(<slug>): <title> [<id>]
  ```

- Do not self-merge. Wait for the reviewer.

## Minimal diff example (bd side)

```diff
--- a/projects/app7/doing.md
+++ b/projects/app7/doing.md
@@
 ## [61b2fb37] Persist last-read page per comic across app resets

 (existing expansion content, if any)
+
+### Execution — 2026-04-23
+
+- **Target:** /home/b/p/minimal-android-apps/app7-remove-content-button-on-catalog_20260421_201451
+- **Branch:** bd/61b2fb37
+- **Commits:** 8f3c1204
+- **TargetPR:** none
+- **Status:** success
+- **Notes:** Added `last_page` field to catalog store; persisted on page change; read on comic open. Verified on desktop build; Android build smoke-tested only.
```

## Trust model

The bd reviewer cannot verify that the target branch or commits
actually exist — the reviewer only sees the bd diff. The Execution
subsection is **attestation, not proof**. Lying in it is a serious
offence: a later reader who cannot find `bd/<id>` in the target repo
may reject, revert, or escalate.

The bd record is append-only. Once an Execution subsection is merged
to `main`, it is never edited. Corrections are a new subsection below.
