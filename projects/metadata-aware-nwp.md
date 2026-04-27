---
slug: metadata-aware-nwp
path: /home/b/p/writings/sketches/metadata-aware-nwp-in-sla
github: https://github.com/berstearns/public-metadata-aware-nwp-in-sla
gdrive_deploy: i:/_rk/metadata-aware-nwp-in-sla/deploy
gdrive_resources: i:/_rk/metadata-aware-nwp-in-sla/resources
objective: Metadata-aware SDPA gate for NWP on L2 text, gated by (CEFR, L1), with cross-corpus zero-shot transfer eval
status: active
created: 2026-04-27
---

# metadata-aware-nwp

## Objective

Replace Qwen's SDPA-output gate `Y' = Y ⊙ σ(X W_θ)` with a metadata-aware
gate `Y' = Y ⊙ σ([X; e_CEFR; e_L1] W_θ)` so an L2 learner LM is controllable
by explicit (CEFR, L1) inputs rather than the corpus-average implicit profile
a continually-trained learner LM picks up. EFCAMDAT trains; andrew100k,
CELVA-SP, and KUPA-KEYS are zero-shot transfer probes.

## Context

- LaTeX paper sketch + experiment plans + reference codebase.
- Research summary: `resesarch-summary.md` (RQs, method, baselines, metrics).
- Codebase: `codebase/` — `src/`, `scripts/`, `configs/`, `tests/`, `Makefile`.
- Experiment variants: `experiments-ideas/v01–v10` (one factor ablated per file;
  `v01-flagship-sdpa-cefr-l1.md` is the headline run).
- Sections: `sections/01-introduction.tex` … `09-acknowledgments.tex`.
- Baselines (matched data exposure, GPT-2 Base): B0 native, B1 learner LM,
  B2 learner LM + prompt metadata, G1 (ours) learner LM + metadata-aware gate.

## Ideas

## Milestones

## Features

## Todos

- [d70f7803] Make eval scripts accept models for online prediction or offline JSONL — same harness, two input modes (added: 2026-04-27)
- [c69e5602] Run eval scripts with prediction files from no-training benchmark models — B0 native GPT-2 first (added: 2026-04-27)

## Doing

## Done

- [b58d3401] Adapt prediction scripts to emit JSONL compatible with every eval script — one schema across B0/B1/B2/G1 (added: 2026-04-27) (picked: 2026-04-27) (done: 2026-04-27)
- [a47c12fd] Set up standalone eval scripts, one per evaluation table in the paper — stratified PPL, cloze, transfer (added: 2026-04-27) (picked: 2026-04-27) (done: 2026-04-27)
- [7e9a1b3f] Assign GitHub repo to project — create remote, push initial commit, link in frontmatter (added: 2026-04-27) (picked: 2026-04-27) (done: 2026-04-27)
