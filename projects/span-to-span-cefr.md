---
slug: span-to-span-cefr
path: /home/b/p/cefr-classification/span-to-span-features-cefr-prediction
alignment_lib_path: /home/b/p/research-sketches/ppl-align-reusable-span-to-span
objective: Span-to-span perplexity-alignment library + cross-corpus CEFR classifier built on those features
status: active
created: 2026-04-26
---

# span-to-span-cefr

## Objective

Two coupled repos forming one research line. Upstream: a reusable span-to-span
alignment pipeline that maps human tokens and LLM byte-BPE subtokens into
maximal connected components ("span groups"), enabling correct many-to-many
perplexity aggregation. Downstream: a modular cross-corpus CEFR proficiency
classifier that consumes those span-group features alongside TF-IDF and PPL
features, evaluated zero-shot and few-shot across EFCAMDAT, CELVA-SP, and KUPA-KEYS.

## Context

- Alignment library: `/home/b/p/research-sketches/ppl-align-reusable-span-to-span`
  - Design: `design-plan.md` (many-to-many span-group reform of `align_hf_offsets()`).
  - Notes: `docs/continuing-from-ppl-files.md`, `docs/term-perplexity-inverse-document-perplexity-proposal.md`.
  - Reproducibility: `paper-reproducibility-span-to-span-colab/`.
- CEFR classifier: `/home/b/p/cefr-classification/span-to-span-features-cefr-prediction`
  - Plan: `docs/execution-plan.md` (jebega-pattern modular pipeline).
  - Pipeline: `data -> features -> train -> eval -> report -> plot`.
  - Feature sets: `tfidf`, `ppl`, `span`. Classifiers: XGBoost, Logistic Regression.
  - Experiments: zero-shot cross-corpus, few-shot 90/10.
- Reference paper: AL_CEFR_LREC_2026 (perplexity-based features for CEFR).

## Ideas

## Milestones

## Features

## Todos

## Doing

## Done
