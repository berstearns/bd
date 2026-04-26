# rules/TEMPLATE.md — per-project layout

Each project is represented by two things:

1. **One overview file** — `projects/<slug>.md`. Strict, index-style,
   one line per item across six fixed sections. Format defined by
   `rules/PR.md` rules 1–9. **This file is authoritative**: item ids,
   titles, section membership, and lifecycle state are read from here,
   not from anywhere else.

2. **One mandatory subfolder** — `projects/<slug>/`. Contains six
   free-form expansion files, one per section. Each expansion file
   holds per-item *depth* (design notes, rationale, links, open
   questions, acceptance criteria) that would bloat the overview.
   The subfolder MUST exist for every project; see invariant T6.

The overview is a dashboard; the subfolder is a detail drawer. The
overview is format-strict and agent-written; the subfolder is
free-form and human-friendly.

## Overview file

Unchanged. See `projects/_template.md` and `rules/PR.md`.

## Subfolder layout

```
projects/<slug>/
├── ideas.md
├── milestones.md
├── features.md
├── todos.md
├── doing.md
└── done.md
```

Every project MUST have its subfolder. All six files MUST exist.
Missing subfolder or missing file is a reject. Empty files are fine —
a file may contain only its header line (see below).

## Expansion file format

Each expansion file is a markdown document. The first line is the
header, naming the slug and section it belongs to:

```markdown
# <slug> — <section>
```

Below the header, zero or more **expansion blocks**. Each block:

```markdown
## [<id>] <title>

<free-form markdown: rationale, design notes, links, acceptance criteria,
open questions, whatever helps a reader understand this item in depth>
```

- `<id>` is the 8-char hex id from the overview item.
- `<title>` is byte-identical to the overview title (without lifecycle
  suffixes like `(added: ...)` / `(picked: ...)` / `(done: ...)`).
- Blocks are ordered top-to-bottom to mirror the overview order in
  that section (stacks grow at the top — newest first).

## Invariants

**T1 — Overview is authoritative.** If the overview and subfolder
disagree about an item's existence, title, or section, the overview
wins. Reconciliation edits the subfolder, never the overview.

**T2 — No orphan expansions.** Every expansion block
`## [<id>] <title>` in `projects/<slug>/<section>.md` MUST correspond
to an overview line `- [<id>] <title> …` under the matching
`## <Section>` heading in `projects/<slug>.md`. A block referring to
an id not present in the overview is a reject.

**T3 — Title equality.** The expansion heading title MUST be
byte-identical to the overview title (excluding the `(added: ...)` /
`(picked: ...)` / `(done: ...)` suffixes).

**T4 — Expansions are optional.** An overview item MAY have no
expansion. Expansions are opt-in depth, not a required mirror.

**T5 — Section match.** An expansion block lives only in the file
matching its item's *current* overview section. When lifecycle moves
the item (e.g. `pick` moves Todos → Doing), the expansion migrates
with it. See the action rules for how each action handles this.

**T6 — Subfolder is mandatory.** For every `projects/<slug>.md` that
exists on `main`, the directory `projects/<slug>/` MUST also exist on
`main` and MUST contain all six expansion files: `ideas.md`,
`milestones.md`, `features.md`, `todos.md`, `doing.md`, `done.md`.
A project file without its subfolder, or a subfolder missing any of
the six files, is a reject. Files may be empty beyond the header line
per T4.

## Enforcement scope

Invariant T6 is **machine-checked** by `rules/REVIEW.md`: every PR that
references a slug (action PRs, or structural PRs adding/touching a
project) must verify that `projects/<slug>/` exists with all six files
on the merged tree. T1–T5 remain declared here but not yet
machine-checked: action rules that are later extended to read/write
the subfolder will carry the corresponding checks into `rules/REVIEW.md`
at that time (in a separate structural PR).

## Creating a new project

1. `cp projects/_template.md projects/<slug>.md`.
2. `cp -r projects/_template projects/<slug>` to scaffold the subfolder
   with the six stub files. **Required** — invariant T6.
3. In each `projects/<slug>/*.md` stub, replace the literal `<slug>`
   placeholder in the header line with the actual slug.
4. Fill the overview frontmatter (`slug`, `path`, `objective`) and
   the `## Objective` paragraph.
5. Leave section files empty until an item warrants expansion.

## Minimal example

Overview `projects/simple-tcp-comm.md` contains on `main`:

```
## Todos

- [a3f2c1b9] Add reconnect backoff to client — client should back off on ECONNREFUSED instead of tight-looping (added: 2026-04-22)
```

A matching expansion `projects/simple-tcp-comm/todos.md` could read:

```markdown
# simple-tcp-comm — todos

## [a3f2c1b9] Add reconnect backoff to client

**Problem.** The client currently retries `connect()` in a tight loop
when the server is down, burning CPU and log volume for no useful
signal.

**Proposed approach.** Exponential backoff with a small jitter
component, capped at ~30s. Reset the delay on the first successful
connect.

**Open questions.**
- Base delay and cap values?
- Should we log every retry or only attempts beyond the cap?
```

The overview line is untouched; the expansion only adds depth.
