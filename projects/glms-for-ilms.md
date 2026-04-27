---
slug: glms-for-ilms
path: /home/b/p/writings/sketches/glms-for-ilms-in-sla
objective: GLM autoregressive blank-infilling for learner cloze, with learner-conditioning to make an Interlanguage LM
status: active
created: 2026-04-27
---

# glms-for-ilms

## Objective

Adapt the GLM (Du et al., 2022) autoregressive blank-infilling objective to
second-language learner text so a model predicts the distribution of fillers
a learner at a specified proficiency would plausibly produce — an
Interlanguage Language Model (ILM). Learner-conditioning prefixes (L1, CEFR,
error profile) turn a vanilla GLM into an ILM. Target venue: EMNLP 2026.

## Context

- LaTeX paper sketch: `main.tex` + `sections/` + ACL/EMNLP style files.
- README: project thesis, three-part narrative (diagnosis → proposal → eval),
  source-project map, structure overview.
- Codebase: `codebase/` — `src/`, `scripts/`, `configs/`, `tests/`, `Justfile`,
  `pyproject.toml`, `samples/`, `artifacts/`.
- Experiment variants: `experiments-ideas/01–10`, README index. Headline run
  is `01-glm-learnercond-multitoken-contextII-efcamdat.md`. Variants 03–05 are
  NWP/MLM diagnosis baselines.
- Reference paper PDF: `2103.10360v2.pdf` (Du et al., GLM).
- Cross-corpus eval: EFCAMDAT in-domain; CELVA-SP, KUPA-KEYS, andrew100k transfer.

## Ideas

## Milestones

## Features

## Todos

- [6b3a25c9] Build cross-corpus transfer eval EFCAMDAT to CELVA-SP/KUPA-KEYS/andrew100k (added: 2026-04-27)
- [5d1e8f74] Set up BERT MLM baseline (variant 05) for diagnosis section (added: 2026-04-27)
- [4c9b30a6] Set up NWP prompt-fillblank baseline (variant 03) for diagnosis section (added: 2026-04-27)
- [8a2f7e1d] Implement GLM autoregressive blank-infilling objective for multi-token spans (added: 2026-04-27)

## Doing

## Done
