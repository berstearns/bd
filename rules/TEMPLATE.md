# rules/TEMPLATE.md — per-project layout

Each project is represented by two things:

1. **One overview file** — `projects/<slug>.md`. Strict, index-style,
   one line per item across six fixed sections. Format defined by
   `rules/PR.md` rules 1–9. **This file is authoritative**: item ids,
   titles, section membership, and lifecycle state are read from here,
   not from anywhere else.

2. **One optional subfolder** — `projects/<slug>/`. Contains six
   free-form expansion files, one per section. Each expansion file
   holds per-item *depth* (design notes, rationale, links, open
   questions, acceptance criteria) that would bloat the overview.

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

When a project has a subfolder, all six files MUST exist. Missing files
are a reject. Empty files are fine — a file may contain only its header
line (see below).

Per-project subfolders are **opt-in**: a project may exist with only
the overview file. Once the subfolder is created, the six-file invariant
applies.

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

## Enforcement scope

Invariants T1–T5 are **declared here** but **no reviewer check is
activated by this file**. They are future-facing: action rules that
are later extended to read/write the subfolder will carry the
corresponding checks into `rules/REVIEW.md` at that time (in a
separate structural PR).

Until then, subfolders are hand-edited only. The existing action rules
(`add|pick|done|drop`) do not touch them, and the existing file-set
check in `rules/REVIEW.md` ("Any additional file touched → reject")
already prevents an action PR from reaching into the subfolder — so
no new reviewer hook is needed yet.

## Creating a new project

1. `cp projects/_template.md projects/<slug>.md`.
2. (Optional) `cp -r projects/_template projects/<slug>` to scaffold
   the subfolder with the six stub files.
3. Fill the overview frontmatter (`slug`, `path`, `objective`) and
   the `## Objective` paragraph.
4. Leave section files empty until an item warrants expansion.

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
