---
slug: app7
path: /home/b/p/minimal-android-apps/app7-ideas-for-later
latest_version_path: /home/b/p/minimal-android-apps/app7-remove-content-button-on-catalog_20260421_201451
objective: Minimal multi-platform (Android/desktop/iOS) reader app with annotation bar and OCR enrichment
status: active
created: 2026-04-22
---

# app7

## Objective

Kotlin Multiplatform reader app. Features an annotations bar, image-fit/expand
modes, rotation handling, and OCR-driven enrichment of page content.
The `app7-ideas-for-later/` directory is a staging ground for feature specs
that haven't yet been branched into a dated working tree.

## Context

- Ideas staging: `app7-ideas-for-later/*.md`.
- Active worktree (latest): `app7-remove-content-button-on-catalog_20260421_201451/`.
- Key specs in the active worktree: `FEATURE_SPEC.md`, `OCR_ENRICHMENT_INSTRUCTIONS.md`, `RESUME.md`.
- Convention: one working branch per feature, directory suffixed with `_YYYYMMDD_HHMMSS`.

## Ideas

- [6f2d9a4b] Annotations-bar position strategy — decide top vs. bottom per device/orientation (added: 2026-04-22)
- [2a8c5e71] Expand image-fit to maximize reading area — spec in ideas-for-later (added: 2026-04-22)
- [8b3e1d47] Rotation functionality for wider width — spec in ideas-for-later (added: 2026-04-22)

## Milestones

- [31f8c2a6] Ship the remove-content-button-on-catalog feature from the current worktree (added: 2026-04-22)

## Features

- [9cc01644] Add cloud sync every X seconds with off-by-default toggle — X defaults to 10s; feature gated by a boolean flag, off by default (added: 2026-04-22)
- [d70e4519] Remove the content button on the catalog view — spec in ideas-for-later (added: 2026-04-22)

## Todos

- [61b2fb37] Persist last-read page per comic across app resets — urgent bugfix; resume currently lands on page 1 every time (added: 2026-04-22)
- [f94a2c0e] Consolidate bugs/ notes from app7-ideas-for-later into trackable items here (added: 2026-04-22)

## Doing

## Done
