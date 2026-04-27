---
slug: metadata-aware-nwp
path: /home/b/p/writings/sketches/metadata-aware-nwp-in-sla
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

- [3e5d7b48] Wire zero-shot transfer eval over andrew100k, CELVA-SP, KUPA-KEYS (added: 2026-04-27)
- [9c0f6a31] Build (CEFR, L1) cell stratifier for evaluation harness — feeds PPL and cloze tables (added: 2026-04-27)
- [2b4e8d09] Run flagship v01 (B0/B1/B2/G1) on EFCAMDAT — produce stratified PPL by (CEFR, L1) (added: 2026-04-27)
- [7f1a3c52] Implement metadata-aware SDPA gate replacing X with [X;e_CEFR;e_L1] — bias-init so σ≈1 (added: 2026-04-27)

## Doing

## Done
