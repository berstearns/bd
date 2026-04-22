# rules/STRUCTURAL.md — structural change protocol

**Audience:** the human (or a human-authorized agent) changing the *shape*
of this repo — rules, templates, folder layout, review protocol, AGENTS.md.

Action PRs (`add|pick|done|drop`) are format-regular and mechanically reviewable
under `rules/PR.md`. Structural PRs are different: they change what
"format-regular" means. Because they alter the rules that every future action
PR is measured against, they are held to a higher bar — and they require a
human merge.

The reviewer agent **MUST NOT** merge a structural PR. It may post a review
comment (PASS or FAIL with reasons), but the merge click is a human decision.

## What counts as structural

A change is structural if, under `rules/PR.md` rule 9, an action PR would be
**rejected as out-of-scope**. Examples:

- Editing `rules/*.md`, `README.md`, `AGENTS.md`, `skills/*`, or any template
  (`projects/_template.md`, `projects/_template/*`).
- Adding or removing files under `projects/<slug>/` (per-project subfolders).
- Adding a new section to the overview template (rule 3 would forbid this in
  an action).
- Introducing a new action verb, a new PR body field, or a new invariant.

If you're unsure whether a change is structural, it is. Default to this rule.

## Rigorous criteria (all must pass)

The PR is measured against S1–S8. Any `FAIL` blocks merge.

### S1 — Motivation is written down

The PR body has a `## Motivation` section: one paragraph answering *what
concrete deficit in the current shape does this solve?* Not "improve X" —
name the deficit.

### S2 — Scope is narrow

The PR changes exactly one *kind* of thing: one new rule, one template
extension, one folder-layout invariant. Do not bundle "new rule + migrate all
projects + update README" in one PR. Prefer sequenced small structural PRs.

A bootstrap PR that introduces a new protocol MAY also apply it to its first
target in the same PR, but must justify the bundling in `## Motivation`.

### S3 — Backward compatibility is explicit

The PR has a `## Compatibility` section listing each existing file or action
and whether it is:

- `unchanged` — byte-identical to `main`.
- `extended` — old form still valid, new form also accepted.
- `breaking` — old form now rejected; `## Migration` section required.

### S4 — Invariants are machine-checkable

If the change introduces or modifies an invariant, it is written down in a
rule file with machine-checkable phrasing. Use `MUST` / `MUST NOT` / `reject`.
"Should" or "try to" is not an invariant.

### S5 — Review hook updated iff checks change

If the change adds or modifies a check the reviewer must run, `rules/REVIEW.md`
is updated in the same PR. Contrapositive: if `REVIEW.md` is not touched, the
PR MUST NOT introduce a new reviewer check — otherwise it becomes dead text.

### S6 — Examples are provided

Any new file type, line format, or layout element comes with a concrete
minimal example in the rule file. No abstract descriptions alone.

### S7 — No agent self-merge

The PR body contains the literal line `Reviewer: human`. The reviewer agent,
on seeing this line, posts a review comment only — never `--approve`, never
`--merge`.

### S8 — Internal consistency

Every cross-reference resolves. If the rule file references `rules/FOO.md`,
that file exists in the same PR or on `main`. All file paths in the PR body
resolve against the merged tree.

## PR body schema (structural)

```
Kind: structural
Summary: <one-line what-and-why>
Reviewer: human

## Motivation
<one paragraph>

## Scope
- <file path>: <what this change does>
- ...

## Compatibility
- <file or action>: unchanged | extended | breaking
- ...

## Migration
<iff any entry above is `breaking`; otherwise write "N/A">

## Criteria self-check
- [x] S1 Motivation written
- [x] S2 Scope narrow
- [x] S3 Compatibility explicit
- [x] S4 Invariants machine-checkable
- [x] S5 REVIEW.md updated iff checks change
- [x] S6 Examples included
- [x] S7 Reviewer: human line present
- [x] S8 Cross-refs valid
```

## Branch and commit

- Branch: `structural/<short-slug>` (e.g. `structural/per-project-subfolders`).
- Commit subject: `struct: <short title>`. Body optional.
- No `[<id>]` suffix; structural changes are not tied to item ids.

## Review

The reviewer agent reads the PR, runs S1–S8, and posts one comment:

- `STRUCTURAL-REVIEW: PASS` — all criteria met; human may merge.
- `STRUCTURAL-REVIEW: FAIL (Sn, Sm, ...)` — one line per failed criterion.

The reviewer never clicks merge on a structural PR. Only the human does.
