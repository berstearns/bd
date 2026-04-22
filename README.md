# bd — brain-dump

Plain-text, git-native database for solo-dev project ideas, milestones, features, and todos.
One project = one markdown file. One item = one line in a stack.
Every mutation is a pull request. Agents author; another agent reviews and merges.

## Layout

```
bd/
├── README.md            human overview (this file)
├── AGENTS.md            entrypoint for any LLM — read first
├── rules/
│   ├── ADD.md           add an item to a stack
│   ├── PICK.md          move an item from a stack to Doing
│   ├── DONE.md          close a Doing item
│   ├── DROP.md          discard an item
│   ├── PR.md            pull-request protocol (author)
│   └── REVIEW.md        review protocol (reviewer)
├── skills/
│   └── pr-creator.md    role playbook for an agent opening a PR
├── projects/
│   ├── _template.md     copy for a new project
│   └── <slug>.md        one file per project
└── archive/
    └── done.md          append-only closed-item log
```

## Item lifecycle

```
Ideas ─┐
Milestones ─┤
Features   ─┼──► Doing ──► Done ──► archive/done.md
Todos      ─┘        └──► dropped (deleted, recorded in commit)
```

## Quick reference

- Add `cat projects/<slug>.md` to read a project's current state.
- New project: copy `projects/_template.md`, fill the frontmatter.
- Point any agent at `AGENTS.md`. One action per PR, nothing more.
