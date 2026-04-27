---
slug: gen-gec-errant
path: /home/b/p/research-sketches/automatic-generation-correction-errortagging-v2
github: https://github.com/berstearns/public-automatic-generation-correction-errortagging-v2
gdrive_deploy: i:/_rk/automatic-generation-correction-errortagging-v2/deploy
gdrive_resources: i:/_rk/automatic-generation-correction-errortagging-v2/resources
objective: Pipeline for text generation + GEC + ERRANT tagging on learner text, used as scaffolding for cross-model experiments
status: active
created: 2026-04-27
---

# gen-gec-errant

## Objective

Five-stage pipeline (`data_loader → generation → gec → annotation → analysis`)
that takes learner-text inputs, generates continuations from a target LM,
runs grammatical error correction, ERRANT-annotates the corrections, and
produces error-profile statistics. Used as a measurement substrate for
cross-model experiments comparing GPT-2, Pythia, and SmolLM2 variants —
both fine-tuned and zero-shot — on EFCAMDAT, CELVA-SP, and KUPA-KEYS.

## Context

- Package: `src/gen_gec_errant/` — one subpackage per pipeline stage plus
  `pipeline/` orchestrator and `preprocessing/` for EFCAMDAT.
- README: `README.md` (CLI usage, stage table, model list).
- Research description: `RESEARCH-DESCRIPTION.md`.
- Configs: `configs/` — one folder per stage plus full-pipeline configs
  (`pipeline/`, `full-celva/`, `efcamdat-{train,test,remainder}/`, `kupa-keys/`).
- Docs: `docs/experiment-plan.md`, `docs/pipeline-order.md`, `docs/commands.md`.
- Reproducibility runs: `reproducibility/paper-reproducibility-ft-{gpt2-{small,medium,large},pythia-{70m,160m,410m,1b,1.4b},smollm2-{135m,360m,1.7b}}/`
  + `paper-reproducibility-gpt2-native-zero-shot/`.
- VastAI runner notes: `reproducibility/VASTAI-REPRODUCIBILITY-INSTRUCTIONS.md`.
- Sample outputs: `outputs/2-sample-celva-ft-{gpt2-small,gpt2-medium,pythia-410m}/`.

## Ideas

## Milestones

## Features

## Todos

- [5f71bc43] Make eval scripts accept models for online prediction or offline JSONL — same harness, two input modes (added: 2026-04-27)
- [4e60ab32] Run eval scripts with prediction files from no-training benchmark models — gpt2-native-zero-shot first (added: 2026-04-27)
- [3d5fa021] Adapt prediction scripts to emit JSONL compatible with every eval script — one schema across all reproducibility variants (added: 2026-04-27)

## Doing

## Done

- [2c4e8f10] Set up standalone eval scripts, one per evaluation table in the paper — error-rate, ERRANT category, perplexity (added: 2026-04-27) (picked: 2026-04-27) (done: 2026-04-27)
- [9a1c3d5b] Assign GitHub repo to project — create remote, push initial commit, link in frontmatter (added: 2026-04-27) (picked: 2026-04-27) (done: 2026-04-27)
