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

Invariants T1–T5 are **declared here** and must be enforced by action
rules that touch the subfolder. `rules/REVIEW.md` enforces whatever
checks the action rules define. This file does not itself add reviewer
checks — it defines the shape; the action rules define when to check.

Until action rules are extended to read/write subfolders (tracked as a
separate structural PR), subfolders are write-only: you may create and
hand-edit them, but the `add|pick|done|drop` actions do not yet touch
them. The reviewer does not yet enforce T1–T5 beyond "the six files
exist if the folder exists" (see S5 note on `rules/REVIEW.md`).

## Creating a new project

1. `cp projects/_template.md projects/<slug>.md`.
2. (Optional) `cp -r projects/_template projects/<slug>` to scaffold
   the subfolder with the six stub files.
3. Fill the overview frontmatter (`slug`, `path`, `objective`) and
   the `## Objective` paragraph.
4. Leave section files empty until an item warrants expansion.

## Minimal example

Overview `projects/simple-tcp-comm.md` already contains:

```
## Todos

- [c94d0a79] Add stale job reaper to server — jobs stuck in running after worker failure (added: 2026-04-22)
```

A matching expansion `projects/simple-tcp-comm/todos.md` could read:

```markdown
# simple-tcp-comm — todos

## [c94d0a79] Add stale job reaper to server

**Problem.** A worker picks a job (state → `running`), then crashes
before sending an ack. The job stays in `running` forever.

**Proposed approach.** A background loop on the server scans for jobs
whose `picked_at` is older than a threshold and resets them to
`pending`. Threshold must coordinate with the heartbeat feature
`[4e8a1c63]` to avoid double recovery.

**Open questions.**
- Threshold = heartbeat interval × N? What N?
- Should the reaper log which worker held the reclaimed job?
```

The overview line is untouched; the expansion only adds depth.
