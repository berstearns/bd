---
slug: glms-for-ilms
path: /home/b/p/writings/sketches/glms-for-ilms-in-sla
github: https://github.com/berstearns/public-glms-for-ilms-in-sla
gdrive_deploy: i:/_rk/glms-for-ilms-in-sla/deploy
gdrive_resources: i:/_rk/glms-for-ilms-in-sla/resources
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

## Doing

- [0a3cbc06] Run eval scripts with prediction files from no-training benchmark models — NWP and BERT-MLM first (added: 2026-04-27) (picked: 2026-04-27)

## Done

- [1b4dcd07] Make eval scripts accept models for online prediction or offline JSONL — same harness, two input modes (added: 2026-04-27) (picked: 2026-04-27) (done: 2026-04-27)
- [f92bab05] Adapt prediction scripts to emit JSONL compatible with every eval script — one schema across GLM/NWP/BERT (added: 2026-04-27) (picked: 2026-04-27) (done: 2026-04-27)
- [e81a9904] Set up standalone eval scripts, one per evaluation table in the paper — diagnosis, learner-plausibility, transfer (added: 2026-04-27) (picked: 2026-04-27) (done: 2026-04-27)
- [8f0b2c4a] Assign GitHub repo to project — create remote, push initial commit, link in frontmatter (added: 2026-04-27) (picked: 2026-04-27) (done: 2026-04-27)
