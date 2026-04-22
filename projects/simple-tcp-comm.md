---
slug: simple-tcp-comm
path: /home/b/simple-tcp-comm
objective: Minimal TCP collector/worker/archive pipeline for streaming events end-to-end
status: active
created: 2026-04-22
---

# simple-tcp-comm

## Objective

Python TCP server + client + worker + archive_receiver forming a tiny event pipeline.
Used as a sandbox for deploy patterns, schema evolution, and timeline debugging.

## Context

- Main entrypoints: `server.py`, `client.py`, `collector.py`, `worker.py`.
- Deploy notes: `deploy_e2e_local.md`, `deploy_instructions.md`.
- Schema: `archive_schema.sql`.

## Ideas

- [7c1e4a02] Replace ad-hoc env files with a single .env.{profile} loader (added: 2026-04-22)

## Milestones

- [b2d9f41a] End-to-end local deploy reproducible from a clean checkout (added: 2026-04-22)

## Features

- [4e8a1c63] Heartbeat frame between client and server for dead-peer detection — drop stale workers after N missed beats (added: 2026-04-22)

## Todos

- [a3f2c1b9] Add reconnect backoff to client — client should back off on ECONNREFUSED instead of tight-looping (added: 2026-04-22)

## Doing

## Done
